---
layout: post
title: 深入理解Laravel核心
categories: [php, laravel]
description: 闲时再看一遍Laravel核心
keywords: Laravel核心
topmost: false
---

<!-- # 目录

* TOC
{:toc}  -->

### 依赖注入，控制反转概念

可以认为两者表达的是同一个意思，举个例子：用户登录时需要提供记录日志的功能，可以选择文件或者数据库登录。

```php

// 日志接口
interface Log
{
    public function write();
}

// 文件形式记录日志
class FileLog implements Log 
{
    public function write() {
        echo 'File log.' . "\n";
    }
}

// 数据库形式记录日志
class DatabaseLog implements Log
{
    public function write() {
        echo 'Database log.' . "\n";
    }
}

// 用户类
class User {
    protected $fileLog;

    public function __construct() {
        $this->fileLog = new FileLog();
    }

    public function login() {
        echo 'Login success...' . "\n";
        $this->fileLog->write();
    }
}

$user = new User();
$user->login();

```

上面的写法可以实现记录日志的功能，但是有个问题，当需要换成数据库记录日志时，就需要修改User类，因此代码耦合性没达到，也不符合程序开放封闭原则，那么，再看看下面的写法。

```php

class User
{
    protected $log;

    public function __construct(Log $log) {
        $this->log = $log;
    }

    public function login() {
        echo 'Login success...' . "\n";
        $this->log->write();
    }
}

$user = new User(new FileLog());
$user->login();

```

这样操作记录日志就不用修改User类了，著需要通过构造函数参数传递就可以实现，其实这就是“控制反转”(IOC)，类不需要修改，而改成外部传递。
不由自己内部new对象或者实例，通过构造函数，或者方法传入的都属于依赖注入(DI)。

### Laravel依赖注入

laravel很多对象实例通过方法参数定义就能直接传进来，调用的时候不需要我们手动传入。下面举一个例子。

```php

namespace App\Http\Controllers\Auth;
use App\Services\Auth\CaptchaService;
use App\Services\Auth\UserService;
use App\Http\Controllers\Controller;

class MyLoginController extends Controller
{
    protected $captchaService;
    protected $userService;
    // 注意，CaptchaService和UserService直接注入到构造函数中
    public function __construct(CaptchaService $captchaService, UserService $userService) {
        $this->captchaService = $captchaService;
        $this->userService = $userService;
    }
}

```

#### 反射的理解

现在已经明白了依赖注入的概念，laravel实现其实是用了反射的机制动态的创建类。
反射的概念其实可以理解成根据类名返回该类的任何信息，比如类中包含的方法，参数，变量等等。

```php

// 获取User的reflectionClass对象
$reflector = new ReflectionClass(User::class);

// 获取User的构造函数
$constructor = $reflector->getConstructor();

// 获取User构造函数的所有依赖参数
$dependencies = $constructor->getParamters();

// 创建User对象
$user = $reflector->newInstance();

// 创建User对象，需要传递参数时
$user = $reflector->newInstanceArgs($dependencies = []);

```

这个时候我们可以创建一个make方法，传入User，利用反射机制拿到User的构造函数，进而得到构造函数的参数对象。用递归的方式创建参数依赖。最后调用newInstanceArgs方法生成User实例。

```php

function make($concrete) {
    $reflector = new ReflectionClass($concrete);
    $constructor = $reflector->getConstructor();
    $dependencies = $constructor->getParameters();
    return $reflector->newInstanceArgs($dependencies);
}
$user = make('User');
$user->login();

```

代码实现

```php

class User
{
    protected $log;

    public function __construct(FileLog $log) {
        $this->log = $log;
    }

    public function login() {
        echo 'Login success...' . "\n";
        $this->log->write();
    }
}

function make($concrete) {
    $reflector = new ReflectionClass($concrete);
    $constructor = $reflector->getConstructor();
    if( is_null($constructor) ) {
        return $reflector->newInstance();
    }else {
        $dependencies = $constructor->getParameters();
        $instances = getDependencies($dependencies);
        return $reflector->newInstanceArgs($instances);
    }
}

function getDependencies($parameters) {
    $dependencies = [];
    foreach( $parameters as $parameter ) {
        $dependencies[] = make($parameter->getClass()->name);
    }
    return $dependencies;
}

$user = make('User');
$user->login();

```

### 如何实现Ioc容器和服务提供者是什么概念

