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
- [说说你对`AMD`和`CommonJS`的了解](#说说你对amd和commonjs的了解)
- [`null`、`undefined`和未声明的变量之间有什么区别？如何检查判断这些状态值？](#nullundefined和未声明的变量之间有什么区别如何检查判断这些状态值)
- [什么是闭包（closure），为什么使用闭包？](#什么是闭包closure为什么使用闭包)
- [请说明`.forEach`循环和`.map()`循环的主要区别，它们分别在什么情况下使用？](#请说明foreach循环和map循环的主要区别它们分别在什么情况下使用)
- [匿名函数的典型应用场景是什么？](#匿名函数的典型应用场景是什么)
- [宿主对象（host objects）和原生对象（native objects）的区别是什么？](#宿主对象host-objects和原生对象native-objects的区别是什么)
- [下列语句有什么区别：`function Person(){}`、`var person = Person()`和`var person = new Person()`？](#下列语句有什么区别function-personvar-person--person和var-person--new-person)
- [`.call`和`.apply`有什么区别？](#call和apply有什么区别？)
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

### 说说你对`AMD`和`CommonJS`的了解
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
当你没有提前使用`var`、`let`或`const`声明变量，就为一个变量赋值时，该变量是未声明变量（undeclared variables）。未声明变量会脱离当前作用域，成为全局作用域下定义的变量。在严格模式下，给未声明的变量赋值，会抛出`ReferenceError`错误。和使用全局变量一样，使用未声明变量也是非常不好的做法，应当尽可能避免。要检查判断它们，需要将用到它们的代码放在`try`/`catch`语句中。
```js
function foo() {
  x = 1; // 在严格模式下，抛出 ReferenceError 错误
}
foo();
console.log(x); // 1
```

当一个变量已经声明，但没有赋值时，该变量的值是`undefined`。如果一个函数的执行结果被赋值给一个变量，但是这个函数却没有返回任何值，那么该变量的值是`undefined`。要检查它，需要使用严格相等（`===`）；或者使用`typeof`，它会返回`'undefined'`字符串。请注意，不能使用非严格相等（`==`）来检查，因为如果变量值为`null`，使用非严格相等也会返回`true`。
```js
var foo;
console.log(foo); // undefined
console.log(foo === undefined); // true
console.log(typeof foo === 'undefined'); // true

console.log(foo == null); // true. 错误，不要使用非严格相等！

function bar() {}
var baz = bar();
console.log(baz); // undefined
```

`null`只能被显式赋值给变量。它表示`空值`，与被显式赋值 `undefined` 的意义不同。要检查判断`null`值，需要使用严格相等运算符。请注意，和前面一样，不能使用非严格相等（`==`）来检查，因为如果变量值为`undefined`，使用非严格相等也会返回`true`。
```js
var foo = null;
console.log(foo === null); // true

console.log(foo == undefined); // true. 错误，不要使用非严格相等！
```  

参考
- <https://stackoverflow.com/questions/15985875/effect-of-declared-and-undeclared-variables>
- <https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/undefined>

[[↑] 回到顶部](#目录)

### 什么是闭包（closure），为什么使用闭包？
闭包是函数和声明该函数的词法环境的组合。词法作用域中使用的域，是变量在代码中声明的位置所决定的。闭包是即使被外部函数返回，依然可以访问到外部（封闭）函数作用域的函数。
> 注：闭包是指有权访问另一个函数作用域中的变量的函数。`《Javascript高级程序设计（第三版）》7-2节 178页`
> 注：有关闭包中的`this`。`《Javascript高级程序设计（第三版）》7-2-2节 182页`
```js
var name = "The Window"
var object = {
    name: "My Object",
    getNameFunc: function() {
        return function() {
            return this.name
        }
    }
}
console.log(object.getNameFunc()()) // "The Window" (在非严格模式下)
```
```js
var name = "The Window"
var object = {
    name: "My Object",
    getNameFunc: function() {
        var that = this
        return function() {
            return that.name
        }
    }
}
console.log(object.getNameFunc()()) // "My Object"
```

*为什么使用闭包？*
- 利用闭包实现数据私有化或模拟私有方法。这个方式也称为[模块模式（module pattern）](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript)。
- [部分参数函数（partial applications）柯里化（currying）](https://medium.com/javascript-scene/curry-or-partial-application-8150044c78b8#.l4b6l1i3x).  

参考
- <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures>
- <https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36>

[[↑] 回到顶部](#目录)

### 请说明`.forEach`循环和`.map()`循环的主要区别，它们分别在什么情况下使用？
*`forEach`*
- 遍历数组中的元素。
- 为每个元素执行回调。
- 无返回值。
```js
const a = [1, 2, 3];
const doubled = a.forEach((num, index) => {
  // 执行与 num、index 相关的代码
});
// doubled = undefined
```
*`map`*
- 遍历数组中的元素
- 通过对每个元素调用函数，将每个元素“映射（map）”到一个新元素，从而创建一个新数组。
```js
const a = [1, 2, 3];
const doubled = a.map((num) => {
  return num * 2;
});
// doubled = [2, 4, 6]
```
`.forEach`和`.map()`的主要区别在于`.map()`返回一个新的数组。如果你想得到一个结果，但不想改变原始数组，用`.map()`。如果你只需要在数组上做迭代修改，用`forEach`。  

参考
- <https://codeburst.io/javascript-map-vs-foreach-f38111822c0f>

[[↑] 回到顶部](#目录)

### 匿名函数的典型应用场景是什么？
匿名函数可以在 IIFE 中使用，来封装局部作用域内的代码，以便其声明的变量不会暴露到全局作用域。
```js
(function () {
  // 一些代码。
})();
```
匿名函数可以作为只用一次，不需要在其他地方使用的回调函数。当处理函数在调用它们的程序内部被定义时，代码具有更好地自闭性和可读性，可以省去寻找该处理函数的函数体位置的麻烦。
```js
setTimeout(function () {
  console.log('Hello world!');
}, 1000);
```
匿名函数可以用于函数式编程或 Lodash（类似于回调函数）。
```js
const arr = [1, 2, 3];
const double = arr.map(function (el) {
  return el * 2;
});
console.log(double); // [2, 4, 6]
```  

参考
- <https://www.quora.com/What-is-a-typical-usecase-for-anonymous-functions>
- <https://stackoverflow.com/questions/10273185/what-are-the-benefits-to-using-anonymous-functions-instead-of-named-functions-fo>

[[↑] 回到顶部](#目录)

### 宿主对象（host objects）和原生对象（native objects）的区别是什么？

原生对象是由 ECMAScript 规范定义的 JavaScript 内置对象，比如`String`、`Math`、`RegExp`、`Object`、`Function`等等。

宿主对象是由运行时环境（浏览器或 Node）提供，比如`window`、`XMLHTTPRequest`等等。  

参考

- <https://stackoverflow.com/questions/7614317/what-is-the-difference-between-native-objects-and-host-objects>

[[↑] 回到顶部](#目录)

### 下列语句有什么区别：`function Person(){}`、`var person = Person()`和`var person = new Person()`？
这个问题问得很含糊。我猜这是在考察 JavaScript 中的构造函数（constructor）。从技术上讲，`function Person(){}`只是一个普通的函数声明。使用 PascalCase 方式命名函数作为构造函数，是一个惯例。
> 注：函数声明，首字母大写按惯例当作构造函数使用，会变量提升。

`var person = Person()`将`Person`以普通函数调用，而不是构造函数。如果该函数是用作构造函数的，那么这种调用方式是一种常见错误。通常情况下，构造函数不会返回任何东西，因此，像普通函数一样调用构造函数，只会返回`undefined`赋给用作实例的变量。
> 注：函数定义表达式，首字母大写按惯例当作构造函数使用，不会变量提升。

`var person = new Person()`使用`new`操作符，创建`Person`对象的实例，该实例继承自`Person.prototype`。另外一种方式是使用`Object.create`，例如：Object.create(Person.prototype)`。
> 注：构造函数的使用  

参考
- <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new>

[[↑] 回到顶部](#目录)

### `.call`和`.apply`有什么区别？
`.call`和`.apply`都用于调用函数，第一个参数将用作函数内 this 的值。然而，`.call`接受逗号分隔的参数作为后面的参数，而`.apply`接受一个参数数组作为后面的参数。一个简单的记忆方法是，从`call`中的 C 联想到逗号分隔（comma-separated），从`apply`中的 A 联想到数组（array）。
```js
function add(a, b) {
  return a + b;
}

console.log(add.call(null, 1, 2)); // 3
console.log(add.apply(null, [1, 2])); // 3
```

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


