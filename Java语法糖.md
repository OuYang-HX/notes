# Java语法糖
## 本文概要
1. Java语法糖的概念
2. 常见的Java语法糖用法与原用法的
3. 使用Java语法糖的注意事项
## Java语法糖概述
### 语法糖是什么?
&emsp;&emsp;语法糖（Syntactic Sugar），也称糖衣语法，是由英国计算机学家 Peter.J.Landin 发明的一个术语，指在计算机语言中添加的某种语法，这种语法对语言本身的功能来说没有什么影响，只是为了方便程序员进行开发，提高开发效率，使用这种语法写出来的程序可读性也更高。简言之，语法糖就是对现有语法的一个封装，是对现有语法的简写。
### 解语法糖
&emsp;&emsp;在Java虚拟机中并不支持语法糖，语法糖在程序的编译阶段就会被还原成简单的基础语法结构，这个过程就是解语法糖。在Java语言中，javac命令可以将后缀名为.java的源文件编译为后缀名为.class的可以运行于Java虚拟机中的字节码，在编译时，compile()会调用负责解语法糖的desugar()方法。
&emsp;&emsp;Java 中最常用的语法糖主要有**内部类**、**Lambda表达式**、**try-with-resource语法**、**包装类自动装箱与拆箱**、**增强for循环**、**变长参数**等。本文主要来介绍这些语法糖的使用并分析下这些语法糖背后的原理。一步一步剥去糖衣，看看其本质。
*References:*
https://blog.csdn.net/weixin_43888891/article/details/124567498
# 常见的Java语法糖
## 内部类（内置类、嵌套类）
*References:*
1. https://www.cainiaojc.com/note/qad6mz.html
2. https://developer.aliyun.com/article/726774
3. https://www.joshua317.com/article/212
4. https://segmentfault.com/a/1190000023832584
5. https://blog.csdn.net/liuxiao723846/article/details/108006609
6. https://www.cnblogs.com/shenjianeng/p/6409311.html
7. https://blog.csdn.net/xiaojin21cen/article/details/104532199

**定义：** 将定义在类A内部的类B称之为内部类Inner，类A称为外部类Outer。
### 内部类作用：
1. 增强封装,把内部类隐藏在外部类中,不允许其他类来访问内部类
2. 内部类能提高代码的可读性和可维护性

### 内部类的分类
&emsp;&emsp;将内部类看作是外部类的一个成员，那么内部类可以使用public/缺省/protected/private修饰，还可以是static修饰。根据内部类使用的不同修饰符或者定义的不同位置，可将其分为4类：
1. 成员内部类:没有使用static修饰的内部类；
2. 静态内部类:使用static修饰的内部类；
3. 局部内部类:在方法中定义的内部类；
4. 匿名内部类:只能使用一次,属于内部类的一种特殊情况。

#### 成员内部类
**1. 定义：** 成员内部类，即没有使用static修饰的内部类。这说明,成员内部类属于外部类的对象,不属于外部类本身(类比字段)。
**2. 创建成员内部类**
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
- 由创建成员内部类的过程可知,当存在内部类对象时,一定存在外部类对象。
- 成员内部类的实例自动持有外部类的实例的引用,成员内部类可以无条件访问外部类的所有字段和方法
**注意:** 当成员内部类拥有和外部类同名的成员变量或者方法时，会发生隐藏现象。即默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式进行访问：
```
外部类.this.成员变量
外部类.this.成员方法
```

- 外部类中不能直接访问内部类的成员,必须先创建一个成员内部类的对象，再通过指向这个对象的引用来访问。
<details>
<summary>使用内部类</summary>

```java
//外部类
class Outer {
    // 外部类成员变量x
    int x = 888;
    //成员内部类
    class Inner {
        //内部类成员变量x
        int x = 999;
        //内部类的成员方法
        public void inner_method() {
            // 局部变量x
            int x = 9999;
            System.out.println("局部 x="  + x);
            System.out.println("内部类 x="  + this.x);
            System.out.println("外部类 x="  + Outer.this.x);
        }
    }
}

public class InnerClassForm{
    public static void main(String[] args) {
        // 先创建内部类对象
        Outer.Inner oi = new Outer().new Inner();
        // 调用内部类方法
        oi.inner_method();
    }
} 
```
</details>

