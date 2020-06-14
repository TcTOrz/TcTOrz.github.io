---
layout: post
title: Javascript面试题
categories: [javascript, 面试]
description: 自己遇见的一些题目
keywords: JavaScript, interview
topmost: false
---

**目录**

* TOC
{:toc}

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


