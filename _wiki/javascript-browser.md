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

### `nodeListToArray`

将NodeList转换为数组。

```js

const nodeListToArray = nodeList => [...nodeList];
nodeListToArray(document.childNodes); // [ <!DOCTYPE html>, html ]

```

### `observeMutations`

创建并返回一个新的`MutationObserver`它会在指定的DOM发生变化时被调用。

[MutationObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver)

```js

const observeMutations = (element, callback, options) => {
    const observer = new MutationObserver(mutations => mutations.forEach(m => callback(m)));
    observer.observe(
        element,
        Object.assign(
        {
            childList: true,
            attributes: true,
            attributeOldValue: true,
            characterData: true,
            characterDataOldValue: true,
            subtree: true
        },
        options
        )
    );
    return observer;
};
const obs = observeMutations(document, console.log); // Logs all mutations that happen on the page
obs.disconnect(); // Disconnects the observer and stops logging mutations on the page

```

### `off`

从元素中删除事件侦听器。

```js

const off = (el, evt, fn, opts = false) => el.removeEventListener(evt, fn, opts);
const fn = () => console.log('!');
document.body.addEventListener('click', fn);
off(document.body, 'click', fn); // no longer logs '!' upon clicking on the page

```

### `on`

将事件侦听器添加到具有使用事件委托功能的元素。

```js

const on = (el, evt, fn, opts = {}) => {
    const delegatorFn = e => e.target.matches(opts.target) && fn.call(e.target, e);
    el.addEventListener(evt, opts.target ? delegatorFn : fn, opts.options || false);
    if (opts.target) return delegatorFn;
};
const fn = () => console.log('!');
on(document.body, 'click', fn); // logs '!' upon clicking the body
on(document.body, 'click', fn, { target: 'p' }); // logs '!' upon clicking a `p` element child of the body
on(document.body, 'click', fn, { options: true }); // use capturing instead of bubbling

// or

const on = (el, evt, fn, opts = {}) => {
    const delegatorFn = e => e.target.matches(opts.target) && fn.call(e.target, e);
    el.addEventListener(evt, opts.target ? e => {
        return e.target.matches(opts.target) && fn.call(e.target, e)
    } : fn, opts.options || false);
};
var fn = () => console.log('!');
on(document.body, 'click', fn); // logs '!' upon clicking the body
on(document.body, 'click', fn, { target: '.tt' }); // logs '!' upon clicking a `p` element child of the body
on(document.body, 'click', fn, { options: true }); // use capturing instead of bubbling

```

### `onUserInputChange`

每当用户输入类型更改（鼠标或触摸）时，运行回调。 根据输入设备来启用/禁用代码。 此过程是动态的，并且适用于混合设备（例如触摸屏笔记本电脑）

```js

const onUserInputChange = callback => {
    let type = 'mouse',
        lastTime = 0;
    const mousemoveHandler = () => {
        const now = performance.now();
        if (now - lastTime < 20)
        (type = 'mouse'), callback(type), document.removeEventListener('mousemove', mousemoveHandler);
        lastTime = now;
    };
    document.addEventListener('touchstart', () => {
        if (type === 'touch') return;
        (type = 'touch'), callback(type), document.addEventListener('mousemove', mousemoveHandler);
    });
};
onUserInputChange(type => {
  console.log('The user is now using', type, 'as an input method.');
});

```

### `parseCookie`

解析`HTTP Cookie`标头字符串，并返回所有cookie名称-值对的对象。

```js

const parseCookie = str =>
  str
    .split(';')
    .map(v => v.split('='))
    .reduce((acc, v) => {
      acc[decodeURIComponent(v[0].trim())] = decodeURIComponent(v[1].trim());
      return acc;
    }, {});
parseCookie('foo=bar; equation=E%3Dmc%5E2'); // { foo: 'bar', equation: 'E=mc^2' }

```

### `prefersDarkColorScheme`

[matchMedia](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/matchMedia)

如果用户配色方案偏好设置为深色，则返回true，否则返回false。

```js

window.matchMedia('(max-width: 600px)'); // 视口宽度大于600px,返回false，否则true

const prefersDarkColorScheme = () =>
  window && window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches;
prefersDarkColorScheme(); // true

```

### `prefersLightColorScheme`

如果用户配色方案偏好设置为浅色，则返回true，否则返回false。

```js

const prefersLightColorScheme = () =>
  window && window.matchMedia && window.matchMedia('(prefers-color-scheme: light)').matches;
prefersLightColorScheme(); // true

```

### `prefix`

返回浏览器支持的CSS属性的前缀版本（如有必要）。

```js

const prefix = prop => {
    const capitalizedProp = prop.charAt(0).toUpperCase() + prop.slice(1);
    const prefixes = ['', 'webkit', 'moz', 'ms', 'o'];
    const i = prefixes.findIndex(
        prefix => typeof document.body.style[prefix ? prefix + capitalizedProp : prop] !== 'undefined'
    );
    return i !== -1 ? (i === 0 ? prop : prefixes[i] + capitalizedProp) : null;
};
prefix('appearance'); // 'appearance' on a supported browser, otherwise 'webkitAppearance', 'mozAppearance', 'msAppearance' or 'oAppearance'

```

### `recordAnimationFrames`

在每个动画帧上调用提供的回调。

