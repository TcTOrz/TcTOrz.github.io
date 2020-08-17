---
layout: post
title: Javascript正则表达式断言
categories: [javascript, Assertions]
description: 正则表达式-断言
keywords: JavaScript, Assertions
topmost: false
---

断言的组成之一是边界。对于文本、词或模式，边界可以用来表明它们的起始或终止部分（如向前断言，向后断言以及条件表达式）。

### 类型

#### 边界类断言

| 字符 | 含义 |
| :----: | :----: |
| ^ | <div style="text-align:left;">匹配输入的开头。如果多行模式设为 true，^ 在换行符后也能立即匹配，比如 /^A/ 匹配不了 "an A" 里面的 "A"，但是可以匹配 "An A" 里面第一个 "A"。 <div style="background-color: #fff3d4"><code>^</code>出现在集合或范围开头时的含义与此不同（[参见 group](#)）。</div></div>|
| $ | <div style="text-align:left;">匹配输入的结束。如果多行模式设为 true，^ 在换行符前也能立即匹配，比如 /t$/ 不能匹配  "eater" 中的 "t"，但是可以匹配 "eat" 中的 "t"。</div> |
| \b | <div style="text-align:left;">匹配一个单词的边界，这是一个字的字符前后没有另一个字的字符位置, 例如在字母和空格之间。需要注意的是匹配的单词边界不包括在匹配中。换句话说，匹配字边界的长度为零。<br />一些例子： <br /> <ul><li><code>/\bm/</code>在 "moon" 中匹配到 "m";</li><li><code>/oo\b/</code>在 "moon" 中不会匹配到 "oo", 因为 "oo" 后面跟着 "n" 这个单词字符.</li><li><code>/oon\b/</code>在 "moon" 中匹配 "oon"， 因为 "oon" 是这个字符串的结尾, 因此后面没有单词字符</li><li><code>/\w\b\w/</code>将永远不会匹配任何东西，因为一个单词字符后面永远不会有非单词字符和单词字符。</li></ul><p>匹配退格字符 ([\b]), 查看 [字符类](#) </p></div> |
| \B | <div style="text-align:left;">匹配非单词边界。这是上一个字符和下一个字符属于同一类型的位置：要么两者都必须是单词，要么两者都必须是非单词，例如在两个字母之间或两个空格之间。字符串的开头和结尾被视为非单词。与匹配的词边界相同，匹配的非词边界也不包含在匹配中。例如，<code>/\Bon/</code> 在 “at noon” 中匹配 “on” ，<code>/ye\B/</code> 在 "possibly yesterday"中匹配"ye"。</div> |

#### 其他断言

<div style="background-color: #fff3d4">提示：<code>?</code>字符也可以用作量词</div>

| 字符 | 含义 |
| :----: | :----: |
| <code>x(?=y)</code> | <code><b>向前断言:</b></code>x被y跟随时匹配。 |
| <code>x(?!y)</code> | <code><b>向前否定断言:</b></code>x没有被y紧随时匹配。 |
| <code>(?<=y)x</code> | <code><b>向后断言:</b></code>x跟随y的情况下匹配。 |
| <code>(?<!y)x</code> | <code><b>向后否定断言:</b></code>x不跟随y时匹配。 |

### 示例

#### 一般边界类型概述示例

```js

// 使用 正则表达式边界修复错误字符串 
buggyMultiline = `tey, ihe light-greon apple 
tangs on ihe greon traa`;

// 修复后的结果应该是："hey, the light-green apple
// hangs on the green tree."

// 1) 使用 ^ to 修正字符串开始处和换行后的匹配.
buggyMultiline = buggyMultiline.replace(/^t/gim,'h'); 
console.log(1, buggyMultiline); // 修复 'tey'=>'hey' , 'tangs'=>'hangs' 避开了'traa'

// 2) 使用 $ 修正字符串结尾处的匹配.
buggyMultiline = buggyMultiline.replace(/aa$/gim,'ee.'); 
console.log(2, buggyMultiline); // 修复 'traa' => 'tree'.

// 3) 使用 \b 修正单词和空格之间边界上的字符.
buggyMultiline = buggyMultiline.replace(/\bi/gim,'t');
console.log(3, buggyMultiline); // 修复 'ihe' => 'the' 而不会影响 'light'.

// 4) 使用 \B to match characters inside borders of an entity.
fixedMultiline = buggyMultiline.replace(/\Bo/gim,'e');
console.log(4, fixedMultiline); // fix  'greon' but does not touch 'on'.

```

#### 使用 ^（控制字符）匹配输入的开头

```js

let fruits = ["Apple", "Watermelon", "Orange", "Avocado", "Strawberry"];

// 使用正则 /^A/ 选择以'A'开头的水果.
// 这里的 '^' 只有一种含义: 匹配输入的开头.

let fruitsStartsWithA = fruits.filter(fruit => /^A/.test(fruit));

```

在第二个示例中，^用于在输入的开始处匹配，以及在内部使用时用于创建否定或被补充的字符集 组和范围.

```js

let fruits = ["Apple", "Watermelon", "Orange", "Avocado", "Strawberry"];

// 使用正则 /^[^A]/ 选择 不是以 ‘A’ 开头的水果
// 在这个例子中，“^” 控件符号表示两种含义:
// 1) 匹配输入的开头
// 2) 一个否定的字符集: [^A] ，意思是匹配不是 ‘A’ 的字符

let fruitsStartsWithNotA = fruits.filter(fruit => /^[^A]/.test(fruit));

```

#### 匹配字边界

```js

let fruitsWithDescription = ["Red apple", "Orange orange", "Green Avocado"];
// 选择包含以 “en” 或 “ed” 结尾的单词的描述:
let enEdSelection = fruitsWithDescription.filter(descr => /(en|ed)\b/.test(descr));

```

#### 向前断言

```js

// JS 向前断言 x(?=y) 匹配被 y 跟随的 x

let regex = /First(?= test)/g;
console.log('First test'.match(regex)); // [ 'First' ]
console.log('test First peach'.match(regex)); // null
console.log('This is a First test in a year.'.match(regex)); // [ 'First' ]
console.log('This is a First peach in a month.'.match(regex)); // null

```

#### 向前否定断言

```js

// 匹配未被小数点跟随且至少有一位数字
console.log(/\d+(?!\.)/g.exec('3.141')); // [ '141', index: 2, input: '3.141' ]

```

#### 不同含义的'?!'：断言和范围的组合用法

不同含义的?! 结合使用 断言  /x(?!y)/ 和  范围 [^?!].

```js

let orangeNotLemon = "Do you want to have an orange? Yes, I do not want to have a lemon!";
let selectNotLemonRegex = /[^?!]+have(?! a lemon)[^?!]+[?!]/gi
console.log(orangeNotLemon.match(selectNotLemonRegex)); // [ 'Do you want to have an orange?' ]

let selectNotOrangeRegex = /[^?!]+have(?! an orange)[^?!]+[?!]/gi
console.log(orangeNotLemon.match(selectNotOrangeRegex)); // [ ' Yes, I do not want to have a lemon!' ]

```

#### 向后断言

```js

let oranges = ['ripe orange A ', 'green orange B', 'ripe orange C',];

let ripe_oranges = oranges.filter( fruit => fruit.match(/(?<=ripe )orange/));
console.log(ripe_oranges); // [ 'ripe orange A ', 'ripe orange C' ]

```
