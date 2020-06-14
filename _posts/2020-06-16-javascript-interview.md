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

### 请解释事件委托
事件委托是将事件监听器添加到父元素，而不是每个子元素单独设置监听。当触发子元素时，事件会冒泡到父元素，监听器就会触发。
这种技术的好处是：
- 减少内存的占用，只需要一个父元素的事件处理程序，而不必为每个后代添加事件处理程序
- 无需从已删除的元素中解绑处理程序，也无需将处理程序绑定到新元素上
参考：
- <https://davidwalsh.name/event-delegate>
- <https://stackoverflow.com/questions/1687296/what-is-dom-event-delegation>

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


