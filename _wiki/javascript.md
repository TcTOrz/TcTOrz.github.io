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

### `insertAt`

`Array.prototype.splice()`

```js

const insertAt = (arr, i, ...v) => {
    arr.splice(i, 0, ...v);
    return arr;
};
let myArray = [1, 2, 3, 4];
insertAt(myArray, 2, 5); // myArray = [1, 2, 3, 5, 4]
let otherArray = [2, 10];
insertAt(otherArray, 0, 4, 6, 8); // otherArray = [2, 4, 6, 8, 10]

```

### `intersection`返回两个数组中都存在的数据的数组

`Array.prototype.filter()`

```js

const intersection = (a, b) => {
    const s = new Set(b);
    return [...new Set(a)].filter(x => s.has(x));
};
intersection([1, 2, 3], [4, 3, 2]); // [2, 3]

```

### `intersectionBy`

`Array.prototype.filter()`

```js

const intersectionBy = (a, b, fn) => {
    const s = new Set(b.map(fn));
    return [...new Set(a)].filter(x => s.has(fn(x)));
};
intersectionBy([2.1, 1.2], [2.3, 3.4], Math.floor); // [2.1]
intersectionBy([{ title: 'Apple' }, { title: 'Orange' }], [{ title: 'Orange' }, { title: 'Melon' }], x => x.title) // [{ title: 'Orange' }]

```

### `intersectionWith`

`Array.prototype.filter()` `Array.prototype.findIndex()`

```js

const intersectionWith = (a, b, comp) => a.filter(x => b.findIndex(y=> comp(x, y)) !== -1);
intersectionWith([1, 1.2, 1.5, 3, 0], [1.9, 3, 0, 3.9], (a, b) => Math.round(a) === Math.round(b)); // [1.5, 3, 0]

```

### `isContainedIn`

`Array.prototype.filter()` `Array.prototype.some()`

```js

const isContainedIn = (a, b) => {
    for( const v of new Set(a) ) {
        if(!b.some(e => e === v) || a.filter(e => e === v).length > b.filter(e => e === v).length)
            return false;
    }
    return true;
};
isContainedIn([1, 4], [2, 4, 1]); // true

```

### `isSorted`返回1，升序。-1降序。0没有排序

```js

const isSorted = arr => {
    let direction = -(arr[0] - arr[1]);
    for(let [i, val] of arr.entries()) {
        direction = !direction ? -(arr[i - 1] - arr[i]) : direction;
        if (i === arr.length - 1) 
            return !direction ? 0 : direction / Math.abs(direction);
        else if ((val - arr[i + 1]) * direction > 0) 
            return 0;
    }
};
isSorted([0, 1, 2, 2]); // 1
isSorted([4, 3, 2]); // -1
isSorted([4, 3, 5]); // 0

```

### `JSONtoCSV`

`Array.prototype.join()` `Array.prototype.reduce()`

```js

const JSONtoCSV = (arr, columns, delimiter = ",") => 
    [
        columns.join(delimiter),
        ...arr.map(obj => 
            columns.reduce(
                (acc, key) => `${acc}${!acc.length ? '' : delimiter}"${!obj[key] ? '' : obj[key]}"`,
                ''
            )
        )
    ].join('\n');
JSONtoCSV([{ a: 1, b: 2 }, { a: 3, b: 4, c: 5 }, { a: 6 }, { b: 7 }], ['a', 'b']); // 'a,b\n"1","2"\n"3","4"\n"6",""\n"","7"'
JSONtoCSV([{ a: 1, b: 2 }, { a: 3, b: 4, c: 5 }, { a: 6 }, { b: 7 }], ['a', 'b'], ';'); // 'a;b\n"1";"2"\n"3";"4"\n"6";""\n"";"7"'

```

### `juxt`

`Array.prototype.map()`

```js

const juxt = (...fns) => (...args) => [...fns].map(fn => [...args].map(fn));
const juxt = (...fns) => (...args) => [...args].map(arg => [...fns].map(fn => fn(arg)));
juxt(
  x => x + 1,
  x => x - 1,
  x => x * 10
)(1, 2, 3); // [[2,3,4],[0,1,2],[10,20,30]]

juxt(
  s => s.length,
  s => s.split(" ").join("-")
)("30 seconds of code"); // [[18],['30-seconds-of-code']]

```

