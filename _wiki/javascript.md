---
layout: wiki
title: Javascript
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## Array

### `all`

`Array.prototype.every()`

```js

const all = (arr, fn = Boolean) => arr.every(fn);
all([1, 2, 3], x => x > 1); // false
all([1, 2, 3]); // true

```

### `aperture`

`Array.prototype.slice()` `Array.prototype.map()`

```js

const aperture = (n, arr) =>
    n > arr.length
        ? []
        : arr.slice(n - 1).map((v, i)=> [...arr.slice(i, i + n - 1), v]);

aperture(2, [1, 2, 3, 4]); // [[1, 2], [2, 3], [3, 4]]
aperture(3, [1, 2, 3, 4]); // [[1, 2, 3], [2, 3, 4]]
aperture(5, [1, 2, 3, 4]); // []

```

### `arrayToCSV`将二维数组转换为逗号分隔值（CSV）字符串。

`Array.prototype.map()` `Array.prototype.join()`

```js

const arrayToCSV = (arr, delimiter = ',') =>
    arr.map(v => v.map(x => (isNaN(x) ? `"${x.replace(/"/g, '""')}"` : x)).join(delimiter)).join('\n');

arrayToCSV([['a', 'b'], ['c', 'd']]); // '"a","b"\n"c","d"'
arrayToCSV([['a', 'b'], ['c', 'd']], ';'); // '"a";"b"\n"c";"d"'
arrayToCSV([['a', '"b" great'], ['c', 3.1415]]); // '"a","""b"" great"\n"c",3.1415'

```

### `bifurcateBy`分组

`Array.prototype.reduce()` `Array.prototype.push()`

```js

const bifurcateBy = (arr, fn) => 
    arr.reduce((acc, val, i) => (acc[fn(val) ? 0 : 1].push(val), acc), [[], []]);
const bifurcateBy = (arr, fn) => 
    arr.reduce((acc, val, i) => {acc[fn(val) ? 0 : 1].push(val);return acc;}, [[], []]);

bifurcateBy(['beep', 'boop', 'foo', 'bar'], x => x[0] === 'b'); // [ ['beep', 'boop', 'bar'], ['foo'] ]

```

### `chunk`将数组分为指定大小的小数组

`Array.from()` `Array.prototype.slice()` `Math.ceil()`

```js

const chunk = (arr, size) =>
    Array.from({length: Math.ceil(arr.length / size)}, (v, i) => arr.slice(i * size, i * size + size));
chunk([1, 2, 3, 4, 5], 2); // [[1,2],[3,4],[5]]

```

### `chunkIntoN`将一个数组分成n个较小的数组。

`Array.from()` `Array.prototype.slice()` `Math.ceil()`

```js

const chunkIntoN = (arr, n) => {
    const size = Math.ceil(arr.length / n);
    return Array.from({length: n}, (v, i) => arr.slice(i * size, i * size + size));
};
chunkIntoN([1, 2, 3, 4, 5, 6, 7], 3); // [[1,2], [3,4], [5,6], [7]]

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

### `compact`

`Array.prototype.filter()`

```js

const compact = arr => arr.filter(Boolean);
compact([0, 1, false, 2, '', 3, 'a', 'e' * 23, NaN, 's', 34]); // [ 1, 2, 3, 'a', 's', 34 ]

```

### `countBy`

根据给定的函数对数组的元素进行分组，并返回每个组中元素的计数。

`Array.prototype.map()` `Array.prototype.reduce()`

```js

const countBy = (arr, fn) => 
    arr.map(typeof fn === 'function' ? fn : val => val[fn]).reduce((acc, val) => {
        acc[val] = (acc[val] || 0) + 1;
        return acc;
    }, {});

countBy([6.1, 4.2, 6.3], Math.floor); // {4: 1, 6: 2}
countBy(['one', 'two', 'three'], 'length'); // {3: 2, 5: 1}
countBy([{ count: 5 }, { count: 10 }, { count: 5 }], x => x.count) // {5: 2, 10: 1}

```

### `countOccurrences`计算数组中某个值的出现次数。

`Array.prototype.reduce()`

