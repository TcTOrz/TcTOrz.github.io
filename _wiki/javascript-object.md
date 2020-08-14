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

### `lowercaseKeys`

从指定的对象创建一个新的对象，其中所有键都小写。

```js

const lowercaseKeys = obj => 
    Object.keys(obj).reduce((acc, key) => {
        acc[key.toLowerCase()] = obj[key];
        return acc;
    }, {});
const myObj = { Name: 'Adam', sUrnAME: 'Smith' };
const myObjLower = lowercaseKeys(myObj); // {name: 'Adam', surname: 'Smith'};

```

### `mapKeys`

```js

const mapKeys = (obj, fn) => 
    Object.keys(obj).reduce((acc, k) => {
        acc[fn(obj[k], k)] = obj[k];
        return acc;
    }, {});
mapKeys({ a: 1, b: 2 }, (val, key) => key + val); // { a1: 1, b2: 2 }

```

### `mapValues`

```js

const mapValues = (obj, fn) => 
    Object.keys(obj).reduce((acc, k) => {
        acc[k] = fn(obj[k]);
        return acc;
    }, {});
const users = {
  fred: { user: 'fred', age: 40 },
  pebbles: { user: 'pebbles', age: 1 }
};
mapValues(users, u => u.age); // { fred: 40, pebbles: 1 }

```

### `matches`

比较两个对象以确定第一个对象是否包含与第二个对象相同的属性值。

```js

const matches = (obj, source) => 
    Object.keys(source).every(key => obj.hasOwnProperty(key) && obj[key] === source[key]);
matches({ age: 25, hair: 'long', beard: true }, { hair: 'long', beard: true }); // true
matches({ hair: 'long', beard: true }, { age: 25, hair: 'long', beard: true }); // false

```

### `matchesWith`

```js

const matchesWith = (obj, source, fn) => 
    Object.keys(source).every(key => 
        obj.hasOwnProperty(key) && fn
            ? fn(obj[key], source[key])
            : obj[key] === source(key)
    );
const isGreeting = val => /^h(?:i|ello)$/.test(val);
matchesWith(
  { greeting: 'hello' },
  { greeting: 'hi' },
  (oV, sV) => isGreeting(oV) && isGreeting(sV)
); // true

```

### `merge`

通过两个或更多对象的组合创建一个新对象。

```js

const merge = (...objs) =>
    // objs
    // 0: {a: Array(2), b: 1}
    // 1: {a: {…}, b: Array(2), c: "foo"}
    [...objs].reduce(
        (acc, obj) =>
            Object.keys(obj).reduce((a, k) => {
                acc[k] = acc.hasOwnProperty(k) ? [].concat(acc[k]).concat(obj[k]) : obj[k];
                return acc;
            }, {})
    , {});
const object = {
  a: [{ x: 2 }, { y: 4 }],
  b: 1
};
const other = {
  a: { z: 3 },
  b: [2, 3],
  c: 'foo'
};
merge(object, other); // { a: [ { x: 2 }, { y: 4 }, { z: 3 } ], b: [ 1, 2, 3 ], c: 'foo' }

```

### `nest`

嵌套

```js

const nest = (items, id = null, link = 'parent_id') =>
  items
    .filter(item => item[link] === id)
    .map(item => ({ ...item, children: nest(items, item.id, link) }));
// One top level comment
const comments = [
  { id: 1, parent_id: null },
  { id: 2, parent_id: 1 },
  { id: 3, parent_id: 1 },
  { id: 4, parent_id: 2 },
  { id: 5, parent_id: 4 }
];
const nestedComments = nest(comments); // [{ id: 1, parent_id: null, children: [...] }]

```

### `objectFromPairs`

根据固定的键值创建对象

```js

const objectFromPairs = arr => arr.reduce((a, [key, val]) => ((a[key] = val), a), {});
objectFromPairs([['a', 1], ['b', 2]]); // {a: 1, b: 2}

```

### `objectToEntries`

从一个对象创建一个键-值对数组。

```js

const objectToEntries = obj => Object.keys(obj).map(k => [k, obj[k]]);
objectToEntries({ a: 1, b: 2 }); // [ ['a', 1], ['b', 2] ]

```

### `objectToPairs`

```js

const objectToPairs = obj => Object.entries(obj);
objectToPairs({ a: 1, b: 2 }); // [ ['a', 1], ['b', 2] ]

```

