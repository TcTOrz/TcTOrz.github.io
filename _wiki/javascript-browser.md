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

### `elementIsVisibleInViewport`

如果指定的元素在视口中可见，则返回true，否则返回false。

```js

const elementIsVisibleInViewport = (el, partiallyVisible = false) => {
    const { top, left, bottom, right } = el.getBoundingClientRect();
    const { innerHeight, innerWidth } = window;
    return partiallyVisible
        ? ((top > 0 && top < innerHeight) || (bottom > 0 && bottom < innerHeight)) &&
            ((left > 0 && left < innerWidth) || (right > 0 && right < innerWidth))
        : top >= 0 && left >= 0 && bottom <= innerHeight && right <= innerWidth;
};
// e.g. 100x100 viewport and a 10x10px element at position {top: -1, left: 0, bottom: 9, right: 10}
elementIsVisibleInViewport(el); // false - (not fully visible) 部分可见 false
elementIsVisibleInViewport(el, true); // true - (partially visible) 部分可见 true

```

### `formToObject`

将表单元素转化为对象

```js

const formToObject = form => 
    Array.from(new FormData(form)).reduce(
        (acc, [key, value]) => {
            acc[key] = value;
            return acc;
        },
        {}
    );
// or
const formToObject = form => 
    Array.from(new FormData(form)).reduce(
        (acc, [key, value]) => ({
            ...acc,
            [key]: value
        }),
        {}
    );
formToObject(document.querySelector('#form')); // { email: 'test@email.com', name: 'Test Name' }

```

### `getBaseURL`

返回没有任何参数的url

```js

const getBaseURL = url => url.indexOf('?') > 0 ? url.slice(0, url.indexOf('?')) : url;
getBaseURL('http://url.com/page?name=Adam&surname=Smith'); // 'http://url.com/page'

```

### `getImages`

从元素中获取所有图像并将它们放入一个数组中

```js

const getImages = (el, includeDuplicates = false) => {
    const images = [...el.getElementsByTagName('img')].map(img => img.getAttribute('src'));
    return includeDuplicates ? images : [...new Set(images)];
};
getImages(document, true); // ['image1.jpg', 'image2.png', 'image1.png', '...']
getImages(document, false); // ['image1.jpg', 'image2.png', '...']

```

### `getScrollPosition`

返回当前页面返回的位置

```js

const getScrollPosition = (el = window) => ({
    x: el.pageXOffset !== undefined ? el.pageXOffset : el.scrollLeft,
    y: el.pageYOffset !== undefined ? el.pageYOffset : el.scrollTop
});
getScrollPosition(); // {x: 0, y: 200}

```

### `getSelectedText`

获取当前选择的文本

```js

const getSelectedText = () => window.getSelection().toString();
getSelectedText(); // 'Lorem ipsum'

```

### `getSiblings`

返回一个包含给定元素的所有同级元素的数组。

```js

const getSiblings = el => 
    [...el.parentNode.childNodes].filter(node => node !== el);
getSiblings(document.querySelector('head')); // ['body']

```

### `getStyle`

返回指定元素的CSS值。

```js

const getStyle = (el, ruleName) => getComputedStyle(el)[ruleName];
getStyle(document.querySelector('p'), 'font-size'); // '16px'

```

### `getURLParameters`

返回一个包含当前URL参数的对象。

```js

const getURLParameters = url =>
    (url.match(/([^?=&]+)(=([^&]*))/g) || []).reduce(
        (a, v) => ((a[v.slice(0, v.indexOf('='))] = v.slice(v.indexOf('=') + 1)), a),
        {}
    );
getURLParameters('http://url.com/page?name=Adam&surname=Smith'); // {name: 'Adam', surname: 'Smith'}
getURLParameters('google.com'); // {}

```

### `hasClass`

如果包含指定的类，则返回true,否则false

```js

const hasClass = (el, className) => el.classList.contains(className);
hasClass(document.querySelector('p.special'), 'special'); // true

```

### `hashBrowser`

使用SHA-256算法为值创建哈希。 返回Promise。

[SubtleCrypto.digest()](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/digest)

