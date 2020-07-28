---
layout: wiki
title: Typescript
categories: [Typescript]
description: 学习typescript,及时记录
keywords: Typescript
---

### 混入

```ts

class Mammal
{
    breathe(): string {
        return "I'm alive";
    }
}

class WingedAnimal
{
    fly(): string {
        return "I can fly";
    }
}

class Bat implements Mammal, WingedAnimal
{
    breathe: ()=>string;
    fly: ()=>string;
}

// 范式
// 限制：1、只能在继承树上继承一级的方法和属性
//      2、如果两个或更多的父类包含了同名方法，那么只会继承传入applyMixins方法中derivedCtors数组中最后一个类中的该方法。
function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor=> {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name=> {
            if(name != 'contructor') {
                derivedCtor.prototype[name] = baseCtor.prototype[name];
            }
        });
    });
}

applyMixins(Bat, [Mammal, WingedAnimal]);
var bat = new Bat();
console.log(bat.breathe()); // I'm alive
console.log(bat.fly()); // I can fly

```