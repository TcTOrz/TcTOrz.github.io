---
layout: wiki
title: Javascript-function
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## `Function`

### `ary`

```js

const ary = (fn, n) => (...args) => fn(...args.slice(0, n));
const firstTwoMax = ary(Math.max, 2);
[[2, 6, 'a'], [6, 4, 8], [10]].map(x => firstTwoMax(...x)); // [6, 6, 10]

```

### `attempt`

```js

const attempt = (fn, ...args) => {
  try {
    return fn(...args);
  } catch (e) {
    return e instanceof Error ? e : new Error(e);
  }
};
var elements = attempt(function(selector) {
  return document.querySelectorAll(selector);
}, '>_>');
if (elements instanceof Error) elements = []; // elements = []

```

### `binary`

```js

const binary = fn => (a, b) => fn(a, b);
// Array.prototype.map((cv, index) => Math.max(cv, index))
['2', '1', '0'].map(binary(Math.max)); // [2, 1, 2]

```

### `bind`

```js

const bind = (fn, context, ...boundArgs) => (...args) => fn.apply(context, [...boundArgs, ...args]);
function greet(greeting, punctuation) {
    return greeting + ' ' + this.user + punctuation;
}
const freddy = { user: 'fred' };
const freddyBound = bind(greet, freddy);
console.log(freddyBound('hi', '!')); // 'hi fred!'

```

### `bindKey`

```js

const bindKey = (context, fn, ...boundArgs) => (...args) => 
    context[fn].apply(context, [...boundArgs, ...args]);
const freddy = {
    user: 'fred',
    greet: function(greeting, punctuation) {
        return greeting + ' ' + this.user + punctuation;
    }
};
const freddyBound = bindKey(freddy, 'greet');
console.log(freddyBound('hi', '!')); // 'hi fred!'

```

### `both`

```js

const both = (f, g) => (...args) => f(...args) && g(...args);
const isEven = num => num % 2 === 0;
const isPositive = num => num > 0;
const isPositiveEven = both(isEven, isPositive);
isPositiveEven(4); // true
isPositiveEven(-2); // false

```

### `call`

