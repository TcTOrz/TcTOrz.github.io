---
layout: wiki
title: Javascript-未完成
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## Array

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


