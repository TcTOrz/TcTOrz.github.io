---
layout: post
title: Java学习
categories: [java, learning]
description: java自学
keywords: Java
topmost: false
---

### 说明JVM、JRE、JDK
- JVM --java virtual machine。java虚拟机，主要功能就是将编译好的class文件进行解释执行，因为class文件不能由操作系统直接执行，需要jvm解释
- JRE --java runtime environment。java运行时环境，class在运行时需要调用各种java类库，即jvm要想运行class，必须依赖jre中的类库，可以认为jvm+lib组成jre
- JDK --java development kit。java开发工具包。
JDK包含JRE，JRE包含JVM。
由此可知，jdk主要面向开发者，具有java的编译功能，jre主要面向用户，主要是class的文件运行。只要有编译好的class和jre，那么就可以运行class了。

### 什么是IDE
集成开发环境（Integrated Development Environment）是一类软件，将程序开发环境和程序调试环境集合在一起，提升开发效率。

### 在java中单双引号的区别
- 单引号： 代表字符。
- 双引号： 代表字符串。

```java

char c = 'a';
String s = "abc";

```

### 字符型常量和字符串常量的区别

- 形式上：字符型常量是单引号引起的一个字符串；字符串常量是双引号引起的若干个字符。
- 含义上：字符常量相当于一个整型值(ASCII值)，可以参加表达式运算；字符串常量代表一个地址值(该字符串在内存中存放的位置)
- 占内存大小：字符常量只占两个字符；字符串常量占若干个字节(注意：char在java中占两个字节)

Java要确定每种基本类型所占存储空间的大小。它的大小并不像其他大多数语言 那样随着机器硬件架构的变化而变化。这种所占存储空间大小的不变性是Java程序比其他大多数语言编写的程序更具可移植性的原因之一。

| 基本类型 | 大小 | 最小值 | 最大值 | 包装器类型 |
| :----- | :----: | :----: | :-----: | :----: |
| boolean | - | - | - | Boolean |
| char | 16-bit | Unicode 0 | Unicode 2^16-1 | Character |
| byte | 8 bits | -128 | +127 | Byte |
| short | 16 bits | -2^15 | +2^15+1 | Short |
| int | 32 bits | -2^31 | +2^31-1 | Integer |
| long | 64 bits | -2^63 | +2^63-1 | Long |
| float | 32 bits | -2^31 | +2^31-1 | Float |
| double | 64 bits | -2^63 | +2^63-1 | Double |
| void | - | - | - | Void | 