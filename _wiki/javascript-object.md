---
layout: wiki
title: Javascript-object
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## `Object`

### `bindAll`

将对象的方法绑定到对象本身，从而覆盖现有方法。

```js

const bindAll = (obj, ...fns) =>
    fns.forEach(
        fn => (
            (f = obj[fn]),
            (obj[fn] = function() {
                return f.apply(obj);
            })
        )
    );
var view = {
    label: 'docs',
    click: function() {
        console.log('clicked ' + this.label);
    }
};
view.click(); // clicked docs
bindAll(view, 'click');
jQuery(element).on('click', view.click); // Logs 'clicked docs' when clicked.

```

### 代码剖析（循环、`Array.prototype.reduce`、方法链）

循环

```js

// O（N）复杂度，每个元素将仅迭代一次。
const files = ['foo.txt', '.bar', '   ', 'baz.foo'];
let filePaths = [];

for(let file of files) {
    const fileName = file.trim();
    if(fileName) {
        const filePath = `~/app/${fileName}`;
        filePaths.push(filePath);   
    }
}

filePaths // ["~/app/foo.txt", "~/app/.bar", "~/app/baz.foo"]

```

Array.prototype.reduce

```js

// 无法跳出循环
// O（N）复杂度，每个元素将仅迭代一次。
const files = [ 'foo.txt ', '.bar', '   ', 'baz.foo' ];
const filePaths = files.reduce((acc, file) => {
  const fileName = file.trim();
  if(fileName) {
    const filePath = `~/cool_app/${fileName}`;
    acc.push(filePath);
  }
  return acc;
}, []);

// filePaths = [ '~/cool_app/foo.txt', '~/cool_app/.bar', '~/cool_app/baz.foo']

```

方法链(Method chaining)

```js

// 无法跳出循环
// O（cN）复杂度，每个元素c(方法链长度)次迭代
const files = [ 'foo.txt ', '.bar', '   ', 'baz.foo' ];
const filePaths = files
  .map(file => file.trim())
  .filter(Boolean)
  .map(fileName => `~/cool_app/${fileName}`);

// filePaths = [ '~/cool_app/foo.txt', '~/cool_app/.bar', '~/cool_app/baz.foo']

```

### `deepClone`

```js

const deepClone = obj => {
    if (obj === null) return null;
    let clone = Object.assign({}, obj);
    Object.keys(clone).forEach(
        key => (clone[key] = typeof obj[key] === 'object' ? deepClone(obj[key]) : obj[key])
    );
    return Array.isArray(obj) && obj.length
        ? (clone.length = obj.length) && Array.from(clone)
        : Array.isArray(obj)
        ? Array.from(obj)
        : clone;
};
const a = { foo: 'bar', obj: { a: 1, b: 2 } };
const b = deepClone(a); // a !== b, a.obj !== b.obj

```

### `deepFreeze`

```js

const deepFreeze = obj => {
    Object.keys(obj).forEach(prop => {
        if (typeof(obj[prop]) === 'object' && !Object.isFrozen(obj[prop])) deepFreeze(obj[prop]);
    });
    return Object.freeze(obj);
};
'use strict';
const o = deepFreeze([1, [2, 3]]);
o[0] = 3; // not allowed
o[1][0] = 4; // not allowed as well

```

### `deepGet`

```js

const deepGet = (obj, keys) => keys.reduce((xs, x)=> (xs && xs[x] ? xs[x] : null), obj);
let index = 2;
const data = {
    foo: {
        foz: [1, 2, 3],
        bar: {
        baz: ['a', 'b', 'c']
        }
    }
};
deepGet(data, ['foo', 'foz', index]); // get 3   data.foo.foz[index]
deepGet(data, ['foo', 'bar', 'baz', 8, 'foz']); // null

```

### `deepMapKeys`

Deep maps an object's keys.

```js

const deepMapKeys = (obj, fn) =>
    Array.isArray(obj)
        ? obj.map(val => deepMapKeys(val, fn))
        : typeof obj === 'object'
            ? Object.keys(obj).reduce((acc, current) => {
                const key = fn(current);
                const val = obj[current];
                acc[key] = val !== null && typeof val === 'object' ? deepMapKeys(val, fn) : val;
                return acc;
            }, {})
            : obj;
const obj = {
    foo: '1',
    nested: {
        child: {
            withArray: [
                {
                    grandChild: ['hello']
                }
            ]
        }
    }
};
const upperKeysObj = deepMapKeys(obj, key => key.toUpperCase());
/*
{
    "FOO":"1",
    "NESTED":{
        "CHILD":{
            "WITHARRAY":[
                {
                    "GRANDCHILD":[ 'hello' ]
                }
            ]
        }
    }
}
*/

```