```js

const countOccurrences = (arr, val) => 
    arr.reduce((a, v) => (v === val ? a + 1 : a), 0);
countOccurrences([1, 1, 2, 1, 2, 3], 1); // 3

```

### `deepFlatten`多维数组转化成一维数组

`Array.prototype.concat()`

```js

const deepFlatten = arr => [].concat(...arr.map(v => (Array.isArray(v) ? deepFlatten(v) : v)));
deepFlatten([1, [2], [[3], 4], 5]); // [1, 2, 3, 4, 5]

```

### `difference`

`Array.prototype.filter()`

```js

const difference = (a, b) => {
    const s = new Set(b);
    return a.filter(x => !s.has(x));
};
difference([1, 2, 3], [1, 2, 4]); // [3]

```

### `differenceBy`

`Array.prototype.filter()` `Array.prototype.map()`

```js

const differenceBy = (a, b, fn) => {
    const s = new Set(b.map(fn));
    return a.map(fn).filter(x => !s.has(x));
};
differenceBy([2.1, 1.2], [2.3, 3.4], Math.floor); // [1]
differenceBy([{ x: 2 }, { x: 1 }], [{ x: 1 }], v => v.x); // [2]

```

### `differenceWith`

`Array.prototype.filter()` `Array.prototype.findIndex()`

```js

const differenceWith = (arr, val, comp) => arr.filter(a => val.findIndex(b => comp(a, b)) === -1); // 相当于双重循环
differenceWith([1, 1.2, 1.5, 3, 0], [1.9, 3, 0], (a, b) => Math.round(a) === Math.round(b)); // [1, 1.2]

```

### `dropRightWhile`

`Array.prototype.slice()`

```js

const dropRightWhile = (arr, func) => {
    let rightIndex = arr.length;
    while(rightIndex-- && !func(arr[rightIndex]));
    return arr.slice(0, rightIndex + 1);
}
dropRightWhile([1, 2, 3, 4], n => n < 3); // [1, 2]

```

### `everyNth`

`Array.prototype.filter()`

```js

const everyNth = (arr, nth) => arr.filter((e, i) => i % nth === nth - 1);
everyNth([1, 2, 3, 4, 5, 6], 2); // [ 2, 4, 6 ]

```

### `filterNonUnique`

`Array.prototype.filter()`

```js

const filterNonUnique = arr => arr.filter( i => arr.indexOf(i) === arr.lastIndexOf(i) );
filterNonUnique([1, 2, 2, 3, 4, 4, 5]); // [1, 3, 5]

```

### `filterNonUniqueBy`

`Array.prototype.every()` `Array.prototype.filter()`

```js

// 索引不等并且值也不等，说明唯一
const filterNonUniqueBy = (arr, fn) => arr.filter((v, i) => arr.every((x, j) => (i === j) === fn(v, x)));
filterNonUniqueBy(
  [
    { id: 0, value: 'a' },
    { id: 1, value: 'b' },
    { id: 2, value: 'c' },
    { id: 1, value: 'd' },
    { id: 0, value: 'e' }
  ],
  (a, b) => a.id == b.id
); // [ { id: 2, value: 'c' } ]

```

### `findLastIndex`

`Array.prototype.map()` `Array.prototype.filter()` `Array.prototype.pop()`

```js

const findLastIndex = (arr, fn) => 
    (
        arr.map((val, i) => [i, val])
            .filter(([i, val]) => fn(val, i, arr))
            .pop() || [-1]
    )[0];
findLastIndex([1, 2, 3, 4], n => n % 2 === 1); // 2 (index of the value 3)
findLastIndex([1, 2, 3, 4], n => n === 5); // -1 (default value when not found)

```

### `flatten`

`Array.prototype.reduce()` `Array.prototype.concat()`

```js

// 注意	逻辑与`&&`和条件运算符`… ? … : …`优先级关系
const flatten = (arr, depth = 1) => 
    arr.reduce((a, v) => a.concat(depth > 1 && Array.isArray(v) ? flatten(v, depth-1) : v), []);
flatten([1, [2], 3, 4]); // [1, 2, 3, 4]
flatten([1, [2, [3, [4, 5], 6], 7], 8], 2); // [1, 2, 3, [4, 5], 6, 7, 8]

```

