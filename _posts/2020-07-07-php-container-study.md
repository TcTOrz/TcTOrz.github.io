---
layout: post
title: 深入理解Laravel核心
categories: [php, laravel]
description: 闲时再看一遍Laravel核心
keywords: Laravel核心
topmost: false
---

# 目录

* TOC
{:toc} 

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
$reflector = new reflectionClass(User::class);

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

