---
layout: post
title: Javascript算法题
categories: [javascript, algorithms]
description: 一些和js相关的算法题
keywords: JavaScript, Algorithms
topmost: false
---

* TOC
{:toc} 

## Conversions

### 十进制转二进制。
- 如果 10
- 10/2=5 余 0
- 5/2=2 余 1
- 2/2=1 余 0
- 1/2=0 余 1
- 则 9=1010

```js

function decimalToBinary(num) {
    var bin = []
    while( num > 0 ) {
        bin.unshift( num % 2 )
        num >>= 1
    }
    console.log(`The decimal in binary is: ${bin.join('')}`)
}

decimalToBinary(2) // 10
decimalToBinary(7) // 111
decimalToBinary(15) // 1111

```

[[↑] 回到顶部](#目录)

### 十进制转十六进制。
- 如果 150
- 150/16=9 余6
- 9/16=0 余9
- 则 150=96

```js

function intToHex(num) {
    switch (num) {
        case 10: return 'A'
        case 11: return 'B'
        case 12: return 'C'
        case 13: return 'D'
        case 14: return 'E'
        case 15: return 'F'
    }
    return num
}

function decimalToHex(num) {
    var hex = []
    while( num > 15 ) {
        hex.unshift(intToHex(num % 16))
        num = Math.floor(num / 16)
    }
    return intToHex(num) + hex.join('')
}

decimalToHex(14) // E
decimalToHex(22) // 16
decimalToHex(10) // A

```

[[↑] 回到顶部](#目录)

### 十进制转八进制。
- 如果 30
- 30/8=3 余6 6*10^0
- 3/8=0 余3 3*10^1
- 则 30=36

```js

function decimalToOctal(num) {
    var oct = 0
    var c = 0
    while( num > 0 ) {
        var r = num % 8
        oct = oct + r * Math.pow(10, c++)
        num = Math.floor(num / 8)
    }
    console.log(`The decimal in octal is ${oct}`)
}

```


[[↑] 回到顶部](#目录)