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