### `forEachRight`

`Array.prototype.slice()` `Array.prototype.reverse()` `Array.prototype.forEach()`

> slice()和concat()适用于不包含引用对象的**一维数组**的深拷贝,多维数组则浅拷贝。如果数组需要深拷贝，则使用JSON.parse(JSON.stringify(array))

[MDN concat描述](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)
[MDN slice描述](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)

```js

// arr.slice主要目的是clone原数组
// 注意 slice()和concat()适用于不包含引用对象的<一维数组>的深拷贝,多维数组则浅拷贝。
//      如果数组需要深拷贝，则使用JSON.parse(JSON.stringify(array))
const forEachRight = (arr, fn) => arr.slice().reverse().forEach(fn);
forEachRight([1, 2, 3, 4], val => console.log(val)); // '4', '3', '2', '1'

```

### `frequencies`

`Array.prototype.reduce()`

```js

const frequencies = arr => arr.reduce((a, v) => {
    a[v] = a[v] ? a[v] + 1 : 1;
    return a;
}, {});
frequencies(['a', 'b', 'a', 'c', 'a', 'a', 'b']); // { a: 4, b: 2, c: 1 }

```

### `groupBy`

根据给定的函数对数组的元素进行分组。

`Array.prototype.map()` `Array.prototype.reduce()`

```js

const groupBy = (arr, fn) =>
    arr.map(typeof fn === 'function' ? fn : val => val[fn]).reduce((acc, val, i) => {
        acc[val] = (acc[val] || []).concat(arr[i]);
        return acc;
    }, {});

groupBy([6.1, 4.2, 6.3], Math.floor); // {4: [4.2], 6: [6.1, 6.3]}
groupBy(['one', 'two', 'three'], 'length'); // {3: ['one', 'two'], 5: ['three']}

```

### `haveSameContents`

`Array.prototype.filter()`

```js

const haveSameContents = (a, b) => {
    for( const v of new Set([...a, ...b]) ) {
        if( a.filter(e => e === v).length !== b.filter(e => e === v).length )
        return false;
        return true;
    }
};
haveSameContents([1, 2, 4], [2, 4, 1]); // true

```

### `head`

```js

const head = arr => (arr && arr.length ? arr[0] : undefined);
head([1, 2, 3]); // 1
head([]); // undefined
head(null); // undefined
head(undefined); // undefined

```

### 如何在`JavaScript`中转换并迭代成数组？

`String`

```js

const name = 'name';
const letters = [...name];  // ["n", "a", "m", "e"]

```

`Set`

```js

const data = [1, 2, 3, 1, 2, 4];
const values = new Set(data);
const uniqueValues = [...values];   // [1, 2, 3, 4]

```

`NodeList`

```js

const nodes = document.childNodes;
const nodeArray = [...nodes];

```

### 如何在`JavaScript`中合并两个数组？

```js

const a = [1, 2, 3];
const b = [4, 5, 6];
const merged = [...a, ...b]; // [1, 2, 3, 4, 5, 6]

const a = [1, 2, 3];
const b = [4, 5, 6];
const merged = [].concat(a, b); // [1, 2, 3, 4, 5, 6]
// -- OR --
const alsoMerged = a.concat(b); // [1, 2, 3, 4, 5, 6]

const a = [1, 2, 3];
const b = true;
const c = 'hi';
const spreadAb = [...a, ...b]; // Error: b is not iterable
const spreadAc = [...a, ...c]; // [1, 2, 3, 'h', 'i'], wrong result
// You should use [...a, b] and [...a, c] instead
const concatAb = [].concat(a, b); // [1, 2, 3, true]
const concatAb = [].concat(a, c); // [1, 2, 3, 'hi']

```

### `includesAll`

`Array.prototype.every()` `Array.prototype.includes()`

```js

const includesAll = (arr, values) => values.every(v => arr.includes(v));
includesAll([1, 2, 3, 4], [1, 4]); // true
includesAll([1, 2, 3, 4], [1, 5]); // false

```

### `includesAny`

`Array.prototype.some()` `Array.prototype.includes()`

