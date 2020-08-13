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