### `objectToQueryString`

```js

const objectToQueryString = queryParameters => {
  return queryParameters
    ? Object.entries(queryParameters).reduce((queryString, [key, val], index) => {
      const symbol = queryString.length === 0 ? '?' : '&';
      queryString += typeof val === 'string' ? `${symbol}${key}=${val}` : '';
      return queryString;
    }, '')
    : '';
};
objectToQueryString({ page: '1', size: '2kg', key: undefined }); // '?page=1&size=2kg'

```

### `omit`

```js

const omit = (obj, arr) =>
  Object.keys(obj)
    .filter(k => !arr.includes(k))
    .reduce((acc, key) => ((acc[key] = obj[key]), acc), {});
omit({ a: 1, b: '2', c: 3 }, ['b']); // { 'a': 1, 'c': 3 }

```

### `omitBy`

```js

const omitBy = (obj, fn) =>
  Object.keys(obj)
    .filter(k => !fn(obj[k], k))
    .reduce((acc, key) => ((acc[key] = obj[key]), acc), {});
omitBy({ a: 1, b: '2', c: 3 }, x => typeof x === 'number'); // { b: '2' }

```

### `orderBy`

```js

const orderBy = (arr, props, orders) =>
    [...arr].sort((a, b) =>
        props.reduce((acc, prop, i) => {
            // 当比较的值相等时再进行下一步的比较
            if (acc === 0) {
                const [p1, p2] = orders && orders[i] === 'desc' ? [b[prop], a[prop]] : [a[prop], b[prop]];
                acc = p1 > p2 ? 1 : p1 < p2 ? -1 : 0;
            }
            return acc;
        }, 0)
    );
const users = [{ name: 'fred', age: 48 }, { name: 'barney', age: 36 }, { name: 'fred', age: 40 }];
orderBy(users, ['name', 'age'], ['asc', 'desc']); // [{name: 'barney', age: 36}, {name: 'fred', age: 48}, {name: 'fred', age: 40}]
orderBy(users, ['name', 'age']); // [{name: 'barney', age: 36}, {name: 'fred', age: 40}, {name: 'fred', age: 48}]

```

### `pick`

```js

const pick = (obj, arr) =>
    arr.reduce((acc, curr) => (curr in obj && (acc[curr] = obj[curr]), acc), {});
pick({ a: 1, b: '2', c: 3 }, ['a', 'c']); // { 'a': 1, 'c': 3 }

```

### `pickBy`

```js

const pickBy = (obj, fn) =>
  Object.keys(obj)
    .filter(k => fn(obj[k], k))
    .reduce((acc, key) => ((acc[key] = obj[key]), acc), {});
pickBy({ a: 1, b: '2', c: 3 }, x => typeof x === 'number'); // { 'a': 1, 'c': 3 }

```

### `renameKeys`

```js

const renameKeys = (keysMap, obj) =>
    Object.keys(obj).reduce(
        (acc, key) => ({
            ...acc,
            ...{ [keysMap[key] || key]: obj[key] }
        }),
        {}
    );
const obj = { name: 'Bobo', job: 'Front-End Master', shoeSize: 100 };
renameKeys({ name: 'firstName', job: 'passion' }, obj); // { firstName: 'Bobo', passion: 'Front-End Master', shoeSize: 100 }

```

### `shallowClone`

浅拷贝

```js

const shallowClone = obj => Object.assign({}, obj);
const a = { x: true, y: 1 };
const b = shallowClone(a); // a !== b

// Note: 
// a === b false
// a.z === b.z true
var a = { x: true, y: 1, z: {c: 1} };
var b = shallowClone(a);

```

### `size`

```js

const size = val =>
    Array.isArray(val)
        ? val.length
        : val && typeof val === 'object'
            ? val.size || val.length || Object.keys(val).length
            : typeof val === 'string'
                ? new Blob([val]).size
                : 0;
size([1, 2, 3, 4, 5]); // 5
size('size'); // 4
size({ one: 1, two: 2, three: 3 }); // 3

```

### `toPairs`

根据对象或其他可迭代对象（对象，数组，字符串，集合等）创建键-值对数组。

[Symbol.iterator](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator)

