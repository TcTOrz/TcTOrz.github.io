---
layout: post
title: Java学习
categories: [java, learning]
description: java自学
keywords: Java
topmost: false
---

Java边学边记。

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

### Java常见关键字

| type | 关键字 |
| :----: | :----: | 
| 访问控制 | private, protected, public |
| 类，方法和变量修饰符 | abstract, class, extends, final, implements, interface, native, new, static, strctfp, synchronized, transient, volatile |
| 程序控制 | break, continue, return, do, while, if, else, for, instanceof, switch, case, default |
| 错误处理 | try, catch, throw, throws, finally |
| 包相关 | import, package |
| 基本类型 | boolean, byte, char, double, float, int, long, short, null, true, false |
| 变量引用 | super, this, void |
| 保留字 |  goto, const |

### Java泛型了解么？什么是类型擦除？介绍一下通配符？
Java泛型(generics)是JDK5中引入的一个新特性，泛型提供了编译时类型安全监测机制，该机制允许程序员在编译时监测到非法的类型。泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。

`Java的泛型是伪泛型，这是因为Java在编译期间，所有的泛型信息都会被擦掉，这也就是通常所说的类型擦除。`

```java

List<Integer> list = new ArrayList<>();
list.add(12);
// 报错
list.add('a');

Class<? extends List> clazz = list.getClass();
Method add = clazz.getDeclareMethod("add", Object.class);
// 通过反射添加，是可以的
add.invoke(list, "ki");

System.out.println(list);

```

泛型一般有三种使用方式：泛型类、泛型接口、泛型方法

- 泛型类

```java

// 此处T可以随便标识，常见的如T、E、K、V等形式的参数常用于标识泛型
// 在实例化泛型类时，必须指定T的具体类型
public class Generic<T> {
    private T key;
    public Generic(T key) {
        this.key = key;
    }
    public T getKey() {
        return key;
    }
}

// 实例化泛型类
Generic<Integer> genericInteger = new Generic<Integer>(123);

```

- 泛型接口

```java

public interface Generator<T> {
    public T method();
}

```

实现泛型接口，不指定类型

```java

class GeneratorImpl<T> implements Generator<T> {
    @Override
    public T method() {
        return null;
    }
}

```

实现泛型接口，指定类型

```java

class GeneratorImpl<T> implements Generator<String> {
    @Override
    public String method() {
        return "hello";
    }
}

```

- 泛型方法

```java

public static <E> void printArray(E[] inputArray) {
    for(E element: inputArray) {
        System.out.println(element);
    }
    System.out.println();
}

// usage
// 创建不同类型的数组：Integer，Double， Charactor
Integer[] intArray = {1,2,3};
String[] stringArray = {"Hello", "World"};
printArray(intArray);
printArray(stringArray);

```

`常用的通配符为：T，E，K，V，?`

- ? 代表不确定的java类型
- T (type)表示具体的一个java类型
- K V (key value)分别代表java键值中的Key Value
- E (element)代表Element

