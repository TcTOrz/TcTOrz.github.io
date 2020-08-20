---
layout: post
title: Javascript responsive principle
categories: [javascript, responsive-principle, vue]
description: javascript responsive principle
keywords: JavaScript, Responsive-principle, Vue
topmost: false
---

### 响应式原理

#### 观察者模式

观察者模式是一种实现一对多关系解耦的行为设计模式。它主要涉及两个角色：观察目标、观察者。
特点是：观察者直接订阅观察目标，观察目标一做出通知，观察者就要进行处理（这也是观察者模式区别于发布订阅模式的最大区别）

> 有些地方说观察者模式和发布/订阅模式是一样的，其实是不完全等同的，发布/订阅模式中，其解耦能力更近一步，发布者只要做好消息的发布，而不关心消息有没有订阅者订阅。而观察者模式则要求两端同时存在。

观察者模式

```js

// 观察者集合
class ObserverList
{
    constructor() {
        this.list = []
    }

    add(obj) {
        this.list.push(obj)
    }

    removeAt(index) {
        this.list.splice(index, 1)
    }

    count() {
        return this.list.length
    }

    get(index) {
        if(index < 0 || index >= this.count()) return;
        return this.list[index]
    }

    indexOf(obj, start = 0) {
        let pos = start
        while(pos < this.count) {
            if(this.list[pos] === obj) {
                return pos
            }
            pos++
        }
        return -1
    }
}

// 观察者类
class Observer 
{
    constructor(fn) {
        this.update = fn
    }
}

// 观察目标类
class Subject
{
    constructor() {
        this.observers = new ObserverList()
    }

    addObserver(observer) {
        this.observers.add(observer)
    }

    removeObserver(observer) {
        this.observers.removeAt(
            this.observers.indexOf(observer)
        )
    }

    notify(context) {
        const count = this.observers.count()
        for (let i = 0; i < count; ++i) {
            this.observers.get(i).update(context)
        }
    }
}

const observer = new Observer((newval) => {
    console.log(`最新值是${newval}`)
})
const subject = new Subject()
subject.addObserver(observer)

subject.notify('Hello, world'); // 最新值是Hello, world

```

#### 响应式原理

Vue.js的响应式原理依赖于`Object.defineProperty`

将data变成可观察的

```js

function observe(value, cb) {
    Object.keys(value).forEach(key => defineReactive(value, key, value[key], cb))
}

function defineReactive(obj, key, val, cb) {
    Object.defineProperty(obj, key, {
        enumerable: true,
        configure: true,
        get: () => {
            return val
        },
        set: newVal => {
            val = newVal
            cb()
        }
    })
}

function _proxy(data) {
    Object.keys(data).forEach(key => {
        Object.defineProperty(this, key, {
            enumerable: true,
            configure: true,
            get: () => {
                return this._data[key]
            },
            set: (val) => {
                this._data[key] = val
            }
        })
    })
} 

class Vue
{
    constructor(options) {
        this._data = options.data
        observe(this._data, options.render)

        _proxy.call(this, options.data)
    }
}

let app = new Vue({
    el: '#app',
    data: {
        a: 1,
        b: 2
    },
    render() {
        console.log("render")
    }
})

// app._data.a
// app.a

```

### 依赖收集

```js

new Vue({
    template: ` <div>
                    <span>{{a}}</span>
                    <span>{{b}}</span>
                </div>`,
    data: {
        a: 'a',
        b: 'b',
        c: 'c'
    }
})

```

按照之前响应式原理中的方法会出现一个问题，`c`在实际模板中没有用到，然而数据修改(`this.c = d`)时，同样会触发c的setter导致执行渲染，这显然不正确。

所以在最开始进行一次render，那么所有被渲染所依赖的data数据会被getter收集到Dep中的subs去。再对data数据修改时settter只会触发Dep的subs函数。

```js

class Dep
{
    constructor() {
        this.subs = []
    }

    addSub(sub: Watcher) {
        this.subs.push(sub)
    }

    removeSub(sub: Watcher) {
        remove(this.subs, sub)
    }

    notify() {
        // stabilize the subcriber list first
        const subs = this.subs.slice()
        for (let i = 0, l = subs.length; i < l; i++) {
            subs[i].update()
        }
    }
}

function remove(arr, item) {
    if(arr.length) {
        const index = arr.indexOf(item)
        if(index > -1) {
            arr.splice(index, 1)
        }
    }
}

```

订阅者，当依赖收集的时候会addSub到sub中，在修改data中的数据的时候会触发dep对象的notify,通知所有watcher对象去修改对应视图。

```js

class Watcher
{
    constructor(vm, expOrFn, cb, options) {
        this.cb = cb
        this.vm = vm

        // 这里将观察者本身赋值给全局的target，只有被target标记过的才会进行依赖收集
        Dep.target = this

        // 触发渲染操作进行依赖收集
        this.cb.call(this.vm)
    }

    update() {
        this.cb.call(this.vm)
    }
}

```

开始依赖收集

