---
layout: wiki
title: Javascript-browser
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## `Browser`

### `arrayToHtmlList`

`Array.prototype.map()` `document.querySelector()`

```js

const arrayToHtmlList = (arr, listID) => 
        (el => (
        (el = document.querySelector('#' + listID)),
        (el.innerHTML += arr.map(item => `<li>${item}</li>`).join(''))
    ))();
arrayToHtmlList(['item 1', 'item 2'], 'myListID');

```

### `bottomVisible`

```js

// 页面底部可见返回true，否则false
const bottomVisible = () => 
    document.documentElement.clientHeight + window.scrollY >=
    (document.documentElement.scrollHeight || document.documentElement.clientHeight);
bottomVisible(); // true

```

### `Can I use an arrow function as the callback for an event listener in JavaScript?`

```js

const toggleElements = document.querySelectorAll('.toggle');
toggleElements.forEach(el => {
  el.addEventListener('click', function() {
    this.classList.toggle('active');
  });
});

const toggleElements = document.querySelectorAll('.toggle');
toggleElements.forEach(el => {
  el.addEventListener('click', () => {
    this.classList.toggle('active'); // `this` refers to `window`
    // Error: Cannot read property 'toggle' of undefined
  });
});

const toggleElements = document.querySelectorAll('.toggle');
toggleElements.forEach(el => {
  el.addEventListener('click', (e) => {
    e.currentTarget.classList.toggle('active'); // works correctly
  });
});

```

### `copyToClipboard`

```js

const copyToClipboard = str => {
    const el = document.createElement('textarea');
    el.value = str;
    el.setAttribute('readonly', '');
    el.style.position = 'absolute';
    el.style.left = '-9999px';
    document.body.appendChild(el);
    const selected =
        document.getSelection().rangeCount > 0 ? document.getSelection().getRangeAt(0) : false;
    el.select();
    document.execCommand('copy');
    document.body.removeChild(el);
    if (selected) {
        document.getSelection().removeAllRanges();
        document.getSelection().addRange(selected);
    }
};
copyToClipboard('Lorem ipsum'); // 'Lorem ipsum' copied to clipboard.

```

### `counter`

为指定的选择器创建一个具有指定范围，步长和持续时间的计数器。

```js

const counter = (selector, start, end, step = 1, duration = 2000) => {
    let current = start,
        _step = (end - start) * step < 0 ? -step : step,
        timer = setInterval(() => {
            current += _step;
            document.querySelector(selector).innerHTML = current;
            if (current >= end) document.querySelector(selector).innerHTML = end;
            if (current >= end) clearInterval(timer);
        }, Math.abs(Math.floor(duration / (end - start))));
    return timer;
};
counter('#my-id', 1, 1000, 5, 2000); // Creates a 2-second timer for the element with id="my-id"

```

### `createElement`

```js

const createElement = str => {
  const el = document.createElement('div');
  el.innerHTML = str;
  return el.firstElementChild;
};

const el = createElement(
  `<div class="container">
    <p>Hello!</p>
  </div>`
);
console.log(el.className); // 'container'

```

### `createEventHub`

使用emit，on和off方法创建一个发布/订阅（发布-订阅）事件中心。

```js

const createEventHub = () => ({
    hub: Object.create(null),
    emit(event, data) {
        (this.hub[event] || []).forEach(handler => handler(data));
    },
    on(event, handler) {
        if(!this.hub[event]) this.hub[event] = [];
        this.hub[event].push(handler);
    },
    off(event, handler) {
        const i = (this.hub[event] || []).findIndex(h => h === handler);
        if (i > -1) this.hub[event].splice(i, 1);
        if (this.hub[event].length === 0) delete this.hub[event];
    }
});

const handler = data => console.log(data);
const hub = createEventHub();
let increment = 0;
// Subscribe: listen for different types of events
hub.on('message', handler);
hub.on('message', () => console.log('Message event fired'));
hub.on('increment', () => increment++);
// Publish: emit events to invoke all handlers subscribed to them, passing the data to them as an argument
hub.emit('message', 'hello world'); // logs 'hello world' and 'Message event fired'
hub.emit('message', { hello: 'world' }); // logs the object and 'Message event fired'
hub.emit('increment'); // `increment` variable is now 1
// Unsubscribe: stop a specific handler from listening to the 'message' event
hub.off('message', handler);

```

### `currentURL`

```js

const currentURL = () => window.location.href;
currentURL(); // 'https://google.com'

```

### `detectDeviceType`

检测网站是在移动设备上/笔记本上打开。

```js

const detectDeviceType = () =>
  /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)
    ? 'Mobile'
    : 'Desktop';
detectDeviceType(); // "Mobile" or "Desktop"

```

### `elementContains`

如果父元素包含子元素，则返回true，否则返回false。

[node.contains( otherNode )](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/contains)
- node 是否包含otherNode节点.
- otherNode 是否是node的后代节点.
- 如果 otherNode 是 node 的后代节点或是 node 节点本身.则返回true , 否则返回 false.

```js

const elementContains = (parent, child) => parent !== child && parent.contains(child);
elementContains(document.querySelector('head'), document.querySelector('title')); // true
elementContains(document.querySelector('body'), document.querySelector('body')); // false

```

### `elementIsFocused`

如果给定元素已聚焦，则返回true，否则返回false。

```js

const elementIsFocused = el => (el === document.activeElement);
elementIsFocused(el); // true if the element is focused

```