[Java泛型文章1](https://juejin.im/post/5d5789d26fb9a06ad0056bd9)
[Java泛型文章2](https://www.cnblogs.com/wuqinglong/p/9456193.html)

### ==和equals的区别
`==`：比较两个对象的地址是否相等。即判断两个对象是不是同一个对象。(基本数据类型==比较的是值，引用数据类型==比较的是内存地址)

> 因为 Java 只有值传递，所以，对于 == 来说，不管是比较基本数据类型，还是引用数据类型的变量，其本质比较的都是值，只是引用类型变量存的值是对象的地址。

`equals()`：比较两个对象是否相等，不能用于比较基本类型的变量。equals()方法存在于Object类中，而Object类是所有类的直接或间接父类。

Object类equals方法

```java

public boolean equals(Object obj) {
    return (this==obj);
}

```

equals() 方法使用存在两种情况

- 类没有覆盖 equals()方法。则通过 equals()比较该类的两个对象时，等价于通过“==”比较这两个对象。使用的默认是 Object类equals()方法。
- 类覆盖了 equals()方法。一般，我们都覆盖 equals()方法来两个对象的内容相等；若它们的内容相等，则返回 true(即，认为这两个对象相等)。 

```java

public class test1 {
    public static void main(String[] args) {
        String a = new String("ab"); // a 为一个引用
        String b = new String("ab"); // b为另一个引用,对象的内容一样
        String aa = "ab"; // 放在常量池中
        String bb = "ab"; // 从常量池中查找
        if (aa == bb) // true
            System.out.println("aa==bb");
        if (a == b) // false，非同一对象
            System.out.println("a==b");
        if (a.equals(b)) // true
            System.out.println("aEQb");
        if (42 == 42.0) { // true
            System.out.println("true");
        }
    }
}

```

说明

- String 中的 equals 方法是被重写过的，因为 Object 的 equals 方法是比较的对象的内存地址，而 String 的 equals 方法比较的是对象的值。
- 当创建 String 类型的对象时，虚拟机会在常量池中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用。如果没有就在常量池中重新创建一个 String 对象。

### hashCode()与 equals()
暂空

### Linux/Windows JAVA环境配置/tomcat安装

- Windows
> [下载](https://www.oracle.com/java/technologies/javase-jdk14-downloads.html)JDK,选择对应下载的版本即可。
> 安装完成后配置PATH环境变量 我的电脑->属性->高级系统设置->环境变量
> 添加系统变量 JAVA_HOME => JDK安装目录
> 添加系统变量 Path => %JAVA_HOME%\bin
> 安装IDEA是设置正确的JDK目录即可正常使用

- Linux
> [下载](https://www.oracle.com/java/technologies/javase-jdk14-downloads.html)JDK,选择对应下载的版本即可。
> 解压后设置环境变量

```bash

# 在/etc/profile中添加
# Java Environment Settings
export JAVA_HOME=/opt/java/jdk-14.0.1
#export CLASSPATH=$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
#export JRE_HOME=$JAVA_HOME/jre

```

生效

```bash

source /etc/profile

```

- Linux Tomcat安装
> [下载](https://tomcat.apache.org/)Tomcat,选择对应版本。
> 解压
> conf/server.xml 配置端口信息等
> conf/tomcat-users.xml添加

```xml

<role rolename="admin-gui"/>
  <role rolename="admin-script"/>
  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <role rolename="manager-jmx"/>
  <role rolename="manager-status"/>
  <user username="tomcat" password="s3cret" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-script,admin-gui"/>

```

> webapps/manager/META-INF/context.xml 注释,作用是可以跨服务器访问Tomcat管理界面

```xml

<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->

```

### JDK中的三个环境变量

- JAVA_HONE: 该变量是指安装Java的JDK路径，它告知操作系统在哪里可以找到JDK。
- Path: 该变量告诉操作系统可执行文件的搜索路径。
- ClassPath: 该变量告诉Java解释器在哪些目录下可以找到所需执行的class文件，即javac编译生成的字节码文件。

### Java访问权限修饰符
Java中有4中访问权限： 公有(public)、私有(private)、保护(protected)、默认(default)。但访问权限修饰符只有三种，因为默认访问权限没有访问权限修饰符。具体权限的规定如下所示：

|  | 私有(private) | 默认(default) | 保护(protected) | 公有(public) |
| :----: | :----: | :----: | :----: | :----: | 
| 类 | 只有内部类允许私有,只能在当前类中被访问 | 可以被当前包中的所有类访问 | 只有内部类可以设为保护权限，相同包中的类和其子类可以访问 | 可以被所有类访问 |
| 属性 | 只能被当前类访问 | 可以被相同包中的类访问 | 可以被相同包中的类和当前类的子类访问 | 可以被所有类访问 |
| 方法 | 只能被当前类访问 | 可以被相同包中的类访问 | 可以被相同包中的类和当前类的子类访问 | 可以被所有类访问 |

### 方法重载（Overload）和覆写（Override）的区别

重载是指在相同类内定义名称相同但参数个数或类型或顺序不同的方法。而覆写是在子类当中定义名称、参数个数与类型均与父类相同的方法，用于覆写父类的方法。区别如下：

| 区别 | 重载 | 覆写 |
| :---: | :---: | :---: |
| 英文单词 | Overload | Override |
| 定义 | 方法名称相同、参数的类型及个数和顺序至少1个不同 | 方法名称、参数的类型及个数、返回值类型完全相同 |
| 范围 | 只发生在一个类中 | 发生在继承关系中 |
| 权限 | 不受权限控制 | 被覆写的方法不能拥有比父类更严格的访问控制权限 |

### 基本数据类型的包装类

| 基本数据类型 | 基本数据类型包装类 |
| :---------: | :-----------: |
| int | Integer |
| char | Character |
| float | Float |
| double | Double |
| byte | Byte |
| long | Long |
| short | Short |
| boolean | Boolean |

###### Usage

```java

public class IntegerDemo {
    public static void main(String[] args) {
        String a = "1234";
        int i = Integer.parseInt(a);
        i++;
        System.out.println(i);  // 1235
    }
}

```

### 异常处理的标准格式

```java

public class ThrowDemo {
    public static void main(String[] args) {
        int[] arr = new int[5];
        try {
//            ThrowDemo t = new ThrowDemo();
            setZero(arr, 10);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("异常:"+e);
        }
        System.out.println("main方法结束");
    }

    public static void setZero(int[] arr, int index) throws ArrayIndexOutOfBoundsException {
        System.out.println("-----------setZero开始-----------");
        try {
            arr[index] = 0;
        }catch (ArrayIndexOutOfBoundsException ex) {
            throw ex;
        }finally {
            System.out.println("-----------setZero结束-----------");
        }
    }
}

// -----------setZero开始-----------
// -----------setZero结束-----------
// 异常:java.lang.ArrayIndexOutOfBoundsException: 10
// main方法结束

```

###### 异常处理的继承关系

异常类型的最大父类是Throwable类，其分为两个子类，分别为Exception、Error。Exception表示程序可以处理的异常，而Error表示JVM错误，一般无需开发人员自己处理。

###### RuntimeException和Exception的区别

RuntimeException类是Exception类的子类，Exception定义的异常必须处理，而RuntimeException定义的异常可以选择性的进行处理。