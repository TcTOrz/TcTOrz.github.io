---
layout: post
title: Javascript Object
categories: [javascript, Object]
description: javascript object
keywords: JavaScript, object
topmost: false
---

## `Object`构造函数

```js

// 构造函数
function T(){}

// 实例化的constructor属性
new T().constructor === T // true
// 相当于
T.prototype.constructor === T // true

// __proto属性
new T().__proto__ === T.prototype // true
new T().__proto__.constructor === T.prototype.constructor // true

// T构造函数没有constructor属性，所以沿原型链向上查找，即
// T.constructor相当于new Function().constructor
T.constructor === T.__proto__.constructor // true

```