```js

const includesAny = (arr, values) => values.some(v => arr.includes(v));
includesAny([1, 2, 3, 4], [2, 9]); // true
includesAny([1, 2, 3, 4], [8, 9]); // false

```

### `indexOfAll`

`Array.prototype.reduce()`

```js

const indexOfAll = (arr, val) => arr.reduce((acc, el, i) => (el === val ? [...acc, i] : acc), []);
indexOfAll([1, 2, 3, 1, 2, 3], 1); // [0, 3]
indexOfAll([1, 2, 3], 4); // []

```

### `initialize2DArray`

`Array.prototype.map()`

```js

const initialize2DArray = (w, h, val) => 
    Array.from({length: w}).map(() => Array.from({length: h}).fill(val));
initialize2DArray(2, 2, 0); // [[0,0], [0,0]]

```

### `initializeArrayWithRange`

`Array.from()`

```js

const initializeArrayWithRange = (start, stop, step) => Array.from({length: (stop - start) / step + 1 }, (_, i) => start + (i * step));
initializeArrayWithRange(0, 4, 1);
// [0, 1, 2, 3, 4] 

```

### `initializeArrayWithValues`

```js

const initializeArrayWithValues = (n, val = 0) => Array(n).fill(val);
initializeArrayWithValues(5, 2); // [2, 2, 2, 2, 2]

```

### `initializeNDArray`创建n维数组

`Array.prototype.map()`

```js

const initializeNDArray = (val, ...args) =>
    args.length === 0
    ? val
    : Array.from({length: args[0]}).map(() => initializeNDArray(val, ...args.slice(1)));
initializeNDArray(1, 3); // [1,1,1]
initializeNDArray(5, 2, 2, 2); // [[[5,5],[5,5]],[[5,5],[5,5]]]

```

### `partition`

根据提供的每个元素中的布尔值，将元素分为两个数组

`Array.prototype.reduce()` `Array.prototype.push()`

```js

const partition = (arr, fn) =>
    arr.reduce(
        (acc, val, i, arr) => {
            acc[fn(val, i, arr) ? 0 : 1].push(val);
            return acc;
        },
        [[], []]
    );
const users = [{ user: 'barney', age: 36, active: false }, { user: 'lint', age: 16, active: false }, { user: 'fred', age: 40, active: true }];
partition(users, o => o.active); // [[{ 'user': 'fred',    'age': 40, 'active': true }],[{ 'user': 'barney',  'age': 36, 'active': false }, { user: 'lint', age: 16, active: false }]]

```

### 什么是`Javascript`迭代器(`Iterators`)，在哪里可以使用它们？

`Javascript`迭代器是在`ES6`引入的，用于迭代一系列值(通常是某种集合)。根据定义，迭代器必须实现`next()`函数，返回`{value, done}`对象，其中`value`是迭代系列的下一个值，`done`表示布尔值，确定是否迭代完毕。

项目中使用的最简单的迭代器可能如下：

```js

class LinkedList {
    constructor(data) {
        this.data = data;
    }

    firstItem() {
        return this.data.find(i => i.head);
    }

    findById(id) {
        return this.data.find(i => i.id === id);
    }

    [Symbol.iterator]() {
        let item = {next: this.firstItem().id};
        return {
            next: () => {
                item = this.findById(item.next);
                if(item) {
                    return {value: item.value, done: false};
                }
                return {value: undefined, done: true};
            }
        };
    }
}

const myList = new LinkedList([
    {id: 'a10', value: 'First', next: 'a13', head: true },
    {id: 'a11', value: 'Last', next: null, head: false },
    {id: 'a12', value: 'Third', next: 'a11', head: false },
    {id: 'a13', value: 'Second', next: 'a12', head: false }
]);

for(let item of myList) {
    console.log(item);    // 'First', 'Second', 'Third', 'Last'
}

```

```js

class SpecialList {
    constructor(data) {
        this.data = data;
    }

    [Symbol.iterator]() {
        return this.data[Symbol.iterator]();
    }

    values() {
        return this.data
            .filter(i => i.complete)
            .map(i => i.value)
            [Symbol.iterator]();
    }
}

const myList = new SpecialList([
    {complete: true, value: 'Lorem ipsum'},
    {complete: true, value: 'dolor sit amet'},
    {complete: false},
    {complete: true, value: 'adipiscing elit'}
]);

for(let item of myList) {
    console.log(item);
}

for(let item of myList.values()) {
    console.log(item);
}

```