```js

const hashBrowser = val => 
    crypto.subtle.digest('SHA-256', new TextEncoder('utf-8').encode(val)).then(h => {
        let hexes = [],
            view = new DataView(h);
        for (let i = 0; i < view.byteLength; i += 4)
            hexes.push(('00000000' + view.getUint32(i).toString(16)).slice(-8));
        return hexes.join('');
    });
hashBrowser(JSON.stringify({ a: 'a', b: [1, 2, 3, 4], foo: { c: 'bar' } })).then(console.log); // '04aa106279f5977f59f9067fa9712afc4aedc6f5862a8defc34552d8c7206393'

// or
const hashBrowser = val => {
    const msgUint8 = new TextEncoder().encode(val);                           // encode as (utf-8) Uint8Array
    return crypto.subtle.digest('SHA-256', msgUint8);           // hash the message 
};
hashBrowser(JSON.stringify({ a: 'a', b: [1, 2, 3, 4], foo: { c: 'bar' } })).then(h => {
    const hashArray = Array.from(new Uint8Array(h));                     // convert buffer to byte array
    const hashHex = hashArray.map(b => b.toString(16).padStart(2, '0')).join(''); // convert bytes to hex string
    return hashHex;
}).then(console.log); 

// or
async function hashBrowser(message) {
  const msgUint8 = new TextEncoder().encode(message);                           // encode as (utf-8) Uint8Array
  const hashBuffer = await crypto.subtle.digest('SHA-256', msgUint8);           // hash the message
  const hashArray = Array.from(new Uint8Array(hashBuffer));                     // convert buffer to byte array
  const hashHex = hashArray.map(b => b.toString(16).padStart(2, '0')).join(''); // convert bytes to hex string
  return hashHex;
}
const digestHex = await hashBrowser(JSON.stringify({ a: 'a', b: [1, 2, 3, 4], foo: { c: 'bar' } }));
console.log(digestHex); 

```

### `hide`

`NodeList.prototype.forEach()`

隐藏所有指定的元素。

```js

const hide = (...el) => [...el].forEach(e => (e.style.display = 'none'));
hide(document.querySelectorAll('img')); // Hides all <img> elements on the page

```

### `Javascriot`如何复制文本

一个普遍的需求是单击复制文本，`Javascript`可以通过五个步骤做到这点。

- 创建一个\<textarea\>,设置为要复制到剪切板的字符串
- 将\<textarea\>添加到`document`中
- 使用`HTMLInputElement.select()`选择\<textarea\>内容
- 使用`Document.execCommand('copy')`将\<textarea\>内容复制到剪切板
- 删除\<textarea\>

```js

const copyToClipboard = str => {
    const el = document.createElement('textarea');
    el.value = str;
    document.body.appendChild(el);
    el.select();
    document.execCommand('copy');
    document.body.removeChild(el);
};

```

```js

// 前面已经有过的例子
const copyToClipboard = str => {
    const el = document.createElement('textarea');
    el.value = str;
    el.setAttribute('readonly', '');
    el.style.position = 'absolute';
    el.style.left = '-9999px';
    document.body.appendChild(el);
    el.select();
    document.execCommand('copy');
    document.body.removeChild(el);
};

```

### 如何使用`Javascript`修改url时不重新加载页面？

使用`History API`

```js

const nextURL = 'https://my-website.com/page_b';
const nextTitle = 'My new page title';
const nextState = { additionalInformation: 'Updated the URL with JS' };

// This will create a new entry in the browser's history, without reloading
window.history.pushState(nextState, nextTitle, nextURL);

// This will replace the current entry in the browser's history, without reloading
window.history.replaceState(nextState, nextTitle, nextURL);

```

使用`Location API`

```js

// Current URL: https://my-website.com/page_a
const nextURL = 'https://my-website.com/page_b';

// This will create a new entry in the browser's history, reloading afterwards
window.location.href = nextURL;

// This will replace the current entry in the browser's history, reloading afterwards
window.location.assign(nextURL);

// This will replace the current entry in the browser's history, reloading afterwards
window.location.replace(nextURL);

```

### `httpDelete`

对传递的URL进行DELETE请求。

