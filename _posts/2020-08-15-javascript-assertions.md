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
| \B | <div style="text-align:left;">匹配非单词边界。这是上一个字符和下一个字符属于同一类型的位置：要么两者都必须是单词，要么两者都必须是非单词，例如在两个字母之间或两个空格之间。字符串的开头和结尾被视为非单词。与匹配的词边界相同，匹配的非词边界也不包含在匹配中。例如，/\Bon/ 在 “at noon” 中匹配 “on” ，/ye\B/ 在 "possibly yesterday"中匹配"ye"。</div> |

#### 其他断言


### 示例