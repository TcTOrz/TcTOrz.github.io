---
layout: post
title: Javascript responsive principle
categories: [javascript, responsive-principle, vue]
description: javascript responsive principle
keywords: JavaScript, Responsive-principle, Vue
topmost: false
---

### 响应式原理

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

按照之前响应式原理中的方法会出现一个问题，`c`在实际模板做没有用到，然而数据修改(`this.c = d`)时，同样会触发c的setter导致执行渲染，这显然不正确。

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