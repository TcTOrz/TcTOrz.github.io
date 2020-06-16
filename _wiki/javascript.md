---
layout: wiki
title: Javascript
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

### 打印网页标签个数以及标签最多的一组数据

```javascript

// tag数
new Set(document.getElementsByTagName("*")).size

// 最多的tag
console.table(Object.entries([...document.getElementsByTagName("*")].map(v=>v.nodeName).reduce((obj, v)=>{
    obj[v] = obj[v]? obj[v]+1: 1;
    return obj
}, {})).sort((a, b)=>b[1]-a[1]).slice(0, 1))

```
