---
layout: wiki
title: Javascript-type
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## `Type`

### `castArray`

转化为数组。

```js

const castArray = val => (Array.isArray(val) ? val : [val]);
castArray('foo'); // ['foo']
castArray([1]); // [1]

```

### `cloneRegExp`

克隆正则表达式。

```js

const cloneRegExp = regExp => new RegExp(regExp.source, regExp.flags);
const regExp = /lorem ipsum/gi;
const regExp2 = cloneRegExp(regExp); // /lorem ipsum/gi

```

### `coalesce`

返回定义的第一个非空参数。

```js

const coalesce = (...args) => args.find(v => ![undefined, null].includes(v));
coalesce(null, undefined, '', NaN, 'Waldo'); // ''

```

### `coalesceFactory`

返回自定义的合并函数，该函数返回从提供的参数验证函数返回true的第一个参数。

```js

const coalesceFactory = valid => (...args) => args.find(valid);
const customCoalesce = coalesceFactory(_ => ![null, undefined, '', NaN].includes(_));
customCoalesce(undefined, null, NaN, '', 'Waldo'); // "Waldo"

```

### `getType`

```js

const getType = v => (v === undefined ? 'undefined' : v === null ? 'null' : v.constructor.name);
getType(new Set([1, 2, 3])); // 'Set'

```

### `is`

检查提供的值是否为指定的类型。

```js

const is = (type, val) => ![, null].includes(val) && val.constructor === type;
is(Array, [1]); // true
is(ArrayBuffer, new ArrayBuffer()); // true
is(Map, new Map()); // true
is(RegExp, /./g); // true
is(Set, new Set()); // true
is(WeakMap, new WeakMap()); // true
is(WeakSet, new WeakSet()); // true
is(String, ''); // true
is(String, new String('')); // true
is(Number, 1); // true
is(Number, new Number(1)); // true
is(Boolean, true); // true
is(Boolean, new Boolean(true)); // true

```

### `isArrayLike`

检查所提供的参数是否类似于数组（即可迭代）。

```js

const isArrayLike = obj => obj != null && typeof obj[Symbol.iterator] === 'function';
isArrayLike([1, 2, 3]); // true
isArrayLike(document.querySelectorAll('.className')); // true
isArrayLike('abc'); // true
isArrayLike(null); // false

```

### `isAsyncFunction`

检查给定参数是否为异步函数。

```js

const isAsyncFunction = val =>
    Object.prototype.toString.call(val) === '[object AsyncFunction]';
isAsyncFunction(function() {}); // false
isAsyncFunction(async function() {}); // true

```

### `isBoolean`

```js

const isBoolean = val => typeof val === 'boolean';
isBoolean(null); // false
isBoolean(false); // true

```

### `isEmpty`

如果是一个空对象，集合，没有可枚举的属性或任何不视为集合的类型，则返回true。

```js

const isEmpty = val => val == null || !(Object.keys(val) || val).length;
isEmpty([]); // true
isEmpty({}); // true
isEmpty(''); // true
isEmpty([1, 2]); // false
isEmpty({ a: 1, b: 2 }); // false
isEmpty('text'); // false
isEmpty(123); // true - type is not considered a collection
isEmpty(true); // true - type is not considered a collection

```

### `isFunction`

```js

const isFunction = val => typeof val === 'function';
isFunction('x'); // false
isFunction(x => x); // true

```

### `isGeneratorFunction`

检查给定的参数是否是生成器函数。

```js

const isGeneratorFunction = val =>
    Object.prototype.toString.call(val) === '[object GeneratorFunction]';
isGeneratorFunction(function() {}); // false
isGeneratorFunction(function*() {}); // true

```

### `isNil`

如果指定的值为null或未定义，则返回true，否则返回false。

```js

const isNil = val => val === undefined || val === null;
isNil(null); // true
isNil(undefined); // true
isNil(''); // false

```

### `isNull`

如果指定的值为null，则返回true，否则返回false。

```js

const isNull = val => val === null;
isNull(null); // true

```

### `isNumber`

```js

const isNumber = val => typeof val === 'number' && val === val;
isNumber(1); // true
isNumber('1'); // false
isNumber(NaN); // false

```

### `isObject`

```js

const isObject = obj => obj === Object(obj);
isObject([1, 2, 3, 4]); // true
isObject([]); // true
isObject(['Hello!']); // true
isObject({ a: 1 }); // true
isObject({}); // true
isObject(true); // false

```

### `isObjectLike`

```js

const isObjectLike = val => val !== null && typeof val === 'object';
isObjectLike({}); // true
isObjectLike([1, 2, 3]); // true
isObjectLike(x => x); // false
isObjectLike(null); // false

```

### `isPlainObject`

检查提供的值是否是由Object构造函数创建的对象。

```js

const isPlainObject = val => !!val && typeof val === 'object' && val.constructor === Object;
isPlainObject({ a: 1 }); // true
isPlainObject(new Map()); // false

```

### `isPrimitive`

返回一个布尔值，确定所传递的值是否为原始值。

```js

const isPrimitive = val => Object(val) !== val;
isPrimitive(null); // true
isPrimitive(undefined); // true
isPrimitive(50); // true
isPrimitive('Hello!'); // true
isPrimitive(false); // true
isPrimitive(Symbol()); // true
isPrimitive([]); // false
isPrimitive({}); // false

```

### `isPromiseLike`

```js

const isPromiseLike = obj =>
    obj !== null &&
    (typeof obj === 'object' || typeof obj === 'function') &&
    typeof obj.then === 'function';
isPromiseLike({
    then: function() {
        return '';
    }
}); // true
isPromiseLike(null); // false
isPromiseLike({}); // false


```

### `isString`

```js

const isString = val => typeof val === 'string';
isString('10'); // true

```

### `isSymbol`

```js

const isSymbol = val => typeof val === 'symbol';
isSymbol(Symbol('x')); // true

```

### `isUndefined`

```js

const isUndefined = val => val === undefined;
isUndefined(undefined); // true

```

### `isValidJSON`

```js

const isValidJSON = str => {
    try {
        JSON.parse(str);
        return true;
    } catch (e) {
        return false;
    }
};
isValidJSON('{"name":"Adam","age":20}'); // true
isValidJSON('{"name":"Adam",age:"20"}'); // false
isValidJSON(null); // true

```