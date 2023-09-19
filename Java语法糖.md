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

Lambda表达式
一、Lambda表达式简介
1.1什么是Lamdba表达式?
 Lambda表达式是Java 8 添加的一个新特性，可以认为，Lambda是一个匿名函数（相似于匿名内部类）,作用是返回一个实现了接口的对象（这个观点非常重要，贯穿于Lambda表达式的整个使用过程）。

1.2为什么使用Lambada表达式？
 使用Lambda表达式对比于其他接口实现方式显得非常简洁。（详见3种接口实现的方法代码块CodeBlock-1）

1.3Lambda对接口的要求？
 虽然Lambda表达式对某些接口进行简单的实现，但是并不是所有的接口都可以使用Lambda表达式来实现，要求接口种定义的必须要实现的抽象方法只能是一个（注意：具体方法可以多个或者没有）。

在Java 8 中对接口增加了新特性：default，提供了一个默认的抽象方法，但是Lambda对此没有特殊的影响，方法可以按Lambda所表达的来。
Java Lambda 表达式 Java Lambda表达式的一个重要用法是简化某些匿名内部类（Anonymous Classes）的写法。


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
Lambda 允许把函数作为一个方法的参数（函数作为参数传递进方法中）。

使用 Lambda 表达式可以使代码变的更加简洁紧凑。
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
(x, y) -> x - y  
  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)
```
### try-with-resource语法
### 包装类自动装箱与拆箱
### 内部类
*References:*
1. https://www.cainiaojc.com/note/qad6mz.html
2. https://developer.aliyun.com/article/726774
3. https://www.joshua317.com/article/212
4. https://segmentfault.com/a/1190000023832584
5. https://blog.csdn.net/liuxiao723846/article/details/108006609
6. https://www.cnblogs.com/shenjianeng/p/6409311.html
7. https://blog.csdn.net/xiaojin21cen/article/details/104532199

**定义：** 定义在类内部的类,称之为内部类。
#### 内部类作用：
1. 增强封装,把内部类隐藏在外部类中,不允许其他类来访问内部类
2. 内部类能提高代码的可读性和可维护性

#### 内部类的分类
对于内部类的分类,可以对比于成员变量的分类.

我们可以根据不同的修饰符或者定义的不同位置把成员变量,可以细分为:类成员变量,实例成员变量,局部变量.

内部类看做是外部类的一个成员,那么内部类可以使用public/缺省/protected/private修饰.还可以是static修饰.

同理,内部类也根据使用不同的修饰符或者定义的不同位置,将其分成4类:
1. 实例内部类:内部类没有使用static修饰

2. 静态内部类:内部类使用static修饰

3. 局部内部类:在方法中定义的内部类

4. 匿名内部类:只能使用一次,属于内部类的一种特殊情况

##### 实例内部类
**1. 定义：** 实例内部类,即没有使用static修饰的内部类.这说明,实例内部类属于外部类的对象,不属于外部类本身(类比字段)。
**2. 创建实例内部类**
<details>
<summary>Example</summary>

```java
//外部类
class Outter {
 // 实例内部类:没有使用static修饰
 class Inner {
 }
}
public class InnerDemo1 {
 public static void main(String[] args) {
 // 创建实例内部类,没有使用static修饰,属于外部类的对象,因此,创建实例内部类前,必须存在外部类对象
 Outter out = new Outter();
 // 通过外部类对象创建内部类对象
 Outter.Inner in = out.new Inner();
 }
}
```
</details>

**3. 特点：**
- 由创建实例内部类的过程可知,当存在内部类对象时,一定存在外部类对象.
- 实例内部类的实例自动持有外部类的实例的引用,实例内部类可以无条件访问外部类的所有字段和方法
注意:当成员内部类拥有和外部类同名的成员变量或者方法时，会发生隐藏现象
- 外部类中不能直接访问内部类的成员,必须先创建一个成员内部类的对象，再通过指向这个对象的引用来访问
<details>
<summary>Example</summary>

```java
//外部类
class Outter {
 private String name = "out";
 private Integer age = 17;
 // 实例内部类:没有使用static修饰
 class Inner {
 private Integer age = 18; // 隐藏现象,隐藏了外部类的age
 Inner() {
  // 特点:1.实例内部类能直接访问外部类成员
  // 2.当实例内部类和外部类有同名的字段或者方法时，会发生隐藏现象
  System.out.println(name + this.age);// 输出out18
  // 此时若需要使用外部类的age,语法:外部类.this.age
  System.out.println(Outter.this.age);// 输出17
 }
 }
}
```
</details>

匿名内部类适合只使用一次的类，当创建一个匿名内部类时，会立即创建该类的一个实例对象，匿名类因为没有类名，所以不能重复使用。
匿名内部类可以看成局部内部类的特例。（匿名的含义是由编译器自动给内部类起一个内部名称）匿名类也存在被Lamda表达式替代的风险，遗留代码还可以看到匿名类的写法，新写代码如无特殊情况，尽量使用Lamda表达式。
### 增强for循环
### 变长参数