1. Array.prototype[@@iterator]()
2. TypedArray.prototype[@@iterator]()
3. String.prototype[@@iterator]()
4. Map.prototype[@@iterator]()
5. Set.prototype[@@iterator]()

[Array.prototype.entries()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/entries)方法返回一个新的Array Iterator对象，该对象包含数组中每个索引的键/值对。 

[Object.entries()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)方法返回一个给定对象自身可枚举属性的键值对数组，其排列与使用 for...in 循环遍历该对象时返回的顺序一致（区别在于 for-in 循环还会枚举原型链中的属性）。

```js

const toPairs = obj =>
    obj[Symbol.iterator] instanceof Function && obj.entries instanceof Function
        ? Array.from(obj.entries())
        : Object.entries(obj);
toPairs({ a: 1, b: 2 }); // [ ['a', 1], ['b', 2] ]  Object.entries()
toPairs([2, 4, 8]); // [ [0, 2], [1, 4], [2, 8] ]   obj.entries()
toPairs('shy'); // [ ['0', 's'], ['1', 'h'], ['2', 'y'] ]   Object.entries()
toPairs(new Set(['a', 'b', 'c', 'a'])); // [ ['a', 'a'], ['b', 'b'], ['c', 'c'] ]   obj.entries()

```

### `transform`

```js

const transform = (obj, fn, acc) => Object.keys(obj).reduce((a, k) => fn(a, obj[k], k, obj), acc);
transform(
    { a: 1, b: 2, c: 1 },
    (r, v, k) => {
        (r[v] || (r[v] = [])).push(k);
        return r;
    },
    {}
); // { '1': ['a', 'c'], '2': ['b'] }

```

### `truthCheckCollection`

```js

const truthCheckCollection = (collection, pre) => collection.every(obj => obj[pre]);
truthCheckCollection([{ user: 'Tinky-Winky', sex: 'male' }, { user: 'Dipsy', sex: 'male' }], 'sex'); // true

```

### `unflattenObject`

```js

const unflattenObject = obj =>
    Object.keys(obj).reduce((acc, k) => {
        if (k.indexOf('.') !== -1) {
            const keys = k.split('.');
            Object.assign(
                acc,
                JSON.parse(
                '{' +
                    keys.map((v, i) => (i !== keys.length - 1 ? `"${v}":{` : `"${v}":`)).join('') +
                    obj[k] +
                    '}'.repeat(keys.length)
                )
            );
        } else acc[k] = obj[k];
        return acc;
    }, {});
unflattenObject({ 'a.b.c': 1, d: 1 }); // { a: { b: { c: 1 } }, d: 1 }

```

### What is the difference between JavaScript's `for...in`, `for...of` and `forEach`?

`for...in`用于迭代对象的所有可枚举属性，包括继承的可枚举属性。 该迭代语句可用于数组字符串或普通对象，但不能用于Map或Set对象。

```js

for (let prop in ['a', 'b', 'c']) 
    console.log(prop);            // 0, 1, 2 (array indexes)

for (let prop in 'str') 
    console.log(prop);            // 0, 1, 2 (string indexes)

for (let prop in {a: 1, b: 2, c: 3}) 
    console.log(prop);            // a, b, c (object property names)

for (let prop in new Set(['a', 'b', 'a', 'd'])) 
    console.log(prop);            // undefined (no enumerable properties)

```

`for...of`用于迭代可迭代对象，迭代其值而不是其属性。 该迭代语句可以与数组，字符串，Map或Set对象一起使用，但不能与普通对象一起使用。

```js

for (let val of ['a', 'b', 'c']) 
    console.log(val);            // a, b, c (array values)

for (let val of 'str') 
    console.log(val);            // s, t, r (string characters)

for (let val of {a: 1, b: 2, c: 3}) 
    console.log(prop);           // TypeError (not iterable)

for (let val of new Set(['a', 'b', 'a', 'd'])) 
    console.log(val);            // a, b, d (Set values)

```

`forEach()`是Array原型的一种方法，它允许您遍历数组的元素。 尽管`forEach()`仅迭代数组，但它可以在迭代时访问每个元素的值和索引。

```js

['a', 'b', 'c'].forEach(
    val => console.log(val)     // a, b, c (array values)
);

['a', 'b', 'c'].forEach(
    (val, i) => console.log(i)  // 0, 1, 2 (array indexes)
);

```