### `defaults`

为对象中所有未定义的属性分配默认值。

```js

const defaults = (obj, ...defs) => Object.assign({}, obj, ...defs.reverse(), obj);
defaults({ a: 1 }, { b: 2 }, { b: 6 }, { a: 3 }); // { a: 1, b: 2 }

```

### `dig`

根据给定的键，返回嵌套JSON对象中的目标值。

```js

const dig = (obj, target) => 
    target in obj
        ? obj[target]
        : Object.values(obj).reduce((acc, val) => {
            if(acc !== undefined) return acc;
            if(typeof val === 'object') return dig(val, target);
        }, undefined);
const data = {
    level1: {
        level2: {
            level3: 'some data'
        }
    }
};
dig(data, 'level3'); // 'some data'
dig(data, 'level4'); // undefined

```

### `equals`

在两个值之间进行深度比较以确定它们是否等效。

```js

const equals = (a, b) => {
    if (a === b) return true;
    if (a instanceof Date && b instanceof Date) return a.getTime() === b.getTime();
    if (!a || !b || (typeof a !== 'object' && typeof b !== 'object')) return a === b;
    if (a.prototype !== b.prototype) return false;
    let keys = Object.keys(a);
    if (keys.length !== Object.keys(b).length) return false;
    return keys.every(k => equals(a[k], b[k]));
};
equals({ a: [2, { e: 3 }], b: [4], c: 'foo' }, { a: [2, { e: 3 }], b: [4], c: 'foo' }); // true
equals([1, 2, 3], { 0: 1, 1: 2, 2: 3 }); // true

```

### `findKey`

```js

    // 返回满足全部条件的key
    const findKey = (obj, fn) => Object.keys(obj).reduce((acc, key) => (fn(obj[key]) ? acc.concat(key) : acc) , []);
    // 返回第一个满足条件的key
    const findKey = (obj, fn) => Object.keys(obj).find(key => fn(obj[key], key, obj));
    // 返回最后一个满足条件的key
    const findKey = (obj, fn) => Object.keys(obj).reverse().find(key => fn(obj[key], key, obj));
    findKey(
        {
            barney: { age: 36, active: true },
            fred: { age: 40, active: false },
            pebbles: { age: 1, active: true }
        },
        o => o['active']
    ); // 'barney'

```

### `flattenObject`

```js

const flattenObject = (obj, prefix = '') => 
    Object.keys(obj).reduce((acc, k) => {
        const pre = prefix.length ? `${prefix}.` : '';
        if (
            typeof obj[k] === 'object' &&
            obj[k] !== null &&
            Object.keys(obj[k]).length > 0
        )
            Object.assign(acc, flattenObject(obj[k], pre + k));
        else acc[pre + k] = obj[k];
            return acc;
    }, {});
flattenObject({ a: { b: { c: 1 } }, d: 1 }); // { 'a.b.c': 1, d: 1 }

```

### `forOwn`

遍历对象的所有自有属性，为每个对象运行一个回调。

```js

const forOwn = (obj, fn) => Object.keys(obj).forEach(key => fn(obj[key], key, obj));
forOwn({ foo: 'bar', a: 1 }, v => console.log(v)); // 'bar', 1

```

### `functions`

Returns an array of function property names from own (and optionally inherited) enumerable properties of an object.

```js

// Object.keys(Object.getPrototypeOf(new Foo())) // ["c"]
// Object.keys(new Foo()) // ["a", "b"]
const functions = (obj, inherited = false) =>
    (inherited
        ? [...Object.keys(obj), ...Object.keys(Object.getPrototypeOf(obj))]
        : Object.keys(obj)
    ).filter(key => typeof obj[key] === 'function');
function Foo() {
  this.a = () => 1;
  this.b = () => 2;
}
Foo.prototype.c = () => 3;
functions(new Foo()); // ['a', 'b']
functions(new Foo(), true); // ['a', 'b', 'c']

```

### `get`

```js

const get = (from, ...selectors) =>
    [...selectors].map(s =>
        s
        .replace(/\[([^\[\]]*)\]/g, '.$1.')
        .split('.')
        .filter(t => t !== '')
        .reduce((prev, cur) => prev && prev[cur], from)
    );
const obj = { selector: { to: { val: 'val to select' } }, target: [1, 2, { a: 'test' }] };
get(obj, 'selector.to.val', 'target[0]', 'target[2].a'); // ['val to select', 1, 'test']

```

### `hasKey`

