---
layout: post
title: Javascript面试题
categories: [javascript, 面试]
description: 自己遇见的一些题目
keywords: JavaScript, interview
topmost: false
---

## 目录

- [请解释事件委托（event delegation）](#请解释事件委托event-delegation)
- [请简述`Javascript`中的`this`](#请简述javascript中的this)
- [请解释原型继承(prototypal inheritance) 的工作原理](#请解释原型继承prototypal-inheritance的工作原理)
- [说说你对 AMD 和 CommonJS 的了解](#说说你对-AMD-和-CommonJS-的了解)
- [`null`、`undefined`和未声明的变量之间有什么区别？如何检查判断这些状态值？](#null、undefined和未声明的变量之间有什么区别？如何检查判断这些状态值？)

- [打印网页标签个数以及标签最多的一组数据](#打印网页标签个数以及标签最多的一组数据)
<!-- * TOC
{:toc} -->

### 请解释事件委托（event delegation）
事件委托是将事件监听器添加到父元素，而不是每个子元素单独设置监听。当触发子元素时，事件会冒泡到父元素，监听器就会触发。
这种技术的好处是：
- 减少内存的占用，只需要一个父元素的事件处理程序，而不必为每个后代添加事件处理程序
- 无需从已删除的元素中解绑处理程序，也无需将处理程序绑定到新元素上  

参考
- <https://davidwalsh.name/event-delegate>
- <https://stackoverflow.com/questions/1687296/what-is-dom-event-delegation>  

[[↑] 回到顶部](#目录)

### 请简述`Javascript`中的`this`
JS中的`this`是一个相对复杂的概念，不是简单几句能解释清楚的。粗略的讲，函数的调用方式决定了this的值。我阅读了网上很多关于`this`的文章[Arnav Aggrawal](https://medium.com/@arnav_aggarwal)写的比较清楚。`this`取值符合以下规则：
1. 在调用函数时使用`new`关键字，函数内的this是一个全新的对象。
2. 如果`apply`、`call`或`bind`方法调用、创建一个函数，函数内的this就是作为参数传入这些方法的对象。
3. 当函数作为对象里的方法被调用时，函数内的`this`是调用该函数的对象。比如：当obj.method()被调用时，函数内的this将绑定到obj对象
4. 如果调用函数不符合上述规则，那么`this`的值指向全局对象(global object)。浏览器环境下指向`window`对象，但在严格模式下(`use strict`)，`this`的值为`undefined`。
5. 如果符合上述多个规则，则较高的规则(1最高，4最低)将决定`this`值。
6. 如果该函数是ES2015中的箭头函数，将忽略上面所有规则，`this`将被设置为它被创建时的上下文。
想获得更深入的解释，请查看[他在 Medium 上的文章](https://codeburst.io/the-simple-rules-to-this-in-javascript-35d97f31bde3)。  

参考
- <https://codeburst.io/the-simple-rules-to-this-in-javascript-35d97f31bde3>
- <https://stackoverflow.com/a/3127440/1751946>  

[[↑] 回到顶部](#目录)

### 请解释原型继承(prototypal inheritance)的工作原理
这是一个非常常见的Javascript问题。所有JS对象都有一个__proto__属性，指向它的原型对象。当试图访问一个对象的属性时，如果没有在该对象上找到，它会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或达到原型的末尾(Object.prototype)。这种行为是在模拟经典的继承，[但是与其说是继承，不如说是委托(delegation)](https://davidwalsh.name/javascript-objects)  

参考
- <https://www.quora.com/What-is-prototypal-inheritance/answer/Kyle-Simpson>
- <https://davidwalsh.name/javascript-objects>  

[[↑] 回到顶部](#目录)

### 说说你对 AMD 和 CommonJS 的了解
存疑  
它们都是实现模块体系的方式，直到ES2015出现之前，Javascript一直没有模块体系。`CommonJS`是同步的，而`AMD`（Asynchronous Module Definitions）从全称中可以明显看出是异步的。`CommonJS`的设计是为服务器端开发考虑的，而`AMD`支持异步加载模块，更适合浏览器。

我发现`AMD`的语法非常冗长，`CommonJS`更接近其他语言`import`声明语句用法习惯。大多数情况下，我认为`AMD`没有使用的必要，因为如果把所有`Javascript`都捆绑进一个文件中，将无法得到异步加载的好处。此外，`CommonJS`语法上更接近`Node`编写模块的风格，在前后端都使用`Jaascript`开发之间进行切换时，语境的切换开销较小。

我很高兴看到`ES2015`的模块加载方案同时支持同步和异步，我们终于可以只使用一种方案了。虽然它尚未在浏览器和`Node`中完全推出，但是我们可以使用代码转换工具进行转换。  

参考
- <https://exploringjs.com/es6/ch_modules.html>
- <https://segmentfault.com/q/1010000005680390>
- <https://auth0.com/blog/javascript-module-systems-showdown/>
- <https://stackoverflow.com/questions/16521471/relation-between-commonjs-amd-and-requirejs>


[[↑] 回到顶部](#目录)

### 请解释下面代码为什么不能用作`IIFE`：`function foo(){ }();`，需要作出哪些修改才能使其成为`IIFE`？
`IIFE`（Immediately Involved Function Expressions）代表立即执行函数。`Javascript`解析器将`function foo(){ }();`解析成`function foo(){ }`和`();`。其中前者是函数声明；后者是一对括号试图调用一个函数，却没有指定名称，因此它会抛出`Uncaught SyntaxError： Unexpected token`的错误。

修改方法是：再添加一对括号，形式上有两种：`(function foo(){ })()`和`(function foo(){ }())`。以上函数不会暴露到全局作用域，如果不需要在函数内部引用自身，可以省略函数的名称。

你可能会用到`void`操作符：`void function foo(){ }();`。但是，这种做法是有问题的。表达式的值是`undefined`,所以如果你的`IIFE`有返回值，不要用这种做法。例如：
```js
const foo = void(function bar(){
    return 'foo';
})();
console.log(foo); // undefined
```
> 注：`function`之前的左圆括号是必需的，因为如果不写这个左圆括号，`Javascript`解释器会试图将关键字`function`解析为函数声明语句。使用圆括号`Javascript`解释器才会正确地将其解析为函数定义表达式。使用圆括号是习惯用法，尽管有时候没有必要也不应当省略。`《Javascript权威指南 第六版》 8-5节 181 页`  

参考
- <http://lucybain.com/blog/2014/immediately-invoked-function-expression/>
- <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/void> 


[[↑] 回到顶部](#目录)

### `null`、`undefined`和未声明的变量之间有什么区别？如何检查判断这些状态值？

[[↑] 回到顶部](#目录)

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

[[↑] 回到顶部](#目录)