```js

const recordAnimationFrames = (callback, autoStart = true) => {
    let running = true,
        raf;
    const stop = () => {
        running = false;
        cancelAnimationFrame(raf);
    };
    const start = () => {
        running = true;
        run();
    };
    const run = () => {
        raf = requestAnimationFrame(() => {
        callback();
        if (running) run();
        });
    };
    if (autoStart) start();
    return { start, stop };
};
const cb = () => console.log('Animation frame fired');
const recorder = recordAnimationFrames(cb); // logs 'Animation frame fired' on each animation frame
recorder.stop(); // stops logging
recorder.start(); // starts again
const recorder2 = recordAnimationFrames(cb, false); // `start` needs to be explicitly called to begin recording frames

```

### `redirect`

重定向到指定的URL。

```js

const redirect = (url, asLink = true) =>
    asLink ? (window.location.href = url) : window.location.replace(url);
redirect('https://google.com');

```

### `renderElement`

在指定的DOM元素中渲染DOM树。

```js

// test createElement
var myElement = {
    type: 'button',
    props: {
        type: 'button',
        className: 'btn',
        onClick: () => alert('Clicked'),
        children: [
            { props: { nodeValue: 'Click me' } }
        ]
    }
};
var element = document.createElement(myElement.type);
element['type'] = myElement.props.type;
element['className'] = myElement.props.className; 
element // <button type="button" class="btn"></button>

```

```js

const renderElement = ({ type, props = {} }, container) => {
    const isTextElement = !type;
    const element = isTextElement
        ? document.createTextNode('')
        : document.createElement(type);

    //  是否为事件 是：true 否：false
    const isListener = p => p.startsWith('on');
    //  是否是属性 是：true 否：false
    const isAttribute = p => !isListener(p) && p !== 'children';

    Object.keys(props).forEach(p => {
        // 属性赋值
        if(isAttribute(p)) element[p] = props[p];
        // 事件添加
        if(!isTextElement && isListener(p))
            element.addEventListener(p.toLowerCase().slice(2), props[p]);
    });

    // 获取children后进行递归
    if(!isTextElement && props.children && props.children.length)
        props.children.forEach(childElement => renderElement(childElement, element));

    container.appendChild(element);
}

const myElement = {
    type: 'button',
    props: {
        type: 'button',
        className: 'btn',
        onClick: () => alert('Clicked'),
        children: [
        { props: { nodeValue: 'Click me' } }
        ]
    }
};
renderElement(
    myElement,
    document.body
);

```

### `runAsync`

[Web Workers API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API)
[Blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)

使用`Web Worker`在单独的线程中运行一个函数，从而允许长时间运行的函数不会阻塞UI。

```js

// test Blob
var typedArray = GetTheTypedArraySomehow();
var blob = new Blob([typedArray.buffer], {type: 'application/octet-stream'}); // 传入一个合适的 MIME 类型
var url = URL.createObjectURL(blob);
// 会产生一个类似 blob:d3958f5c-0777-0845-9dcf-2cb28783acaf 这样的URL字符串
// 你可以像使用普通 URL 那样使用它，比如用在 img.src 上。

```

```js

const runAsync = fn => {
    const worker = new Worker(
        URL.createObjectURL(new Blob([`postMessage((${fn})());`]), {
            type: 'application/javascript; charset=utf-8'
        }) // MIME类型
    );
    return new Promise((res, rej) => {
        worker.onmessage = ({ data }) => {
            res(data), worker.terminate();
        };
        worker.onerror = err => {
            rej(err), worker.terminate();
        };
    });
};

const longRunningFunction = () => {
  let result = 0;
  for (let i = 0; i < 1000; i++)
    for (let j = 0; j < 700; j++) for (let k = 0; k < 300; k++) result = result + i + j + k;

  return result;
};
/*
  NOTE: Since the function is running in a different context, closures are not supported.
  The function supplied to `runAsync` gets stringified, so everything becomes literal.
  All variables and functions must be defined inside.
*/
runAsync(longRunningFunction).then(console.log); // 209685000000
runAsync(() => 10 ** 3).then(console.log); // 1000
let outsideVariable = 50;
runAsync(() => typeof outsideVariable).then(console.log); // 'undefined'

```

### `scrollToTop`

平滑滚动到页面顶部。

```js

// test requestAnimationFrame
var start = null;
var element = document.getElementById('SomeElementYouWantToAnimate');
element.style.position = 'absolute';

function step(timestamp) {
  if (!start) start = timestamp;
  var progress = timestamp - start;
  element.style.left = Math.min(progress / 10, 200) + 'px';
  if (progress < 2000) {
    window.requestAnimationFrame(step);
  }
}

window.requestAnimationFrame(step);

```

```js

const scrollToTop = () => {
  const c = document.documentElement.scrollTop || document.body.scrollTop;
  if (c > 0) {
    window.requestAnimationFrame(scrollToTop);
    window.scrollTo(0, c - c / 8);
  }
};
scrollToTop();

```

### `serializeCookie`

将Cookie名/值对序列化为Set-Cookie标头字符串。

```js

const serializeCookie = (name, val) => `${encodeURIComponent(name)}=${encodeURIComponent(val)}`;
serializeCookie('foo', 'bar'); // 'foo=bar'

```

### `serializeForm`

将一组表单元素编码为查询字符串。

```js

// test
Array.from([1, 2, 3], x => x + x);
// [2, 4, 6]
Array.from({length: 5}, (v, i) => i);
// [0, 1, 2, 3, 4]

```

```js

const serializeForm = form =>
  Array.from(new FormData(form), field => field.map(encodeURIComponent).join('=')).join('&');
serializeForm(document.querySelector('#form')); // email=test%40email.com&name=Test%20Name

```
