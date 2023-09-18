# Java语法糖
1. Java语法糖的概念
2. 常见的Java语法糖用法与原用法的
3. 使用Java语法糖的注意事项

## Java语法糖概述
### 语法糖
&emsp;&emsp;语法糖(Syntactic Sugar)，也称糖衣语法，是由英国计算机学家 Peter.J.Landin 发明的一个术语，指在计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。简而言之，语法糖让程序更加简洁，有更高的可读性。我们所熟知的编程语言中几乎都有语法糖。
&emsp;&emsp;很多人说Java是一个“低糖语言”，其实从Java 7开始Java语言层面上一直在添加各种糖，主要是在“Project Coin”项目下研发。尽管现在Java有人还是认为现在的Java是低糖，未来还会持续向着“高糖”的方向发展。
### 解语法糖
&emsp;&emsp;前面提到过，语法糖的存在主要是方便开发人员使用。但其实，Java虚拟机并不支持这些语法糖。这些语法糖在编译阶段就会被还原成简单的基础语法结构，这个过程就是解语法糖。
&emsp;&emsp;说到编译，大家肯定都知道，Java语言中，javac命令可以将后缀名为.java的源文件编译为后缀名为.class的可以运行于Java虚拟机的字节码。
&emsp;&emsp;如果你去看com.sun.tools.javac.main.JavaCompiler的源码，你会发现在compile()中有一个步骤就是调用desugar()，这个方法就是负责解语法糖的实现的。
&emsp;&emsp;Java 中最常用的语法糖主要有泛型、变长参数、条件编译、自动拆装箱、内部类等。本文主要来分析下这些语法糖背后的原理。一步一步剥去糖衣，看看其本质。
## 常见的Java语法糖
### Lambda表达式

在了解Lambda之前，先回顾一下Java的方法。
Java的方法分为实例方法，例如Integer定义的`equals()`方法:
<details>
  <summary>equals()方法</summary>

```java
    public final class Integer {
    	boolean equals(Object o) {
   	 	...
    	}
    }
```
</details>

以及静态方法，例如Integer定义的`parseInt()`方法:
<details>
    <summary>parseInt()方法</summary>

```java
    public final class Integer {
    	boolean equals(Object o) {
   	 	...
    	}
    }
```
</details>

无论是实例方法，还是静态方法，本质上都相当于过程式语言的函数。例如C函数：`char* strcpy(char* dest, char* src)`
只不过Java的实例方法隐含地传入了一个this变量，即实例方法总是有一个隐含参数this。
函数式编程（Functional Programming）是把函数作为基本运算单元，函数可以作为变量，可以接收函数，还可以返回函数。历史上研究函数式编程的理论是Lambda演算，所以我们经常把支持函数式编程的编码风格称为Lambda表达式。
**Lambda表达式**
Lambda表达式，是Java8的一个新特性，也是Java8中最值得学习的新特性之一。
在Java程序中，我们经常遇到一大堆单方法接口，即一个接口只定义了一个方法：
- Comparator
- Runnable
- Callable
以`Comparator`为例，我们想要调用`Arrays.sort()`时，可以传入一个`Comparator`实例，以匿名类方式编写如下：
<details>

```java
String[] array = ...
Arrays.sort(array, new Comparator<String>() {
    public int compare(String s1, String s2) {
        return s1.compareTo(s2);
    }
});
```
</details>

基本语法:
(parameters) -> expression或(parameters) ->{ statements; }
下面是Java lambda表达式的简单例子:
```java
// 1. 不需要参数,返回值为 5  
() -> 5  
  
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
  
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> x – y  
  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)
```
### try-with-resource语法
### 包装类自动装箱与拆箱
### 内部类
### 增强for循环
### 变长参数