[Promise.resolve()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve)
[偏函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

```js

const call = (key, ...args) => context => context[key](...args);
Promise.resolve([1, 2, 3])
    .then(call('map', x => 2 * x))
    .then(console.log); // [ 2, 4, 6 ]
/* 
    const promise1 = Promise.resolve([123,124]);
    promise1.then(
        context => context['map'](x=>2*x)
    ).then(console.log);
    // Array [246, 248]
 */

 // 偏函数 call第一个参数'map'
const map = call.bind(null, 'map');
Promise.resolve([1, 2, 3])
    .then(map(x => 2 * x))
    .then(console.log); // [ 2, 4, 6 ]
// or
const map = call.bind(null, 'map', x => 2 * x);
Promise.resolve([1, 2, 3])
    .then(map())
    .then(console.log); // [ 2, 4, 6 ]
// or
const map = call.bind(null);
Promise.resolve([1, 2, 3])
    .then(map('map', x => 2 * x))
    .then(console.log); // [ 2, 4, 6 ]

```

### `chainAsync`

```js

const chainAsync = fns => {
    let curr = 0;
    const last = fns[fns.length - 1];
    const next = () => {
        const fn = fns[curr++];
        fn === last ? fn() : fn(next);
    };
    next();
};
chainAsync([
    next => {
        console.log('0 seconds');
        setTimeout(next, 1000);
    },
    next => {
        console.log('1 second');
        setTimeout(next, 1000);
    },
    () => {
        console.log('2 second');
    }
]);

```

### `checkProp`

```js

const checkProp = (predicate, prop) => obj => !!predicate(obj[prop]);

const lengthIs4 = checkProp(l => l === 4, 'length');
lengthIs4([]); // false
lengthIs4([1, 2, 3, 4]); // true
lengthIs4(new Set([1, 2, 3, 4])); // false (Set uses Size, not length)

const session = { user: {} };
const validUserSession = checkProp(u => u.active && !u.disabled, 'user');

validUserSession(session); // false

session.user.active = true;
validUserSession(session); // true

const noLength = checkProp(l => l === undefined, 'length');
noLength([]); // false
noLength({}); // true
noLength(new Set()); // true

```

### `collectInto`

[Promise.all()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

```js

var p1 = Promise.resolve(3);
var p2 = 1337;
var p3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 'foo');
}); 
Promise.all([p1, p2, p3]).then(values => { 
  console.log(values); // [3, 1337, "foo"] 
});

```

```js

const collectInto = fn => (...args) => fn(args);

const Pall = collectInto(Promise.all.bind(Promise));
let p1 = Promise.resolve(1);
let p2 = Promise.resolve(2);
let p3 = new Promise(resolve => setTimeout(resolve, 2000, 3));
Pall(p1, p2, p3).then(console.log); // [1, 2, 3] (after about 2 seconds)

```

### `complement`

```js

const complement = fn => (...args) => !fn(...args);
const isEven = num => num % 2 === 0;
const isOdd = complement(isEven);
isOdd(2); // false
isOdd(3); // true

```

### `compose`

```js

const compose = (...fns) => fns.reduce((f, g) => (...args) => f(g(...args)));
const add5 = x => x + 5;
const multiply = (x, y) => x * y;
const multiplyAndAdd5 = compose(
  add5,
  multiply
);
multiplyAndAdd5(5, 2); // 15

```

### `converge`

求平均值。

```js

const converge = (converger, fns) => (...args) => converger(...fns.map(fn => fn.apply(null, args)));
const average = converge((a, b) => a / b, [
  arr => arr.reduce((a, v) => a + v, 0),
  arr => arr.length
]);
average([1, 2, 3, 4, 5, 6, 7]); // 4

```

### `curry`

函数柯里化

```js

const curry = (fn, arity = fn.length, ...args) =>
    arity <= args.length ? fn(...args) : curry.bind(null, fn, arity, ...args);
curry(Math.pow)(2)(10); // 1024
curry(Math.min, 3)(10)(50)(2); // 2

```

### `debounce`

[函数的防抖和节流](https://ifwechat.com//2020/06/16/javascript-interview/#%E5%87%BD%E6%95%B0%E7%9A%84%E9%98%B2%E6%8A%96%E5%92%8C%E8%8A%82%E6%B5%81)有更详细的说明

防抖函数

```js

const debounce = (fn, ms = 0) => {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), ms);
  };
};
window.addEventListener(
  'resize',
  debounce(() => {
    console.log(window.innerWidth);
    console.log(window.innerHeight);
  }, 250)
); // Will log the window dimensions at most every 250ms

```

### `defer`

```js

const defer = (fn, ...args) => setTimeout(fn, 1, ...args);

// Example A:
defer(console.log, 'a'), console.log('b'); // logs 'b' then 'a'

// Example B:
document.querySelector('#someElement').innerHTML = 'Hello';
longRunningFunction(); // Browser will not update the HTML until this has finished
defer(longRunningFunction); // Browser will update the HTML then run the function

```

### `delay`

```js

const delay = (fn, wait, ...args) => setTimeout(fn, wait, ...args);
delay(
    function(text) {
        console.log(text);
    },
    1000,
    'later'
); // Logs 'later' after one second.

```

### `either`

```js

const either = (f, g) => (...args) => f(...args) || g(...args);
const isEven = num => num % 2 === 0;
const isPositive = num => num > 0;
const isPositiveOrEven = either(isPositive, isEven);
isPositiveOrEven(4); // true
isPositiveOrEven(3); // true

```

### `flip`

```js

const flip = fn => (first, ...rest) => fn(...rest, first);
let a = { name: 'John Smith' };
let b = {};
const mergeFrom = flip(Object.assign);
let mergePerson = mergeFrom.bind(null, a);
mergePerson(b); // == b
b = {};
Object.assign(b, a); // == b

```

### `functionName`

```js

const functionName = fn => (console.debug(fn.name), fn);
functionName(Math.max); // max (logged in debug channel of console)

```

### `在JavaScript中实现单例？`

[Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

```js

const singletonify = (className) => {
    return new Proxy(className.prototype.constructor, {
        instance: null,
        construct: (target, argumentsList) => {
            if (!this.instance)
                this.instance = new target(...argumentsList);
            return this.instance;
        }
    });
}

class MyClass {
  constructor(msg) {
    this.msg = msg;
  }

  printMsg() {
    console.log(this.msg);
  }
}

MySingletonClass = singletonify(MyClass);
const myObj = new MySingletonClass('first');
myObj.printMsg();           // 'first'
const myObj2 = new MySingletonClass('second');
myObj2.printMsg();           // 'first'

```

### `hz`

返回每秒执行一个函数的次数。 hz是赫兹的单位，频率的单位定义为每秒一个周期。

```js

const hz = (fn, iterations = 100) => {
    const before = performance.now();
    for (let i = 0; i < iterations; i++) fn();
    return (1000 * iterations) / (performance.now() - before);
};
// 10,000 element array
const numbers = Array(10000)
    .fill()
    .map((_, i) => i);

// Test functions with the same goal: sum up the elements in the array
const sumReduce = () => numbers.reduce((acc, n) => acc + n, 0);
const sumForLoop = () => {
    let sum = 0;
    for (let i = 0; i < numbers.length; i++) sum += numbers[i];
    return sum;
};

Math.round(hz(sumReduce)); // 572
Math.round(hz(sumForLoop)); // 4784

```

### `memoize`

返回缓存函数。

```js

const memoize = fn => {
    const cache = new Map();
    const cached = function(val) {
        return cache.has(val) ? cache.get(val) : cache.set(val, fn.call(this, val)) && cache.get(val);
    };
    cached.cache = cache;
    return cached;
};

const fn = x => {
    console.log('第一次载入');
    return x * 2;
};
const fnCached = memoize(fn);
fnCached(2); // takes a long time // 第一次载入 4
fnCached(2); // returns virtually instantly since it's now cached // 4
console.log(fnCached.cache); // The cached fn map

```

### `mostPerformant`

返回执行最快的函数数组中的函数索引。

```js

const mostPerformant = (fns, iterations = 10000) => {
    const times = fns.map(fn => {
        const before = performance.now();
        for (let i = 0; i < iterations; i++) fn();
        return performance.now() - before;
    });
    return times.indexOf(Math.min(...times));
};
mostPerformant([
    () => {
        // Loops through the entire array before returning `false`
        [1, 2, 3, 4, 5, 6, 7, 8, 9, '10'].every(el => typeof el === 'number');
    },
    () => {
        // Only needs to reach index `1` before returning false
        [1, '2', 3, 4, 5, 6, 7, 8, 9, 10].every(el => typeof el === 'number');
    }
]); // 1

```

### `negate`

```js

const negate = func => (...args) => !func(...args);
// or
const negate = func => (v) => !func(v);
[1, 2, 3, 4, 5, 6].filter(negate(n => n % 2 === 0)); // [ 1, 3, 5 ]
// or
[1, 2, 3, 4, 5, 6].filter(n => n % 2 !== 0);

```

### `nthArg`

```js

const nthArg = n => (...args) => args.slice(n)[0];
const third = nthArg(2);
third(1, 2, 3); // 3
third(1, 2); // undefined
const last = nthArg(-1);
last(1, 2, 3, 4, 5); // 5

```

### `once`

[相似情形](https://ifwechat.com//wiki/javascript-browser/#listenonce)

```js

const once = fn => {
    let called = false;
    return function(...args) {
        if (called) return;
        called = true;
        return fn.apply(this, args);
    };
};
const startApp = function(event) {
  console.log(this, event); // document.body, MouseEvent
};
document.body.addEventListener('click', once(startApp)); // only runs `startApp` once upon click

```

### `over`

创建一个函数，该函数使用接收到的参数调用提供的每个函数并返回结果。

```js

const over = (...fns) => (...args) => fns.map(fn => fn.apply(null, args));
const minMax = over(Math.min, Math.max);
minMax(1, 2, 3, 4, 5); // [1,5]

```

### `overArgs`

```js

const overArgs = (fn, transforms) => (...args) => fn(...args.map((val, i) => transforms[i](val)));
const square = n => n * n;
const double = n => n * 2;
const fn = overArgs((x, y) => [x, y], [square, double]);
fn(9, 3); // [81, 6]

```

### `partial` & `partialRight`

```js

const partial = (fn, ...partials) => (...args) => fn(...partials, ...args);
const greet = (greeting, name) => greeting + ' ' + name + '!';
const greetHello = partial(greet, 'Hello');
greetHello('John'); // 'Hello John!'

```

```js

const partialRight = (fn, ...partials) => (...args) => fn(...args, ...partials);
const greet = (greeting, name) => greeting + ' ' + name + '!';
const greetJohn = partialRight(greet, 'John');
greetJohn('Hello'); // 'Hello John!'

```

### `pipeAsyncFunctions`

对异步函数执行从左到右的功能组合。

```js

const pipeAsyncFunctions = (...fns) => arg => fns.reduce((p, f) => p.then(f), Promise.resolve(arg));
const sum = pipeAsyncFunctions(
    x => x + 1,
    x => new Promise(resolve => setTimeout(() => resolve(x + 2), 1000)),
    x => x + 3,
    async x => (await x) + 4
);
(async() => {
    console.log(await sum(5)); // 15 (after one second)
})();

```

### `pipeFunctions`

```js

const pipeFunctions = (...fns) => fns.reduce((f, g) => (...args) => g(f(...args)));
// or
const pipeFunctions = (...fns) => (...args) => fns.reduce((f, g) => g(f(...args)));

const add5 = x => x + 5;
const multiply = (x, y) => x * y;
const multiplyAndAdd5 = pipeFunctions(multiply, add5);
multiplyAndAdd5(5, 2); // 15

```

### `promisify`

转换异步函数,返回promise。

```js

const promisify = func => (...args) =>
    new Promise((resolve, reject) =>
        func(...args, res => resolve(res))
        // or
        func(...args, (err, result) => (err ? reject(err) : resolve(result)))
    );
const delay = promisify((d, cb) => setTimeout(cb, d));
delay(2000).then(() => console.log('Hi!')); // // Promise resolves after 2s

```

### `rearg`

创建一个函数，该函数调用提供的函数，并根据指定的索引排列其参数。

```js

const rearg = (fn, indexes) => (...args) => fn(...indexes.map(i => args[i]));
var rearged = rearg(
    function(a, b, c) {
        return [a, b, c];
    },
    [2, 0, 1]
);
rearged('b', 'c', 'a'); // ['a', 'b', 'c']

```

### `sleep`

延迟异步功能的执行。

```js

const sleep = ms => new Promise(resolve => setTimeout(resolve, ms));
async function sleepyWork() {
  console.log("I'm going to sleep for 1 second.");
  await sleep(1000);
  console.log('I woke up after 1 second.');
}

```

### `spreadOver`

```js

const spreadOver = fn => args => fn(...args);
const arrayMax = spreadOver(Math.max);
arrayMax([1, 2, 3]); // 3

```

### `throttle`

[函数的防抖和节流](https://ifwechat.com//2020/06/16/javascript-interview/#%E5%87%BD%E6%95%B0%E7%9A%84%E9%98%B2%E6%8A%96%E5%92%8C%E8%8A%82%E6%B5%81)有更详细的说明

函数节流

```js

const throttle = (fn, wait) => {
    let inThrottle, lastFn, lastTime;
    return function() {
        const context = this,
        args = arguments;
        if (!inThrottle) {
            fn.apply(context, args);
            lastTime = Date.now();
            inThrottle = true;
        } else {
            clearTimeout(lastFn);
            lastFn = setTimeout(function() {
                if (Date.now() - lastTime >= wait) {
                    fn.apply(context, args);
                    lastTime = Date.now();
                }
            }, Math.max(wait - (Date.now() - lastTime), 0));
        }
    };
};
window.addEventListener(
    'resize',
    throttle(function(evt) {
        console.log(window.innerWidth);
        console.log(window.innerHeight);
    }, 250)
); // Will log the window dimensions at most every 250ms

```

### `times`

遍历回调n次

```js

const times = (n, fn, context = undefined) => {
    let i = 0;
    while (fn.call(context, i) !== false && ++i < n) {}
};
var output = '';
times(5, i => (output += i));
console.log(output); // 01234

```

### `timeTaken`

测量函数执行所需的时间。

```js

const timeTaken = callback => {
    console.time('timeTaken');
    const r = callback();
    console.timeEnd('timeTaken');
    return r;
};
timeTaken(() => Math.pow(2, 10)); // 1024, (logged): timeTaken: 0.02099609375ms

```

### `unary`

创建一个函数，该函数最多接受一个参数，而忽略任何其他参数。

```js

const unary = fn => val => fn(val);
['6', '8', '10'].map(unary(parseInt)); // [6, 8, 10]

```

### `uncurry`

柯里化直到n深度的函数。


```js

const uncurry = (fn, n = 1) => (...args) => {
    const next = acc => args => args.reduce((x, y) => x(y), acc);
    if (n > args.length) throw new RangeError('Arguments too few!');
    return next(fn)(args.slice(0, n));
};
const add = x => y => z => x + y + z;
const uncurriedAdd = uncurry(add, 3);
uncurriedAdd(1, 2, 3); // 6

```


### 了解`JavaScript`中的`this`关键字

在`Javascript`中，`this`关键字指代当前正在执行代码的对象，具体如下：

- 默认情况下，`this`指全局对象
- 函数中，当不在严格模式下，`this`指全局对象
- 函数中，在严格模式下，`this`为`undefined`
- 箭头函数中，保留了封闭词法上下文`this`的值
- 在对象方法中，`this`指代调用该方法的对象
- 构造函数中，`this`绑定在正在构造而成的新对象中
- 事件处理中，`this`绑定到放置事件侦听的元素

```js

// 全局上下文中，this指代全局对象
console.log(this === window); //true

// 非严格模式，this指代全局对象
function f() {
    return this;
}
console.log(f() === window); // true

// 严格模式，this等于undefined 
'use strict';
function f() {
    return this;
}
console.log(f()); // undefined

// 在对象方法中，this指代调用该方法的对象
const obj = {
    f: function() {
        return this;
    }
};
console.log(obj.f()); // { obj }

// 构造函数中，this绑定在正在构造而成的新对象中
class C {
    constructor() {
        this.x = 10;
    }
}
const obj = new C();
console.log(obj.x); // 10

// 箭头函数中，保留了封闭词法上下文this的值
const f = () => this;
console.log(f() === window); // true

const obj = {
    foo: function() { // foo不是箭头函数, 所以this的值为调用他方法的对象
        const baz = () => this; // this===obj
        return baz();
    },
    bar: () => this
};
console.log(obj.foo()); // { foo, bar }
console.log(obj.bar() === window); // true

// 事件处理中，this绑定到放置事件侦听的元素
const el = document.getElementById('my-el');
el.addEventListener('click', function() {
    console.log(this === el); // true
});

```

绑定`this`

`Function.prototype.bind()` `Function.prototype.call()` `Function.prototype.apply()`

```js

function f() {
    return this.foo;
}

var x = f.bind({foo: 'hello'});
console.log(x()); // 'hello'

console.log(f.call({foo: 'hi'})); // 'hi'

```

### `unfold`

```js

const unfold = (fn, seed) => {
    let result = [],
        val = [null, seed];
    while ((val = fn(val[1]))) result.push(val[0]);
    return result;
};
var f = n => (n > 50 ? false : [-n, n + 10]);
unfold(f, 10); // [-10, -20, -30, -40, -50]

```

### `Using JavaScript generator functions for ranges`

```js

function* generateRange(end, start = 0, step = 1) {
    let x = start - step;
    while(x < end - step) yield x += step;
}
const gen5 = generateRange(5);
let x = gen5.next();
while (!x.done) {
    console.log(x.value);
    x = gen5.next();
} // Logs: 0, 1, 2, 3, 4

const iterableX = {
    [Symbol.iterator]: function* () {
        yield 1;
        yield 2;
    }
};
console.log([...iterableX]); // [1, 2]

const range = (end, start = 0, step = 1) => {
    function* generateRange() {
        let x = start - step;
        while(x < end - step) yield x += step;
    }
    return {
        [Symbol.iterator]: generateRange
    };
}
console.log([...range(7)]); // [0, 1, 2, 3, 4, 5, 6]
for (let i of range(8, 2, 2)) console.log(i); // Logs: 2, 4, 6

```

### What are JavaScript closures?

[什么是闭包（closure），为什么使用闭包？](https://ifwechat.com//2020/06/16/javascript-interview/#%E4%BB%80%E4%B9%88%E6%98%AF%E9%97%AD%E5%8C%85closure%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BD%BF%E7%94%A8%E9%97%AD%E5%8C%85)

### `when`

```js

const when = (pred, whenTrue) => x => (pred(x) ? whenTrue(x) : x);
const doubleEvenNumbers = when(x => x % 2 === 0, x => x * 2);
doubleEvenNumbers(2); // 4
doubleEvenNumbers(1); // 1

```

### 什么时候使用`Javascript`缓存技术？

- 加快执行缓慢，耗时的函数调用
- 多次调用同一函数
- 完全不同的情况下多次调用同一函数应避免使用

```js

const memoize = fn => new Proxy(fn, {
    cache: new Map(),
    apply (target, thisArg, argsList) {
        let cacheKey = argsList.toString();
        if(!this.cache.has(cacheKey))
        this.cache.set(cacheKey, target.apply(thisArg, argsList));
        return this.cache.get(cacheKey);
    }
});

const fibonacci = n => (n <= 1 ? 1 : fibonacci(n - 1) + fibonacci(n - 2));
const memoizedFibonacci = memoize(fibonacci);

for (let i = 0; i < 100; i ++)
    fibonacci(30);                      // ~5000ms
for (let i = 0; i < 100; i ++)
    memoizedFibonacci(30);              // ~50ms

```