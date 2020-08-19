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