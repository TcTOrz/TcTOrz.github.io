---
layout: post
title: Javascript算法题
categories: [javascript, algorithms]
description: 一些和js相关的算法题
keywords: JavaScript, Algorithms
topmost: false
---

# 目录

* TOC
{:toc} 

## 数组排序算法的复杂性参考

| 名称                  | 最优      | 平均      | 最坏          | 内存      | 稳定      | 备注                  |
| --------------------- | :-------: | :-------: | :-----------: | :-------: | :-------: | --------------------- |
| **冒泡排序**          | n         | n^2       | n^2           | 1         | Yes       |                       |
| **插入排序**          | n         | n^2       | n^2           | 1         | Yes       |                       |
| **选择排序**          | n^2       | n^2       | n^2           | 1         | No        |                       |
| **堆排序**            | n log(n)  | n log(n)  | n log(n)      | 1         | No        |                       |
| **归并排序**          | n log(n)  | n log(n)  | n log(n)      | n         | Yes       |                       |
| **快速排序**          | n log(n)  | n log(n)  | n^2           | log(n)    | No        | 在 in-place 版本下，内存复杂度通常是 O(log(n)) |
| **希尔排序**          | n log(n)  | 取决于差距序列   | n (log(n))^2  | 1         | No        |  |
| **计数排序**          | n + r     | n + r     | n + r         | n + r     | Yes       | r - 数组里最大的数    |
| **基数排序**          | n * k     | n * k     | n * k         | n + k     | Yes       | k - 最长 key 的升序   |

[[↑] 回到顶部](#目录)

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

## Data-Structures

### Array

#### 快速选择算法

```js

/* 
 * 快速选择是一种从无序列表中找到第k小元素的选择算法
 *
 * 注：快速选择总体思路和快速排序一致
 */

function quickSelect(items, kth) {
    return randomizedSelect(items, 0, items.length - 1, kth)
}

function randomizedSelect(
    items,
    left,
    right,
    i
) {
    if( left === right ) return items[left]

    const pivotIndex = randomizedPartition(items, left, right)
    const k = pivotIndex - left + 1

    if( i === k ) return items[pivotIndex]
    if( i < k ) return randomizedSelect(items, left, pivotIndex - 1, i)
    
    return randomizedSelect(items, pivotIndex + 1, right, i - k)
}

function randomizedPartition (items, left, right) {
  const rand = getRandomInt(left, right)
  swap(items, rand, right)
  return partition(items, left, right)
}

function partition (items, left, right) {
  const x = items[right]
  let pivotIndex = left - 1

  for (let j = left; j < right; j++) {
    if (items[j] <= x) {
      pivotIndex++
      swap(items, pivotIndex, j)
    }
  }

  swap(items, pivotIndex + 1, right)

  return pivotIndex + 1
}

function getRandomInt (min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min
}

function swap (arr, x, y) {
  [arr[x], arr[y]] = [arr[y], arr[x]]
}

// testing
console.log(quickSelect([1, 4, 2, -2, 4, 5]))

```

[[↑] 回到顶部](#目录)
