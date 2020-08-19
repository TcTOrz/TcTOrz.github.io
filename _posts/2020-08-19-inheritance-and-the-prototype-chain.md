---
layout: post
title: Javascript Inheritance and the prototype chain
categories: [javascript, inheritance, prototype-chain]
description: javascript继承与原型链
keywords: JavaScript, inheritance the prototype chain
topmost: false
---

## `Inheritance and the prototype chain`

每个实例对象(`object`)都有一个私有属性(`__proto__`)指向他的构造函数的原型对象(`prototype`)。

```js

function Obj() {}
new Obj().__proto__ === Obj.prototype   // true

```

该原型对象也有一个自己的原型对象(`__proto__`)，层层向上直到一个对象的原型对象为`null`。根据定义，`null`没有原型，并作为这个原型链中的最后一个环节。

```js

new Obj().__proto__.__proto__   // Object.prototype
new Obj().__proto__.__proto__.__proto__ // null

```

### 基于原型链的继承

#### 继承属性

`JavaScript`对象是动态的属性“包”（指其自己的属性）。`JavaScript`对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。

```js

let F = function() {
    this.a = 1
    this.b = 2
}

let o = new F

F.prototype.b = 3
F.prototype.c = 4

// 注意：不能写成F.prototype = {b: 3, c: 4}。会打破原型链。

o.__proto__ // {b: 3, c: 4, constructor: ƒ}
o.__proto__.constructor === F   // true
o.__proto__.__proto__ === Object.prototype  // true

// 原型链
{a: 1, b: 2} ----> {b: 3, c: 4} ----> Object.prototype ----> null

o.a // 1
o.b // 2 原型上也有一个属性b，但是不会被访问到，这种情况被称作“属性遮蔽(property shadowing)”
o.c // 4
o.d // undefined

```

#### 继承方法

`JavaScript`并没有其他基于类的语言所定义的“方法”。在`JavaScript`里，任何函数都可以添加到对象上作为对象的属性。函数的继承与其他的属性继承没有差别，包括上面的“属性遮蔽”（这种情况相当于其他语言的方法重写）。

当继承的函数被调用时，`this`指向的是当前继承的对象，而不是继承的函数所在的原型对象。

```js

const o = {
    a: 2,
    m: function() {
        return this.a + 1;
    }
}

o.m()   // 3 this指向o

// p继承自o
const p = Object.create(o)

p.a = 4 // p自身属性a

p.m()   // 5 this指向p

```

### 在`JavaScript`中使用原型

正如之前提到的，在`JavaScript`中，函数(`function`)是允许拥有属性的。所有的函数会有一个特别的属性 —— `prototype`。

```js

function doSomething(){}
console.log(doSomething.prototype)

/* 
    {
        constructor: ƒ doSomething(),
        __proto__: {
            constructor: ƒ Object(),
            hasOwnProperty: ƒ hasOwnProperty(),
            isPrototypeOf: ƒ isPrototypeOf(),
            propertyIsEnumerable: ƒ propertyIsEnumerable(),
            toLocaleString: ƒ toLocaleString(),
            toString: ƒ toString(),
            valueOf: ƒ valueOf()
        }
    }
 */

```

我们可以给doSomething函数的原型对象添加新属性，如下：

```js

function doSomething(){}
doSomething.prototype.foo = "bar"
console.log(doSomething.prototype)

/* 
    {
        foo: "bar",
        constructor: ƒ doSomething(),
        __proto__: {
            constructor: ƒ Object(),
            hasOwnProperty: ƒ hasOwnProperty(),
            isPrototypeOf: ƒ isPrototypeOf(),
            propertyIsEnumerable: ƒ propertyIsEnumerable(),
            toLocaleString: ƒ toLocaleString(),
            toString: ƒ toString(),
            valueOf: ƒ valueOf()
        }
    }
 */

```

new一下

```js

function doSomething(){}
doSomething.prototype.foo = "bar" // add a property onto the prototype
var doSomeInstancing = new doSomething()
doSomeInstancing.prop = "some value" // add a property onto the object
console.log(doSomeInstancing)

/* 
    {
        prop: "some value",
        __proto__: {
            foo: "bar",
            constructor: ƒ doSomething(),
            __proto__: {
                constructor: ƒ Object(),
                hasOwnProperty: ƒ hasOwnProperty(),
                isPrototypeOf: ƒ isPrototypeOf(),
                propertyIsEnumerable: ƒ propertyIsEnumerable(),
                toLocaleString: ƒ toLocaleString(),
                toString: ƒ toString(),
                valueOf: ƒ valueOf()
            }
        }
    }
 */

```

输出结果

```js

function doSomething(){}
doSomething.prototype.foo = "bar"
var doSomeInstancing = new doSomething()
doSomeInstancing.prop = "some value";
console.log("doSomeInstancing.prop:      " + doSomeInstancing.prop)
console.log("doSomeInstancing.foo:       " + doSomeInstancing.foo)
console.log("doSomething.prop:           " + doSomething.prop)
console.log("doSomething.foo:            " + doSomething.foo)
console.log("doSomething.prototype.prop: " + doSomething.prototype.prop)
console.log("doSomething.prototype.foo:  " + doSomething.prototype.foo)

/* 
    doSomeInstancing.prop:      some value
    doSomeInstancing.foo:       bar
    doSomething.prop:           undefined
    doSomething.foo:            undefined
    doSomething.prototype.prop: undefined
    doSomething.prototype.foo:  bar
 */

```