```js

class Vue
{
    constructor(options) {
        this._data = options.data
        observe(this._data, options.render);
        let watcher = new Watcher(this, );
    }
}

function defineReactive (obj, key, val, cb) {
    // 在闭包内存储一个Dep对象
    const dep = new Dep();

    Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get: ()=>{
            if (Dep.target) {
                // Watcher对象存在全局的Dep.target中
                dep.addSub(Dep.target);
            }
        },
        set: newVal=> {
            // 只有之前addSub中的函数才会触发
            dep.notify();
        }
    })
}

Dep.target = null;

```

将观察者Watcher实例赋值给全局的Dep.target，然后触发render操作只有被Dep.target标记过的才会进行依赖收集。有Dep.target的对象会将Watcher的实例push到subs中，在对象被修改触发setter操作的时候dep会调用subs中的Watcher实例的update方法进行渲染。


```js

class Dep
{
    static target = null
    constructor() {
        // 订阅集合
        this.subs = []
    }

    addSub(sub) {
        this.subs.push(sub)
    }

    removeSub(sub) {
        if( this.subs.length ) {
            const index = this.subs.indexOf(sub)
            if( index > -1 ) {
                return this.subs.splice(index, 1)
            }
        }
    }

    notify() {
        // 稳定订阅列表
        const subs = this.subs.concat()
        for (let i = 0, l = subs.length; i < l.length; i++) {
            subs[i].update()
        }
    }
}

// 订阅者
class Watcher
{
    constructor(vm, expOrFn, cb, options) {
        this.vm = vm
        this.cb = cb

        // 将观察者本身赋值给静态属性target，只有被target标记过的才会经行依赖收集
        Dep.target = this
        // 触发渲染操作
        // this.cb.call(this.vm)
    }

    update() {
        this.cb.call(this.vm)
    }
}

function defineReactive(obj, key, val, cb) {
    // 闭包存储Dep对象
    const dep = new Dep();

    Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get: () => {
            if (Dep.target) {
                // Watcher对象存在全局的Dep.target中
                dep.addSub(Dep.target)
                // console.log(Dep.target)
                // Dep.target = null;
                return val
            }
        },
        set: newVal=> {
            // 只有之前addSub中的函数才会触发
            // val = newVal
            dep.notify()
        }
    })
}

function observe(value, cb) {
    Object.keys(value).forEach((key) => defineReactive(value, key, value[key] , cb))
}

// 开始依赖收集
class Vue
{
    constructor(options) {
        this._data = options.data
        observe(this._data, options.render)
        let watcher = new Watcher(this, '', options.render, options)
        watcher.update()
    }
}

let app = new Vue({
    el: '#app',
    data: {
        text: 'text',
        text2: 'text2'
    },
    render(){
        console.log("render");
    }
})

console.log(app._data.text2)
console.log(app._data.text)
app._data.text3 = 'text3'
console.log(app._data.text3)

```

另一个例子：

```js

class Dep
{
    static target = null
    constructor() {
        this.deps = []
    }

    depend() {
        if(Dep.target && this.deps.indexOf(Dep.target) === -1) {
            this.deps.push(Dep.target)
        }
    }

    notify() {
        this.deps.forEach(dep => dep())
    }
}

class Observable
{
    constructor(obj) {
        return this.walk(obj)
    }

    walk(obj) {
        const keys = Object.keys(obj)
        keys.forEach(key => {
            this.defineReactive(obj, key, obj[key])
        })
        return obj
    }

    defineReactive(obj, key, val) {
        const dep = new Dep()
        Object.defineProperty(obj, key, {
            get() {
                dep.depend() // 获取对象属性时，将Dep.target加到Dep.deps中
                return val
            },
            set(newVal) {
                val = newVal
                dep.notify() // 在设置对象的属性时，执行Dep.deps中的方法
            }
        })
    }
}

class Watcher
{
    constructor(obj, key, onComputedUpdate) {
        this.obj = obj
        this.key = key
        this.onComputedUpdate = onComputedUpdate
        return this.defineComputed()
    }

    defineComputed() {
        const self = this

        Object.defineProperty(self.obj, self.key, {
            get() {
                Dep.target = self.onComputedUpdate;//将依赖函数赋给Dep.target，以便对应的依赖属性进行依赖收集
                //执行依赖函数（执行依赖函数时，会触发依赖属性的get，此时将依赖函数收集到Dep.deps）
                const val =  self.onComputedUpdate();
                //依赖属性执行完毕后，将Dep.target置null，以便下次或者其他计算属性使用。
                Dep.target = null;
                //返回计算属性的值
                return val
            },
            set() {
                console.error('计算属性无法被赋值！')
            }
        })
    }
}

/**
  * 先把对象变为可观测对象
  */
const hero = new Observable({
    health: 3000,
    IQ: 150
})

new Watcher(hero, 'type', () => {
    return hero.health > 4000 ? '坦克' : '脆皮'
})

console.log(`英雄初始类型：${hero.type}`)
hero.health = 5000;
console.log(`英雄加强后的类型：${hero.type}`);

```