```js

const hasKey = (obj, keys) => {
    return (
        keys.length > 0 &&
        keys.every(key => {
            if (typeof obj !== 'object' || !obj.hasOwnProperty(key)) return false;
            // 内层obj
            obj = obj[key];
            return true;
        })
    );
};
let obj = {
    a: 1,
    b: { c: 4 },
    'b.d': 5
};
hasKey(obj, ['a']); // true
hasKey(obj, ['b']); // true
hasKey(obj, ['b', 'c']); // true
hasKey(obj, ['b.d']); // true
hasKey(obj, ['d']); // false
hasKey(obj, ['c']); // false
hasKey(obj, ['b', 'f']); // false

```

### `How can I deep freeze an object in JavaScript?`

JavaScript中的对象是可变的，无论您是否将它们定义为const。 实际上，在定义对象时使用const只能防止重新分配变量。 但是，可以重新分配对象或数组的属性，如下所示

```js

const myObj = { a: 10, b: 20, c: 30 };
myObj.a = 12;     // { a: 12, b: 20, c: 30 };

const myArr = [15, 25, 35];
myArr[1] = 28;    // [15, 28, 35];

```

为了使对象不可变，可以利用Object.freeze()，防止添加新属性，并在一定程度上防止删除和更改现有属性。 但是，尽管Object.freeze()提供了某种解决方案，但它只能冻结一层，因为实际上它执行的是浅冻结。 这意味着仍可以对对象或数组的属性进行改变：

```js

const myObj = {
    a: 1,
    b: 'hello',
    c: [0, 1, 2],
    d: { e: 1, f: 2 }
};
Object.freeze(myObj);

myObj.a = 10;
myObj.b = 'hi';
myObj.c[1] = 4;
myObj.d.e = 0;
/*
myObj = {
    a: 1,
    b: 'hello',
    c: [0, 4, 2],
    d: { e: 0, f: 2 }
}
*/

```

运用递归

```js

const myObj = {
    a: 1,
    b: 'hello',
    c: [0, 1, 2],
    d: { e: 1, f: 2 }
};

const deepFreeze = obj => {
    Object.keys(obj).forEach(prop => {
        if (obj[prop] === 'object' && !Object.isFrozen(obj[prop])) deepFreeze(v[prop]);
    });
    return Object.freeze(obj);
};
deepFreeze(myObj);

myObj.a = 10;
myObj.b = 'hi';
myObj.c[1] = 4;
myObj.d.e = 0;

/*
myObj = {
  a: 1,
  b: 'hello',
  c: [0, 1, 2],
  d: { e: 1, f: 2 }
}
*/

```

### `How do I clone an object in JavaScript?`

[deepClone](https://ifwechat.com//wiki/javascript-object/#deepclone)
[shallowClone](https://ifwechat.com//wiki/javascript-object/#shallowClone)

```js

let str = 'Hello';
let copy = str;
copy = 'Hi';
// str = 'Hello', copy = 'Hi'

let obj = { a: 1, b: 2 };
let objCopy = obj;
objCopy.b = 4;
// obj = { a: 1, b: 4}, objCopy = { a: 1, b: 4 }

// solution
// note: 浅克隆，当出现深嵌套对象时，这些对象还是会通过引用传递！
let obj = { a: 1, b: 2};
let clone = { ...obj };
clone.b = 4;
// obj = { a: 1, b: 2}, clone = { a: 1, b: 4 }

let otherClone = Object.assign({}, obj);
otherClone.b = 6;
clone.b = 4;
// obj = { a: 1, b: 2}, otherClone = { a: 1, b: 6 }

// solution
// node: 虽然这样有效，但是必须序列化/反序列化整个对象，这可能会严重影响代码的性能，因此，它不太可能适用于较大的对象或对性能要求很重要的项目。可以通过递归函数来深度克隆对象，速度会快得多。
let obj = { a: 1, b: { c: 2 } };
let clone = JSON.parse(JSON.stringify(obj));
clone.b.c = 4;
// obj = { a: 1, b: { c: 2 }}, clone = { a: 1, b: { c: 4 } }

```

### `invertKeyValues`

反转对象键值

```js

const invertKeyValues = (obj, fn) => 
    Object.keys(obj).reduce((acc, key) => {
        const val = fn ? fn(obj[key]) : obj[key];
        acc[val] = acc[val] || [];
        acc[val].push(key);
        return acc;
    }, {});
invertKeyValues({ a: 1, b: 2, c: 1 }); // { 1: [ 'a', 'c' ], 2: [ 'b' ] }
invertKeyValues({ a: 1, b: 2, c: 1 }, value => 'group' + value); // { group1: [ 'a', 'c' ], group2: [ 'b' ] }

```