### `last`

```js

const last = arr => (arr && arr.length ? arr[arr.length - 1] : undefined);
last([1, 2, 3]); // 3
last([]); // undefined
last(null); // undefined
last(undefined); // undefined

```

### `longestItem`

`Array.prototype.reduce()`

```js

const longestItem = (...args) => args.reduce((a, x) => (x.length > a.length ? x : a));
longestItem('this', 'is', 'a', 'testcase'); // 'testcase'
longestItem(...['a', 'ab', 'abc']); // 'abc'
longestItem(...['a', 'ab', 'abc'], 'abcd'); // 'abcd'
longestItem([1, 2, 3], [1, 2], [1, 2, 3, 4, 5]); // [1, 2, 3, 4, 5]
longestItem([1, 2, 3], 'foobar'); // 'foobar'

```

### `maxN`

`Array.prototype.sort()` `Array.prototype.slice()`

```js

const maxN = (arr, n = 1) => [...arr].sort((a, b) => b - a).slice(0, n);
maxN([1, 2, 3]); // [3]
maxN([1, 2, 3], 2); // [3,2]

```

### `mostFrequent`

`Array.prototype.reduce()`

```js

const mostFrequent = arr => 
    Object.entries(
        arr.reduce((a, v) => {
            a[v] = a[v] ? a[v] + 1 : 1;
            return a;
        }, {})
    ).reduce((a, v) => (v[1] >= a[1] ? v : a), [null, 0])[0];
mostFrequent(['a', 'b', 'a', 'c', 'a', 'a', 'b']); // 'a'

```

### `nthElement`

`Array.prototype.slice()`

```js

const nthElement = (arr, n = 0) => (n === -1 ? arr.slice(n) : arr.slice(n, n + 1))[0];
nthElement(['a', 'b', 'c'], 1); // 'b'
nthElement(['a', 'b', 'b'], -3); // 'a'

```

### `offset`

`Array.prototype.slice()`

```js

const offset = (arr, offset) => [...arr.slice(offset), ...arr.slice(0, offset)];
offset([1, 2, 3, 4, 5], 2); // [3, 4, 5, 1, 2]
offset([1, 2, 3, 4, 5], -2); // [4, 5, 1, 2, 3]

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

### `permutations`

`Array.prototype.map()` `Array.prototype.reduce()`

```js

const permutations = arr => {
    if (arr.length <= 2) return arr.length === 2 ? [arr, [arr[1], arr[0]]] : arr;
    return arr.reduce(
        (acc, item, i) => 
            acc.concat(
                permutations([...arr.slice(0, i), ...arr.slice(i + 1)]).map(val => [item, ...val])
            ),
            []
    );
};
// 遍历步骤：
// 1、item： 1. 33/5递归，返回交换后的数值，map合并数值
// 2、item： 33. 1/5递归，返回交换后的数值，map合并数值
// 3、item： 5. 1/33递归，返回交换后的数值，map合并数值
permutations([1, 33, 5]); // [ [ 1, 33, 5 ], [ 1, 5, 33 ], [ 33, 1, 5 ], [ 33, 5, 1 ], [ 5, 1, 33 ], [ 5, 33, 1 ] ]

```

### `pull`

`Array.prototype.filter()` `Array.prototype.includes()`

```js

const pull = (arr, ...args) => {
//   let argState = Array.isArray(args[0]) ? args[0] : args;
  let pulled = arr.filter(v => !args.includes(v));
  arr.length = 0;
  pulled.forEach(v => arr.push(v));
};
let myArray = ['a', 'b', 'c', 'a', 'b', 'c'];
pull(myArray, 'a', 'c'); // myArray = [ 'b', 'b' ]

```

### `pullAtIndex`

`Array.prototype.filter()` `Array.prototype.includes()` `Array.prototype.push()`

```js