上面已经实现了依赖注入，控制反转和反射，但是还没有完全达到解耦，如果项目中很多功能用到login，我们则在页面中反复写，突然某天换需求时，则所有login都要去替换。
我们可以借助容器，提前把log，user都绑定到IOC容器中。User的创建交给这个容器去做，比如：

### 具体代码实现

- IOC容器维护binding数组记录bind方法传入键值对，如：log=>FileLog, user=>User
- 在ioc->make('user')的时候，通过反射拿到User构造函数，拿到构造函数参数，发现参数是User的构造函数参数是log，然后再根据log得到FileLog
- 这时候我们只要通过反射机制创建$fileLog = new FileLog()
- 通过newInstanceArgs去创建new User($fileLog)

```php

// 实例化容器
$ioc = new Ioc();
$ioc->bind('log', 'FileLog');
$ioc->bind('user', 'User');
$user = $ioc->make('user');
$user->login();

```

这里的容器指Ioc容器，服务提供者是User。

```php

interface Log
{
    public function write();
}

class FileLog implements Log
{
    public function write() {
        echo 'File log.' . "\n";
    }
}

class DatabaseLog implements Log
{
    public function write() {
        echo 'Database log.' . "\n";
    }
}

class User
{
    protected $log;
    public function __construct(Log $log) {
        $this->log = $log;
    }
    public function login() {
        echo 'Login success...' . "\n";
        $this->log->write();
    }
}

class Ioc
{
    protected $binding = [];

    public function bind($abstract, $concrete) {
        // 这里为什么要返回一个closure呢？因为bind的时候还不需要创建User对象，所以采用closure等make的时候再创建FileLog;
        $this->binding[$abstract]['concrete'] = function($ioc) use ($concrete) {
            return $ioc->build($concrete);
        };
    }

    public function make($abstract) {
        // 根据key获取binding的值
        $concrete = $this->binding[$abstract]['concrete'];
        return $concrete($this);
    }

    public function build($concrete) {
        $reflector = new ReflectionClass($concrete);
        $constructor = $reflector->getConstructor();
        if(is_null($constructor)) {
            return $reflector->newInstance();
        }else {
            $dependencies = $constructor->getParameters();
            $instances = $this->getDependencies($dependencies);
            return $reflector->newInstanceArgs($instances);
        }
    }

    // 获取参数的依赖
    protected function getDependencies($paramters) {
        $dependencies = [];
        foreach ($paramters as $paramter) {
            $dependencies[] = $this->make($paramter->getClass()->name);
        }
        return $dependencies;
    }
}

$ioc = new Ioc();
$ioc->bind('Log', 'FileLog');
$ioc->bind('user', 'User');
$user = $ioc->make('user');
$user->login();

```

至此，Ioc已经实现了。

### laravel中的服务容器和服务提供者是什么样子？

在laravel config/app.php文件中providers,这个数组定义的都是已经写好的服务提供者。

```php

'providers' => [
    Illuminate\Auth\AuthServiceProvider::class,
    Illuminate\Broadcasting\BroadcastServiceProvider::class,
    Illuminate\Bus\BusServiceProvider::class,
    Illuminate\Cache\CacheServiceProvider::class,
    // ...
],

// 随便打开一个，这个服务提供者通过调用register方法注册到ioc容器中，其中的app就是ioc容器。singleton可以理解成上面例子中的bind方法。只不过这里的singleton指的是单例模式。
class AuthServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->registerAuthenticator();
    }
    protected function registerAuthenticator()
    {
        $this->app->singleton('auth', function ($app) {
            $app['auth.loaded'] = true;

            return new AuthManager($app);
        });

        $this->app->singleton('auth.driver', function ($app) {
            return $app['auth']->guard();
        });
    }
}


```

### Contracts契约之面向接口编程

#### 什么是契约

契约就是所谓的面向接口的编程。

不使用接口的例子

```php

class FileLog
{
    public function write() {
        echo 'File log write...' . "\n";
    }
}

class DatabaseLog
{
    public function write() {
        echo 'Database log write...' . "\n";
    }
}

class User
{
    protected $log;
    public function __construct(FileLog $log) {
        $this->log = $log;
    }
    public function login() {
        echo "login success...\n";
        $this->log->write();
    }
}

$user = new User(new FileLog());
$user->login();

```

看上去没有任何问题，但是如果日后需求变更，需要换database记录日志时，就得改User类，没有解耦。所以才有了面向接口的编程，也就是laravel中的契约，代码修改如下

