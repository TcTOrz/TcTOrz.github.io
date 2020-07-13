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