```js

const httpDelete = (url, callback, err = console.error) => {
    const request = new XMLHttpRequest();
    request.open('DELETE', url, true);
    request.onload = () => callback(request);
    request.onerror = () => err(request);
    request.send();
};
httpDelete('https://jsonplaceholder.typicode.com/posts/1', request => {
  console.log(request.responseText);
}); /*
Logs: {}
*/

```

### `httpGet`

向传递的URL发出GET请求。

```js

const httpGet = (url, callback, err = console.error) => {
    const request = new XMLHttpRequest();
    request.open('GET', url, true);
    request.onload = () => callback(request.responseText);
    request.onerror = () => err(request);
    request.send();
};
httpGet(
    'https://jsonplaceholder.typicode.com/posts/1',
    console.log
); /*
Logs: {
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
}
*/

```

### `httpPost`

向传递的URL发出POST请求。

```js

const httpPost = (url, data, callback, err = console.error) => {
    const request = new XMLHttpRequest();
    request.open('POST', url, true);
    request.setRequestHeader('Content-type', 'application/json; charset=utf-8');
    request.onload = () => callback(request.responseText);
    request.onerror = () => err(request);
    request.send(data);
};
const newPost = {
    userId: 1,
    id: 1337,
    title: 'Foo',
    body: 'bar bar bar'
};
const data = JSON.stringify(newPost);
httpPost(
    'https://jsonplaceholder.typicode.com/posts',
    data,
    console.log
); /*
Logs: {
  "userId": 1,
  "id": 1337,
  "title": "Foo",
  "body": "bar bar bar"
}
*/
httpPost(
    'https://jsonplaceholder.typicode.com/posts',
    null, // does not send a body
    console.log
); /*
Logs: {
  "id": 101
}
*/

```

### `httpPut`

向传递的URL发出PUT请求。

```js

const httpPut = (url, data, callback, err = console.error) => {
    const request = new XMLHttpRequest();
    request.open('PUT', url, true);
    request.setRequestHeader('Content-type', 'application/json; charset=utf-8');
    request.onload = () => callback(request);
    request.onerror = () => err(request);
    request.send(data);
};
// const password = 'fooBaz';
const data = JSON.stringify({
    id: 1,
    title: 'foo',
    body: 'bar',
    userId: 1
});
httpPut('https://jsonplaceholder.typicode.com/posts/1', data, request => {
    console.log(request.responseText);
}); /*
Logs: {
  id: 1,
  title: 'foo',
  body: 'bar',
  userId: 1
}
*/

```

### `httpsRedirect`

如果页面当前位于HTTP中，则将其重定向到HTTPS。同时，按返回按钮不会将其带回到HTTP页面，因为历史记录已被替换。

```js

const httpsRedirect = () => {
    if (location.protocol !== 'https:') location.replace('https://' + location.href.split('//')[1]);
};
httpsRedirect(); // If you are on http://mydomain.com, you are redirected to https://mydomain.com

```

### `insertAfter`

在指定元素后面插入`html`

[insertAdjacentHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/insertAdjacentHTML)

```html

<!-- beforebegin -->
<p>
  <!-- afterbegin -->
  foo
  <!-- beforeend -->
</p>
<!-- afterend -->

```

```js

const insertAfter = (el, htmlString) => el.insertAdjacentHTML('afterend', htmlString);
insertAfter(document.getElementById('myId'), '<p>after</p>');
// <div id="myId">...</div> <p>after</p>

```

### `isBrowser`

确定当前运行时环境是否是浏览器，以便前端模块可以在服务器（节点）上运行而不会引发错误。

```js

const isBrowser = () => ![typeof window, typeof document].includes('undefined');
isBrowser(); // true (browser)
isBrowser(); // false (Node)

```

### `isBrowserTabFocused`

如果页面的浏览器`tab`具有焦点，则返回true，否则返回false。

```js

const isBrowserTabFocused = () => !document.hidden;
isBrowserTabFocused(); // true

```

### `listenOnce`

将事件侦听器添加到仅在首次触发事件时才运行回调的元素。

```js

const listenOnce = (el, evt, fn) => el.addEventListener(evt, fn, { once: true });
listenOnce(
    document.getElementById('my-id'),
    'click',
    () => console.log('Hello world')
); // 'Hello world' will only be logged on the first click

```