const pullAtIndex = (arr, pullArr) => {
    let removed = [];
    let pulled = arr.map((v, i) => (pullArr.includes(i) ? removed.push(v) : v))
                    .filter((v, i) => !pullArr.includes(i));
    arr.length = 0;
    pulled.forEach(v => arr.push(v));
    return removed;
};
let myArray = ['a', 'b', 'c', 'd'];
let pulled = pullAtIndex(myArray, [1, 3]); // myArray = [ 'a', 'c' ] , pulled = [ 'b', 'd' ]

```

### `pullAtValue`

`Array.prototype.filter()` `Array.prototype.includes()` `Array.prototype.push()`

```js

const pullAtValue = (arr, pullArr) => {
    let removed = [],
        pushToRemove = arr.forEach((v, i) => (pullArr.includes(v) ? removed.push(v) : v)),
        mutateTo = arr.filter((v, i) => !pullArr.includes(v));
    arr.length = 0;
    mutateTo.forEach(v => arr.push(v));
    return removed;
};
let myArray = ['a', 'b', 'c', 'd'];
let pulled = pullAtValue(myArray, ['b', 'd']); // myArray = [ 'a', 'c' ] , pulled = [ 'b', 'd' ]

```

### `pullBy`

`Array.prototype.map()` `Array.prototype.filter()` `Array.prototype.includes()` `Array.prototype.push()`

```js

const pullBy = (arr, ...args) => {
    const length = args.length;
    let fn = length > 1 ? args[length - 1] : undefined;
    fn = typeof fn == 'function' ? (args.pop(), fn) : undefined;
    let argState = (Array.isArray(args[0]) ? args[0] : args).map(val => fn(val));
    let pulled = arr.filter((v, i) => !argState.includes(fn(v)));
    arr.length = 0;
    pulled.forEach(v => arr.push(v));
    };
var myArray = [{ x: 1 }, { x: 2 }, { x: 3 }, { x: 1 }];
pullBy(myArray, [{ x: 1 }, { x: 3 }], o => o.x); // myArray = [{ x: 2 }]

```

### `reducedFilter`

`Array.prototype.filter()` `Array.prototype.map()` `Array.prototype.reduce()`

```js

const reducedFilter = (data, keys, fn) => data.filter(fn)
    .map(el => keys.reduce((acc, key) => {
        acc[key] = el[key];
        return acc;
    }, {}));
const data = [
  {
    id: 1,
    name: 'john',
    age: 24
  },
  {
    id: 2,
    name: 'mike',
    age: 50
  }
];
reducedFilter(data, ['id', 'name'], item => item.age > 24); // [{ id: 2, name: 'mike'}]

```

### `reduceSuccessive`

`Array.prototype.reduce()`

```js

const reduceSuccessive = (arr, fn, acc) => 
    arr.reduce((res, val, i, arr) => (res.push(fn(res.slice(-1)[0], val, i, arr)), res), [acc]);
reduceSuccessive([1, 2, 3, 4, 5, 6], (acc, val) => acc + val, 0); // [0, 1, 3, 6, 10, 15, 21]

```

### `reduceWhich`

`Array.prototype.reduce()`

```js

const reduceWhich = (arr, comparator = (a, b) => a - b) =>  arr.reduce((a, b) => (comparator(a, b) >= 0 ? b : a));
reduceWhich([1, 3, 2]); // 1
reduceWhich([1, 3, 2], (a, b) => b - a); // 3
reduceWhich(
  [{ name: 'Tom', age: 12 }, { name: 'Jack', age: 18 }, { name: 'Lucy', age: 9 }],
  (a, b) => a.age - b.age
); // {name: "Lucy", age: 9}

```

### 重构`for...in`循环来避免ESlint警告。

> for..in loops iterate over the entire prototype chain, which is virtually never what you want. Use Object.{keys,values,entries}, and iterate over the resulting array.eslint(no-restricted-syntax)

`Object.keys()`

```js

const data = [3, 4];
// Same as for (let k in data) console.log(k)
Object.keys(data).forEach(k => console.log(k));
// 0 1

```

`Object.values()`

```js

const data = [3, 4];
// Iterate over the values
Object.keys(data).forEach(v => console.log(v));
// 3 4

```

`Object.entries()`

```js

const data = [3, 4];
// Iterate over the data, returning key-value pairs
Object.entries(data).forEach(e => console.log(e[0], e[1]));
// [0, 3] [1, 4]

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