<details>
<summary>不使用内部类</summary>

```java
class NonInner {
    int x = 2;
    NonOuter nonOuter = new NonOuter();

    public void non_inner_method() {
        int x = 3;
        System.out.println("局部x=" + x);
        System.out.println("非内部x=" + this.x);
        System.out.println("非外部x=" + nonOuter.x);
    }
}

public class NonInnerClassForm {
    public static void main(String[] args) {
        NonInner nonInner = new NonInner();
        nonInner.non_inner_method();
    }
}
```
</details>

一般的类是不允许用private修饰符的，但是内部类却可以，该实例中，类Inner只对Outer可见，其他的类无法访问的到Inner类。

注意，这里有个例外，如果内部类的访问修饰符被设定为public，那么它是可以被其他类使用的，但是必须经由外部类来实例化内部类。

1) 外部类可以直接访问内部类的成员和方法，但是必须先创建一个内部类的对象，再通过该对象使用其成员和方法。

2) 内部类可以访问外部类的成员和方法，但是要注意，当内部类拥有和外部类相同的成员或方法时，会发生隐藏现象，默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式访问：外部类.this.成员变量/方法

3) 内部类只是一个编译时的概念，一旦编译成功，就会成为完全不同的两个类。对于一个名为Outer的外部类和其内部定义的名为Inner的内部类，编译完后会生成Outer.class和Outer$Inner.class两个类

4) 成员内部类与普通的成员没什么区别，可以与普通成员一样进行修饰和限制

&emsp;&emsp;匿名内部类适合只使用一次的类，当创建一个匿名内部类时，会立即创建该类的一个实例对象，匿名类因为没有类名，所以不能重复使用。

&emsp;&emsp;匿名内部类可以看成局部内部类的特例。（匿名的含义是由编译器自动给内部类起一个内部名称）匿名类也存在被Lamda表达式替代的风险，遗留代码还可以看到匿名类的写法，新写代码如无特殊情况，尽量使用Lamda表达式。

#### 静态内部类
&emsp;&emsp;静态内部类也是定义在另一个类里面的类，只不过在类的前面多了一个关键字static。静态内部类是不需要依赖于外部类的，这点和类的静态成员属性有点类似，并且它不能使用外部类的非static成员变量或者方法，这点很好理解，因为静态内部类没有持有外部类对象的引用，在没有外部类对象的情况下，也可以创建静态内部类的对象，如果允许访问外部类的非static成员就会产生矛盾，因为外部类的非static成员必须依附于具体的对象。

静态内部类也是定义在另一个类里面的类，只不过在类的前面多了一个关键字static。静态内部类是不需要依赖于外部类的，它不持有指向外部类对象的引用this，并且它不能使用外部类的非static成员或方法，这点很好理解，因为在没有外部类的对象的情况下，可以创建静态内部类的对象，如果允许访问外部类的非static成员就会产生矛盾，因为外部类的非static成员必须依附于具体对象。它唯一的作用就是随着类的加载（而不是随着对象的产生）而产生。

其他类如何访问静态类：
1. 当要访问静态类中的非静态成员时：new 外部类名.内部类名（）.内部方法名（），`new  Outer.Inner().function()`
2. 当要访问静态类中的静态成员时 ：外部类名.内部类名.内部方法名（），`Outer.Inner.function()`

主要特点:
静态内部类的主要特点：
1. 不持有外部类的引用（普通内部类持有）
2. 可以直接创建实例，不需要先创建外部类（普通内部类需要）
3. 可以有静态成员变量、方法（普通内部类不行）和非静态成员变量、方法
4. 只可以直接访问外部类静态成员，不可以直接访问外部类的非静态成员（普通内部类可以），需要通过传入外部类引用的方式才能访问

使用场景:
1. 外部类与内部类有很强的联系，需要通过内部类的方式维持嵌套的可读性。
2. 内部类可以单独创建。
3. 内部类不依赖于外部类，外部类需要使用内部类，而内部类不需使用外部类（或者不合适持有外部类的强引用）。

为什么不直接使用两个类，而要使用内部类？
1. 维持两者之间密切的联系
2. 保持嵌套可读性

