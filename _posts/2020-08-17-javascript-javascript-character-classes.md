---
layout: post
title: Javascript正则表达式字符类
categories: [javascript, chacracter-classes]
description: 正则表达式-断言
keywords: JavaScript, character-classes
topmost: false
---

字符类可以区分各种字符，例如区分字母和数字。

### 类型

| 类型 | 含义 |
| :----: | :----: |
| <code>.</code> | <div style="text-align: left;"><p>有下列含义之一:</p><ul><li>匹配除终止符之外的任何单个字符: <code>\n</code>、<code>\r</code>、<code>\u2028</code>、<code>\u2029</code></li><li>在字符集内，点失去了它的特殊意义，并与文字点匹配。</li></ul><div> <p>需要注意的是，m multiline标志不会改变点的行为。因此，要跨多行匹配一个模式，可以使用字符集[^]—它将匹配任何字符，包括新行。ES2018 添加了 s "dotAll" 标志,它允许点也匹配行终止符。</p> | 