```php

interface Log
{
    public function write();
}

class FileLog implements Log
{
    public function write() {
        echo 'File log write...' . "\n";
    }
}

class DatabaseLog implements Log
{
    public function write() {
        echo 'Database log write...' . "\n";
    }
}

class User 
{
    protected $log;
    public function __construct(Log $log) {
        $this->log = $log;
    }
    public function login() {
        echo "login success...\n";
        $this->log->write();
    }
}

$user = new User(new FileLog());
$user->login();

```

#### laravel中的契约是什么样子的？

例如Cache，定义的契约在 Illuminate\Contracts\Cache\Repository 文件中

### Facade外观模式背后的实现原理

#### 外观模式理解

上面写过需要$ioc->make('user')才能拿到User的实例，再去用$user->login()。可以进一步简化一下，比如:

```php

UserFacade::login();

```

#### Facade工作原理

- Facade 核心原理就是在UserFacade提前注入Ioc容器
- 定义一个服务器提供者的外观类，在该类定义一个类的变量，和Ioc绑定key一样
- 通过静态魔术方法__callStatic可以得到当前想要调用的login
- 使用static::$ioc->make('user')

#### 具体实现

- 我们定义一个User的外观类UserFacade

```php

class UserFacade
{
    protected static $ioc;
    
    public static function setFacadeIoc($ioc) {
        static::$ioc = $ioc;
    } 

    protected static function getFacadeAccessor() {
        return 'user';
    }

    // 魔术方法，当静态方法被调用时会被触发
    public function __callStatic($method, $args) {
        $instance = static::$ioc->make(static::getFacadeAccessor());
        return $instance->$method($args);
    }
}

$ioc = new Ioc();
$ioc->bind('log', 'FileLog');
$ioc->bind('user', 'User');
UserFacade::setFacadeIoc($ioc);
UserFacade::login();

```

### laravel中间件

laravel中间件提供了一种方便的机制来过滤进入应用的HTTP请求。例如：laravel内置了一个中间件来验证用户的身份认证，如果没有通过身份认证，中间件会将用户重定向到登陆界面。如果用户被认证，则中间件允许该请求进入该应用。

当然，除了身份认证外，还可以编写另外的中间件来执行各种任务。例如：CORS中间件负责为所有离开应用的响应添加适合的头部信息，日志中间件可以记录所有传入应用的请求。

laravel自带了一些中间件，在app/Http/Middleware目录。

简单来说就是请求在不去修改自身逻辑，通过中间件来扩展处理一些功能。

#### 实现一个中间件

```php

interface Middleware
{
    public static function handle(Closure $next);
}

class VertifyCsrfToken implements Middleware
{
    public static function handle(Closure $next) {
        echo "验证CSRF token...\n";
        $next();
    }
}

class VertifyAuth implements Middleware
{
    public static function handle(Closure $next) {
        echo "验证是否登录...\n";
        $next();
    }
}

class SetCookie implements Middleware
{
    public static function handle(Closure $next) {
        $next();
        echo "设置Cookie信息...\n";
    }
}

function call_middleware() {
    SetCookie::handle(function(){
        VertifyAuth::handle(function(){
            VertifyCsrfToken::handle(function(){
                echo "执行的程序\n";
            });
        });
    });
}

call_middleware();

```

原理很简单，暂不赘述了。

仔细看上述代码，发现这样的代码肯定不好维护与扩展的，需要修改于一下。这其中用到了两个函数

- call_user_func()
- array_reduce()

```php

interface Middleware
{
    public static function handle(Closure $next);
}

class VertifyCsrfToken implements Middleware
{
    public static function handle(Closure $next) {
        echo "验证CSRF token...\n";
        $next();
    }
}

class VertifyAuth implements Middleware
{
    public static function handle(Closure $next) {
        echo "验证是否登录...\n";
        $next();
    }
}

class SetCookie implements Middleware
{
    public static function handle(Closure $next) {
        $next();
        echo "设置Cookie信息...\n";
    }
}

$handle = function() {
    echo "执行的程序\n";
};

$pipe_arr = [
    'VertifyCsrfToken',
    'VertifyAuth',
    'SetCookie'
];

$callback = array_reduce($pipe_arr, function($stack, $pipe) {
    return function() use($stack, $pipe) {
        return $pipe::handle($stack);
    };
}, $handle);

call_user_func($callback);

```

