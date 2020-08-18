---
layout: wiki
title: Javascript-regular-expressions
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## `正则表达式`

### `创建正则表达式`

- 使用一个正则表达式字面量，其由包含在斜杠之间的模式组成。例如：

```js

var re = /ab+c/;

```

> 脚本加载后，正则表达式字面量就会被编译。当正则表达式保持不变时，使用此方法可获得更好的性能。

- 调用`RegExp`对象的构造函数。例如：

```js

var re = new RegExp("ab+c");

```

> 在脚本运行过程中，用构造函数创建的正则表达式会被编译。如果正则表达式将会改变，或者它将会从用户输入等来源中动态地产生，就需要使用构造函数来创建正则表达式。


### 编写一个正则表达式

#### 简单模式

略

#### 使用特殊字符

##### 断言`Assertions`

断言的组成之一是边界。对于文本、词或模式，边界可以用来表明它们的起始或终止部分（如向前断言，向后断言以及条件表达式）。

[断言详细说明](https://ifwechat.com//2020/08/15/javascript-assertions/)

##### 字符类（Character Classes）

区分不同类型的字符，例如区分字母和数字。

[字符类详细说明](https://ifwechat.com//2020/08/17/javascript-character-classes/)