### `JavaScript` `for ... in`，`for ... of`和`Array.prototype.forEach`之间有什么区别？

`for ... in`用于迭代对象的可枚举属性，包括继承的可枚举属性。可用于数组、字符串或者普通对象，但不能用于`Map`和`Set`集合。

```js

for (let prop in ['a', 'b', 'c']) 
  console.log(prop);            // 0, 1, 2 (array indexes)

for (let prop in 'str') 
  console.log(prop);            // 0, 1, 2 (string indexes)

for (let prop in {a: 1, b: 2, c: 3}) 
  console.log(prop);            // a, b, c (object property names)

```

`for ... of`用于迭代可迭代对象，迭代其值而不是属性。可用于数组、字符串、Map和Set集合，但不能用于普通对象。

```js

for (let val of ['a', 'b', 'c']) 
  console.log(val);            // a, b, c (array values)

for (let val of 'str') 
  console.log(val);            // s, t, r (string characters)

for (let val of new Set(['a', 'b', 'a', 'd'])) 
  console.log(val);            // a, b, d (Set values)

```

`Array.prototype.forEach`是Array原型的方法，允许遍历数组的元素。遍历时可以使用数组的值和索引。

```js

['a', 'b', 'c'].forEach(
  val => console.log(val)     // a, b, c (array values)
);

['a', 'b', 'c'].forEach(
  (val, i) => console.log(i)  // 0, 1, 2 (array indexes)
);

```

### 打印网页标签个数以及标签最多的一组数据。

```js

// tag数
new Set(document.getElementsByTagName("*")).size

// 最多的tag
console.table(Object.entries([...document.getElementsByTagName("*")].map(v=>v.nodeName).reduce((obj, v)=>{
    obj[v] = obj[v]? obj[v]+1: 1;
    return obj
}, {})).sort((a, b)=>b[1]-a[1]).slice(0, 1))

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

### 部分正则表达式的使用

```js

const str = 'JavaScript is a programming language';
/(JavaScript) is a (.*)/.exec(str);
/* 
    [
        0: "JavaScript is a programming language"
        1: "JavaScript"
        2: "programming language"
    ]
 */

const str = 'JavaScript is a programming language';
/(?:JavaScript|Python) is a (.+)/.exec(str);
/*
    [
        0: "JavaScript is a programming language"
        1: "programming language"
    ]
*/

// <name>
const str = 'JavaScript is a programming language';
/(?<subject>.+) is a (?<description>.+)/.exec(str);
/* 
    [
        0: "JavaScript is a programming language"
        1: "JavaScript"
        2: "programming language"
        groups: {
            description: "programming language"
            subject: "JavaScript"
        }
    ]
*/

// \1 \2 \k<name>
const str = 'JavaScript is a programming language - an awesome programming language JavaScript is';
/(.+) is a (?<description>.+) - an awesome \k<description> \1 is/.exec(str);
/* 
    [
        0: "JavaScript is a programming language - an awesome programming language JavaScript is"
        1: "JavaScript"
        2: "programming language"
        groups: {
            description: "programming language"
        } 
    ]
*/

// ?= ?!
const str = 'JavaScript is not the same as Java and you should remember that';
/Java(?=Script)(.*)/.exec(str);
/* 
    [
        0: "JavaScript is not the same as Java and you should remember that"
        1: "Script is not the same as Java and you should remember that"
    ]
*/
/Java(?!Script)(.*)/.exec(str);
/*
    [
        0: 'Java and you should remember that',
        1: ' and you should remember that'
    ]
*/

// 使用/ p {...}和/ u标志来匹配unicode字符。 示例包括但不限于{Emoji}，{Math_Symbols}和{Script = Greek}：
const str = 'Greek looks like this: γεια';
/\p{Script=Greek}+/u.exec(str);
/*
    [
        0: 'γεια'
    ]
*/

```