laravel中的middleware也是通过call_user_func和array_reduce来实现的。

### laravel的生命周期

laravel入口文件public/index.php

```php

/**
 * Laravel - A PHP Framework For Web Artisans
 * 定义laravel开始请求时间
 * @package  Laravel
 * @author   Taylor Otwell <taylor@laravel.com>
 */

define('LARAVEL_START', microtime(true));

/*
|--------------------------------------------------------------------------
| Register The Auto Loader
|--------------------------------------------------------------------------
| Composer提供的自动加载机制
|
| Composer provides a convenient, automatically generated class loader for
| our application. We just need to utilize it! We'll simply require it
| into the script here so that we don't have to worry about manual
| loading any of our classes later on. It feels great to relax.
|
*/

require __DIR__.'/../vendor/autoload.php';

/*
|--------------------------------------------------------------------------
| Turn On The Lights
|--------------------------------------------------------------------------
| 
| 引入一个Ioc容器
|
| We need to illuminate PHP development, so let us turn on the lights.
| This bootstraps the framework and gets it ready for use, then it
| will load up this application so that we can run it and send
| the responses back to the browser and delight our users.
|
*/

$app = require_once __DIR__.'/../bootstrap/app.php';

/*
|--------------------------------------------------------------------------
| Run The Application
|--------------------------------------------------------------------------
|
| Once we have the application, we can handle the incoming request
| through the kernel, and send the associated response back to
| the client's browser allowing them to enjoy the creative
| and wonderful application we have prepared for them.
|
*/
// 创建一个Kernel::class服务提供者
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);

// 获取一个request请求，返回http响应
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);

// 返回的响应发送给浏览器
$response->send();

// 执行耗时请求/后续操作
$kernel->terminate($request, $response);


```

那middleware，服务提供者都是在那些文件里注册运行呢？

在app/Http/Kernel::class这个文件。

### laravel事件之观察者模式

#### 观察者模式

laravel提供了一个简单的观察者实现，能够订阅和监听应用中发生的各种事件。

什么是观察者？

观察者模式（Observer），当一个对象的状态发生改变时，依赖他的对象会全部收到通知，并自动更新。

场景: 一个事件发生后，要执行一连串的更新操作。传统的编程方式，就是在事件的代码之后直接加入逻辑处理，当更新的逻辑增多之后，代码会变得难以维护。这种方式是耦合的、侵入式的，增加新的逻辑需要改变事件主题的代码。

观察者模式实现了低耦合、非侵入式的通知与更新机制。

代码实现

```php

/**
 * 观察者接口类
 * Interface Observer
 */
interface Observer
{
    public function update($event_info = null);
}

/**
 * 观察者1
 */
class Observer1 implements Observer
{
    public function update($event_info = null) {
        echo "观察者1 收到通知 执行完毕！ \n";
    }
}

/**
 * 观察者2
 */
class Observer2 implements Observer
{
    public function update($event_info = null) {
        echo "观察者2 收到通知 执行完毕！ \n";
    }
}

/**
 * 事件
 */
class Event
{
    // 用于观察者注册的数组
    protected $observers = [];

    // 增加观察者
    public function add(Observer $observer) {
        $this->observers[] = $observer;
    }

    // 事件通知
    public function notify() {
        foreach($this->observers as $observer) {
            $observer->update();
        }
    }

    // 触发事件
    public function trigger() {
        // 通知观察者
        $this->notify();
    }
}

// 服务类  实现事件的创建和触发，不需要关心具体多少调用方需要监听
class DemoService
{
    public function demo() {
        // 创建一个事件
        $event = new Event();

        // ...

        // 执行事件，通知旁观者
        $event->trigger();
    }
}

// 调用方， 调用方仅仅需要知道服务类创建了哪些事件
class DoService
{
    public function do() {
        // 为事件增加旁观者
        $event->add(new Observer1());
        $event->add(new Observer2());
    }
}

```

#### laravel中使用事件

```php

php artisan make:event // 创建事件

php artisan make:listener // 创建事件监听者，可以为多个

```

laravel事件类保存在app/Events中，而这些事件的监听则被保存在app/Listener下

```php

// 在程序需要触发事件的地方通过event()触发事件，例如
event(new SendMailEvent());

```

### 致谢
作者：cxp1539
[链接](https://learnku.com/docs/laravel-core-concept)