```java
public class Outer {

    static class Inner {
        public Inner () {

        }
    }

    public static void main(String[] args) {
        Outer.Inner innner = new Outer.Inner();
    }
}
```

#### 局部内部类
局部内部类是定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内。局部内部类就像方法里面的局部变量一样，是不能有public、protected、private及static修饰符的。
```java
public class Outer {
    private int num = 1;
    private int num1 = 10;

    public void func () {
        class Inner {
            private int num = 2;
        }
        Inner inner = new Inner();
        System.out.println(inner.num);
    }


    public static void main(String[] args) {
        Outer outer = new Outer();
        outer.func();
    }
}
```

#### 匿名内部类
匿名内部类应该是平时我们编写代码时用的最多的，在编写事件监听的代码时匿名内部类不但方便，而且使代码更加容易维护。匿名内部类是唯一一种没有构造器的类。正因为其没有构造器，所以匿名内部类的使用范围非常有限，大部分匿名内部类用于接口回调。匿名内部类在编译的时候由系统自动起名为Outer$1.class。一般来说，匿名内部类用于集成其他类或者实现接口，并不需要增加额外的方法，只是对集成方法的实现或是重写。


```java
public class Outer {

    public void func () {
        System.out.println("1");
    }


    public static void main(String[] args) {
        Outer inner = new Outer() {
            public void func () {
                System.out.println("2");
            }
        };
        inner.func();
    }
}
```


#### 非静态内部类原理简析
&emsp;&emsp;内部类为什么也算是一种语法糖呢？因为在Java虚拟机中内部类与常规类并无不同，在编译过程中，内部类由编译器识别到并被编译为了一个独立的类，而不是外部类的某一个域。虚拟机在运行时，也是将内部类作为一个常规类处理。下面以一个简单的内部类反编译后的结果来说明内部类的实现原理：
<details>
<summary>编译所用的内部类</summary>

```java
class Outer {
    private void outer_method() {
    }

    class Inner {
        public void inner_method() {
        }
    }
}
```
</details>

&emsp;&emsp;将`Outer.java`编译后在`Outer.class`所在目录打开Terminal，通过`javap -p -c Outer$Inner.class`反编译，得到如下信息
```java
Compiled from "Outer.java"
class InnerClassDemo.Outer$Inner {
  final InnerClassDemo.Outer this$0;

  InnerClassDemo.Outer$Inner(InnerClassDemo.Outer);
    Code:
       0: aload_0
       1: aload_1
       2: putfield      #1                  // Field this$0:LInnerClassDemo/Outer;
       5: aload_0
       6: invokespecial #2                  // Method java/lang/Object."<init>":()V
       9: return

  public void inner_method();
    Code:
       0: return
}
```
内部类隐式生成了一个字段`this$0`，指向外部对象的引用。内部类的构造方法有一个外部类类型参数，这个参数通过`putfield`给`this$0`赋值。这也是**注意：** 由于非静态内部类和匿名内部类都默认持有外部类的引用，使用不当的话会使外部类一直被某对象持有引用，从而在其该被GC回收时却不被回收，也最终导致了内存泄漏。
<details>
<summary>内存泄漏</summary>
Java使用有向图机制，通过GC自动检查内存中的对象（什么时候检查由虚拟机决定），如果GC发现一个或一组对象为不可到达状态，则将该对象从内存中回收。也就是说，一个对象不被任何引用所指向，则该对象会在被GC发现的时候被回收；另外，如果一组对象中只包含互相的引用，而没有来自它们外部的引用（​​​例如有两个对象A和B互相持有引用，但没有任何外部对象持有指向A或B的引用​​），这仍然属于不可到达，同样会被GC回收。
</details>

内部类没有什么特殊的，其访问外围类数据的能力都是编译器添加代码实现的。
#### 内部类的意义
1. 每个内部类都能独立的集成一个接口的实现，所以无论外部类是否已经集成了某个（接口）实现，对于内部类都没有影响。内部类使得多重集成的解决方案变得完整。

2. 方便将存在一定逻辑关系的类组织在一起，又可以对外界隐藏。

3. 方便编写时间驱动程序。

4. 方便编写线程代码。
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

