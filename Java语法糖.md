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
&emsp;&emsp;Java 中最常用的语法糖主要有**内部类**、**Lambda表达式**、**try-with-resource语法**、**包装类自动装箱与拆箱**、**增强for循环**、**变长参数**等。本文主要来介绍这些语法糖的使用并分析下这些语法糖背后的原理。
<!-- *References:*
https://blog.csdn.net/weixin_43888891/article/details/124567498 -->
# 常见的Java语法糖
## 内部类（内置类、嵌套类）
<!-- *References:*
1. https://www.cainiaojc.com/note/qad6mz.html
2. https://developer.aliyun.com/article/726774
3. https://www.joshua317.com/article/212
4. https://segmentfault.com/a/1190000023832584
5. https://blog.csdn.net/liuxiao723846/article/details/108006609
6. https://www.cnblogs.com/shenjianeng/p/6409311.html
7. https://blog.csdn.net/xiaojin21cen/article/details/104532199 -->

**定义：** 将定义在类A内部的类B称之为内部类Inner，类A称为外部类Outer。
### 内部类作用：
&emsp;&emsp;Java 语言中之所以引入内部类，是因为有些时候一个类只想在一个类中有用，不想让其在其他地方被使用，也就是对外隐藏内部细节。内部类其实也是一个语法糖，因为虚拟机在运行时，也是将内部类作为一个常规类处理。其只是一个编译时的概念，在Java虚拟机中内部类与常规类并无不同，在编译过程中，内部类由编译器识别到并被编译为了一个单独的名为 `outer$innter.class`的class文件，而不是外部类的某一个域。
1. 每个内部类都能独立的集成一个接口的实现，所以无论外部类是否已经集成了某个（接口）实现，对于内部类都没有影响。内部类使得多重集成的解决方案变得完整。
2. 增强封装,把内部类隐藏在外部类中,不允许其他类来访问内部类；
3. 方便将存在一定逻辑关系的类组织在一起，又可以对外界隐藏。
4. 内部类能提高代码的可读性和可维护性。
5. 方便编写时间驱动程序。
6. 方便编写线程代码。

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
<summary>Example1：创建成员内部类</summary>

```java
//外部类
class Outter {
    // 成员内部类:没有使用static修饰
    class Inner {
    }
}
public class InnerDemo1 {
    public static void main(String[] args) {
    // 创建成员内部类,没有使用static修饰,属于外部类的对象,因此,创建成员内部类前,必须存在外部类对象
    Outter out = new Outter();
    // 通过外部类对象创建内部类对象
    Outter.Inner in = out.new Inner();
 }
}
```
</details>

**3. 特点：**
&emsp;&emsp;一般的类是不允许用private修饰符的，但是内部类却可以，该实例中，类Inner只对Outer可见，其他的类无法访问的到Inner类。注意，这里有个例外，如果内部类的访问修饰符被设定为public，那么它是可以被其他类使用的，只是必须经由外部类来实例化内部类。

1. 外部类可以直接访问内部类的成员和方法，但是必须先创建一个内部类的对象，再通过该对象使用其成员和方法；
2. 内部类可以访问外部类的成员和方法，但是要注意，当内部类拥有和外部类相同的成员或方法时，会发生隐藏现象，默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式访问：`外部类.this.成员变量/方法`；
3. 内部类只是一个编译时的概念，一旦编译成功，就会成为完全不同的两个类。对于一个名为Outer的外部类和其内部定义的名为Inner的内部类，编译完后会生成Outer.class和Outer$Inner.class两个类；
4. 成员内部类与普通的成员没什么区别，可以与普通成员一样进行修饰和限制。
<details>
<summary>Example2：使用内部类</summary>

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
<summary>Example3：不使用内部类</summary>

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

##### 反编译分析
&emsp;&emsp;首先通过IDEA或者javac命令编译生成class文件，然后通过jad工具进行反编译，生成jad文件：
<details>
<summary>Example4源码</summary>

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
<details>
<summary>反编译Outer.class</summary>

```java
class Outer
{
    class Inner
    {

        public void inner_method()
        {
        }

        final Outer this$0;

        Inner()
        {
            this.this$0 = Outer.this;
            super();
        }
    }


    Outer()
    {
    }

    private void outer_method()
    {
    }
}
```
</details>

&emsp;&emsp;可以看到内部类在编译时隐式生成了一个Outer类型的字段`this$0`，并在Inner的构造方法中指向了外部对象的引用`this.this$0 = Outer.this`。这也是非静态内部类必须依赖外部类才能存在的原因。**需要注意：** 由于非静态内部类和匿名内部类都默认持有外部类的引用，使用不当的话会使外部类一直被某对象持有引用，从而在其该被GC回收时却不被回收，最终可能导致内存泄漏。
<details>
<summary>内存泄漏</summary>
&emsp;&emsp;Java使用有向图机制，通过GC自动检查内存中的对象（什么时候检查由虚拟机决定），如果GC发现一个或一组对象为不可到达状态，则将该对象从内存中回收。也就是说，一个对象不被任何引用所指向，则该对象会在被GC发现的时候被回收；另外，如果一组对象中只包含互相的引用，而没有来自它们外部的引用（​​​例如有两个对象A和B互相持有引用，但没有任何外部对象持有指向A或B的引用​​），这仍然属于不可到达，同样会被GC回收。
</details>

#### 静态内部类
**1. 定义**：使用static修饰的内部类。所以，该内部类属于外部类本身，而不属于外部类的对象。
**2. 创建静态内部类**
<details>
<summary>Example5 创建静态内部类</summary>

```java
class Outter {
    // 静态内部类:使用static修饰
    static class Inner {
    }
}
public class InnerDemo2 {
    public static void main(String[] args) {
        // 因为静态内部类属于外部类本身,可以直接通过外部类类名来访问(类比字段)
        Outter.Inner in = new Outter.Inner();
    }
}
```
</details>

**3. 特点：**
- 在创建内部类的实例时，不必创建外部类的实例。
- 静态内部类可以直接访问外部类的静态成员，如果访问外部类的实例成员，必须通过外部类的实例去访问。
*简单理解：静态成员属于类，非静态成员属于对象，如果要访问外部类的实例成员（非静态成员），当然要先存着外部类对象的。而静态内部类的创建是不需要外部类的对象，因此，如果访问外部类的实例成员，必须通过外部类的实例去访问。*
- 在静态内部类中可以定义静态成员和实例成员。
- 测试类可以通过完整的类名直接访问静态内部类的静态成员。

<details>
<summary>Example6</summary>

```java
class Outter {
    static String name = "outter";
    public Integer age = 17;
    // 静态内部类:使用static修饰
    static class Inner {
        Inner() {
            // 静态内部类能直接访问外部类的静态成员
            System.out.println(name);// 输出 outter
            // 访问外部类的实例成员,必须通过外部类的实例去访问.
            System.out.println(new Outter().age);// 输出 17
        }
    }
}
```
</details>

&emsp;&emsp;静态内部类也是定义在另一个类里面的类，只不过在类的前面多了一个关键字static。静态内部类是不需要依赖于外部类的，它不持有指向外部类对象的引用this，并且它不能使用外部类的非static成员或方法，这点很好理解，因为在没有外部类的对象的情况下，可以创建静态内部类的对象，如果允许访问外部类的非static成员就会产生矛盾，因为外部类的非static成员必须依附于具体对象。它唯一的作用就是随着类的加载（而不是随着对象的产生）而产生。

其他类如何访问静态内部类：
1. 当要访问静态内部类中的非静态成员时：`new 外部类名.内部类名().内部方法名()`，`new  Outer.Inner().function()`
2. 当要访问静态类中的静态成员时 ：`外部类名.内部类名.内部方法名()`，`Outer.Inner.function()`

使用场景:
1. 外部类与内部类有很强的联系，需要通过内部类的方式维持嵌套的可读性。
2. 内部类可以单独创建。
3. 内部类不依赖于外部类，外部类需要使用内部类，而内部类不需使用外部类（或者不合适持有外部类的强引用）。

为什么不直接使用两个类，而要使用内部类？
1. 维持两者之间密切的联系
2. 保持嵌套可读性

#### 局部内部类
**1. 定义**：在方法中定义的内部类,其可见范围是当前方法,和局部变量是同一个级别,所以局部内部类只能在方法中使用。
**注意**，局部内部类和方法里面的局部变量一样，是不能有public、protected、private以及static修饰符的。
<details>
<summary>Example7</summary>

```java
public class InnerDemo3 {
    public static void main(String[] args) {
        // 局部内部类
        class Inner {
        }
    }
}
```
</details>

**2. 特点：**
- 局部内部类和实例内部类一样，不能包含静态成员。（局部内部类属于方法，而静态成员属于类）
- 局部内部类和实例内部类，可以访问外部类的所有成员。
- 局部内部类访问的局部变量必须使用final修饰，在Java8中是自动隐式加上final（语法糖）。
原因：当方法被调用运行完毕之后，当前方法的栈帧被销毁，方法内部的局部变量的空间全部销毁。但内部类对象可能还在堆内存中，要直到没有被引用时才会消亡。此时就会出现一种情况：内部类要访问一个不存在的局部变量。为了避免该问题，我们使用final修饰局部变量，从而变成常量，永驻内存空间，即使方法销毁之后，该局部变量也在内存中，对象可以继续持有。
<details>
<summary>Example8</summary>

```java
public class InnerDemo3 {
    public static void main(String[] args) {
        int age = 17;
        final int num = 15;
        // 局部内部类
        class Inner {
            public void test() {
                // 报错:Cannot refer to a non-final variable age inside an inner class defined in a different method
                System.out.println(age);
                System.out.println(num);// 正确
            }
        }
    }
}
```
</details>

#### 匿名内部类（使用最频繁）
1. 定义：匿名内部类是一个没有名称的局部内部类，适合于只使用一次的类。（匿名的含义是由编译器自动给内部类起一个内部名称）
*当创建一个匿名内部类时，会立即创建该类的一个实例对象，匿名类因为没有类名，所以不能重复使用。*
2. 创建匿名内部类
匿名内部类本身没有构造器，但是会调用父类构造器。一般来说，匿名内部类用于继承其他类或是实现接口，并不需要增加额外的方法，只是对继承方法的实现或是重写。
**注意**：匿名内部类必须继承一个父类或者实现一个接口，但最多只能一个父类或实现一个接口。
<details>
<summary>Example9</summary>

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
</details>

<!-- #### 静态内部类与线程安全
*Reference：*https://blog.csdn.net/xiaojin21cen/article/details/104532199 -->

## Lambda表达式
&emsp;&emsp;Lambda表达式可以将**某些**匿名内部类进一步简写，其本质也是一个“语法糖”，由编译器推断并转换包装为常规的代码，因此通过Lambda表达式可以使用更少的代码来实现同样的功能。可以认为，Lambda是一个匿名函数（相似于匿名内部类），作用是返回一个实现了接口的对象。
虽然Lambda表达式对某些接口进行简单的实现，但是并不是所有的接口都可以使用Lambda表达式来实现，要求接口种定义的必须要实现的抽象方法只能是一个（注意：具体方法可以多个或者没有）。如：
- Comparator
- Runnable
- Callable
在Java 8 中对接口增加了新特性：default，提供了一个默认的抽象方法，但是Lambda对此没有特殊的影响，方法可以按Lambda所表达的来。

&emsp;&emsp;以Comparator为例，我们想要调用Arrays.sort()时，可以传入一个Comparator实例，以匿名类方式编写如下：
<details>
<summary>Example10</summary>

```java
String[] array = new String[] { "Apple", "Orange", "Banana", "Lemon" };
Arrays.sort(array, new Comparator<String>() {
    public int compare(String s1, String s2) {
        return s1.compareTo(s2);
    }
});
```
</details>

&emsp;&emsp;上述写法非常繁琐。从Java 8开始，可以用Lambda表达式来替换单方法接口。上述代码可以改写为如下：
<details>
<summary>Example11</summary>

```java
import java.util.Arrays;

public class LambdaDemo1 {
    public static void main(String[] args) {
        String[] array = new String[] { "Apple", "Orange", "Banana", "Lemon" };
        Arrays.sort(array, (s1, s2) -> {
            return s1.compareTo(s2);
        });
        System.out.println(String.join(", ", array));
    }
}
```
</details>

观察Lambda表达式的写法，它只需要写出方法定义：
```java
(s1, s2) -> {
    return s1.compareTo(s2);
}
```
其中，参数是(s1, s2)，参数类型可以省略，因为编译器可以自动推断出String类型。-> { ... }表示方法体，所有代码写在内部即可。Lambda表达式没有class定义，因此写法非常简洁。
如果只有一行return xxx的代码，完全可以用更简单的写法：
`Arrays.sort(array, (s1, s2) -> s1.compareTo(s2));`
返回值的类型也是由编译器自动推断的，这里推断出的返回值是int，因此，只要返回int，编译器就不会报错。

***FunctionalInterface***
&emsp;&emsp;前文提到Lambda表达式可以用来替换单方法接口，这种只定义了单方法的接口称为FunctionalInterface，使用`@FunctionalInterface`标记。例如，Callable接口：
```java
@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```
在接收FunctionalInterface作为参数的时候，可以把实例化的匿名类改写为Lambda表达式，能大大简化代码。
#### 反编译分析
&emsp;&emsp;使用CFR工具反编译上述Example11，命令格式：` java -jar CFR的jar包位置D:\cfr_0_119.jar class文件.\LambdaDemo1.class --decodelambdas false`。
<details>
<summary>Example11反编译结果</summary>

```java
public class LambdaDemo1 {
    public static void main(String[] args) {
        CharSequence[] array = new String[]{"Apple", "Orange", "Banana", "Lemon"};
        Arrays.sort(array, (java.util.Comparator)LambdaMetafactory.metafactory(null, null, null, (Ljava/lang/Object;Ljava/lang/Object;)I, lambda$main$0(java.lang.String java.lang.String ), (Ljava/lang/String;Ljava/lang/String;)I)());
        System.out.println(String.join((CharSequence)", ", array));
    }

    private static /* synthetic */ int lambda$main$0(String s1, String s2) {
        return s1.compareTo(s2);
    }
}
```
</details>

可以看到，在编译时，该lambda表达式改写的方法被编译器生成了一个名为`lambda$main$0`的方法。

## try-with-resource语法
&emsp;&emsp;在JDK7之前，资源需要手动关闭，如下所示：
<details>
<summary>Example12</summary>

```java
BufferedWriter writer = null;
try {
   
    writer = Files.newBufferedWriter(file, charset);
    writer.write(s, 0, s.length());
} catch (IOException x) {
   
    System.err.format("IOException: %s%n", x);
} finally {
   
    if (writer != null) writer.close();
}
```
</details>

&emsp;&emsp;在JDK7之前，必须要牢记在finally中执行关闭资源的方法，否则随着程序不断运行，资源泄露将会累计成重大的生产事故，然而如果同时打开多个资源，那么关闭资源的操作就会很繁琐。例如：
<details>
<summary>Example13</summary>

```java
public class Demo {
   
    public static void main(String[] args) {
   
        BufferedInputStream bin = null;
        BufferedOutputStream bout = null;
        try {
   
            bin = new BufferedInputStream(new FileInputStream(new File("test.txt")));
            bout = new BufferedOutputStream(new FileOutputStream(new File("out.txt")));
            int b;
            while ((b = bin.read()) != -1) {
   
                bout.write(b);
            }
        }
        catch (IOException e) {
   
            e.printStackTrace();
        }
        finally {
   
            if (bin != null) {
   
                try {
   
                    bin.close();
                }
                catch (IOException e) {
   
                    e.printStackTrace();
                }
                finally {
   
                    if (bout != null) {
   
                        try {
   
                            bout.close();
                        }
                        catch (IOException e) {
   
                            e.printStackTrace();
                        }
                    }
                }
            }
        }
    }
}
```
</details>
&emsp;&emsp;在上面的示例中，关闭资源的代码比业务代码都要多！这是因为，不仅需要关闭BufferedInputStream，还需要保证如果关闭BufferedInputStream时出现了异常， BufferedOutputStream也要能被正确地关闭。所以我们不得不借助finally中嵌套finally大法。可以想象，打开的资源越多，finally中嵌套的将会越深！

&emsp;&emsp;而从JDK 1.7 开始，java引入了 try-with-resources 声明，作用就是将省去手动关流，这其实是一种语法糖，在编译时会进行转化为 try-catch-finally 语句。如下就是try-with-resources的一个用法示例：
<details>
<summary>Example14</summary>

```java
public class TryWithResourceDemo1 {
    public static void main(String[] args) {
        try (InputStream inputStream = new FileInputStream(new File("xxx"))) {
            inputStream.read();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
</details>
<details>
<summary>Example14反编译结果</summary>

```java
public class TryWithResourceDemo1
{

    public TryWithResourceDemo1()
    {
    }

    public static void main(String args[])
    {
        InputStream inputStream;
        Throwable throwable;
        inputStream = new FileInputStream(new File("xxx"));
        throwable = null;
        try
        {
            inputStream.read();
        }
        catch(Throwable throwable2)
        {
            throwable = throwable2;
            throw throwable2;
        }
        if(inputStream != null)
            if(throwable != null)
                try
                {
                    inputStream.close();
                }
                catch(Throwable throwable1)
                {
                    throwable.addSuppressed(throwable1);
                }
            else
                inputStream.close();
        break MISSING_BLOCK_LABEL_103;
        Exception exception;
        exception;
        if(inputStream != null)
            if(throwable != null)
                try
                {
                    inputStream.close();
                }
                catch(Throwable throwable3)
                {
                    throwable.addSuppressed(throwable3);
                }
            else
                inputStream.close();
        throw exception;
        Exception e;
        e;
        e.printStackTrace();
    }
}
```
</details>

&emsp;&emsp;可以看到，生成的 try-with-resources 经过编译后还是使用的 try…catch…finally 语句，只不过这部分工作由编译器替我们做了，这样能让我们的代码更加简洁，从而消除样板代码。
**注意**：使用try-with-resources来自动关流最主要的一点是变量必须是实现AutoCloseable接口，比如：InputStream、OutputStream。
当try-with-resources语句存放多个资源时，
```java
 try (ZipFile zf = new ZipFile(zipFileName);
     BufferedWriter writer = newBufferedWriter(outputFilePath, charset)
    ) {
//执行任务
}
```
在任务执行完毕或者出现异常中断之后是根据new的反向顺序调用各资源的close()的。例如这里先关闭writer再关闭zf。


## 包装类自动装箱与拆箱
1. 定义：基本数据类型和包装类之间可以自动地相互转换
#### 为什么需要将基本数据类型包装为类
&emsp;&emsp;在Java中，一切皆为对象，Java中的许多设计是针对类与对象来完成的，但八大基本类型却不是对象，在进行面向对象的开发时，基本类型的使用存在许多不便。例如一些数据类型的转换，int数据类型取值范围的获取等等，需要重复造轮子。假如没有包装类，想要通过String转换成int，会非常麻烦，而有包装类之后，通过调用包装类提供的方法便可以完成类型转换。
<details>
<summary>Example15</summary>

```java
public class Test {
    public static void main(String[] args) {
        // 数据库中的商品数量 number
        String number = "666";
        // 借助封装类 Integer 转换为 int
        int intVal = Integer.valueOf(number);
        // 借助封装类 Float 转换为 float
        float floatVal = Float.valueOf(number);
        // 借助封装类 Long 转换为 long
        long longVal = Long.valueOf(number);
        // 依次输出三个值的内容
        System.out.println("int="+intVal);
        System.out.println("floatVal="+floatVal);
        System.out.println("longVal="+longVal);
    }
}
```
</details>

&emsp;&emsp;在将基本类型包装为类后，那么就可以在它们的内部定义成员方法来提供丰富便利的功能，借助于泛型的使用，可以不必为基本数据类型单独编写方法。
#### 自动装箱与拆箱过程
&emsp;&emsp;自动装箱用法：包装类类型 变量名=基本类型值/变量；
&emsp;&emsp;自动拆箱用法：基本类型 变量名=包装类变量；
```java
public class AutoboxingAndUnboxingDemo1 {
    public static void main(String[] args) {
        Integer a = 100;
        int b = a;
    }
}
```
&emsp;&emsp;通过编译成class文件，再将class文件反编译出来，结果如下：
```java
public class AutoboxingAndUnboxingDemo1
{

    public AutoboxingAndUnboxingDemo1()
    {
    }

    public static void main(String args[])
    {
        Integer a = Integer.valueOf(100);
        int b = a.intValue();
    }
}
```
&emsp;&emsp;可以看到，装箱的过程是编译时自动调用了包装类的valueOf()方法完成类型转换，拆箱过程是编译器自动调用了包装类的intValue()方法完成取值。

#### 装箱与拆箱的使用场景
1. 装箱：在基本类型的值赋值给包装类型时会自动触发。例如：`Integer a = 1;`
这时在编译后的文件中实际上是`Integer a = Integer.valueOf(1);`
2. 拆箱:
- 在包装类型**赋值给基本类型**时会自动触发。
例如：`Integer a = 1;int b = a; // a 是Integer类型将Integer类型赋值给int类型，触发拆箱`
- 在做**运算符运算**时触发。
例如：
```java
Integer a = 1;
Integer b = 2;
System.out.print(a * b); //这时a*b在二进制文件中被编译成a.intValue() * b.intValue();
```
3. **\==运算**时，如果 a 和 b 都是Integer类型，则不会拆箱，因为\==也可以直接比较对象，表示a和b是否指向同一对象地址。因此这里并不是比较值是否相等了。而如果a 和 b 中有一个是int类型，另一个是Integer 类型，则会触发拆箱，然后对两个int值进行比较。

#### 需要注意的问题
**拆箱过程中的空指针（NPE）问题**
&emsp;&emsp;拆箱给我们带来便利的同时，有时候也会带来NPE的危害！如下提供了两个NPE案例。
NPE错误案例一：返回类型为基本数据类型，return的是包装数据类型的对象时，若该包装数据类型存在被置为null的可能,则自动拆箱可能产生 NPE错误。
<details>
<summary>Example16</summary>

```java
public class NPEDemo1 {
    public static void main(String[] args) {
        System.out.println(test());
    }
    public static int test() {
        Integer result = null;
        return result;
    }
}
```
</details>

NPE错误案例二：包装类在发生符号运算的时候会进行拆箱，这个自动拆箱过程有可能产生 NPE。
<details>
<summary>Example17</summary>

```java
public class NPEDemo2 {
    public static void main(String[] args) {
        Integer a = 1;
        Integer b = 2;
        Integer c = null;
        Boolean flag = false;
        //由于a*b的结果是int类型，那么c也会跟着被强制拆箱成int类型，而c=null，因此会出现NPE异常
        Integer result = (flag ? a * b : c);
    }
}
```
</details>

**包装类的缓存机制**
&emsp;&emsp;在包装类的源码中，对`Byte,Short,Integer,Long` 这 4 种包装类默认创建了数值 [-128，127] 的相应类型的缓存数据，`Character` 创建了数值在 [0,127] 范围的缓存数据，`Boolean` 则直接返回了true和false。而Float和Double没有缓存机制。因此在缓存范围内的包装类实际是同一对象，可以使用==比较，而在缓存范围外的包装类，则需要使用equals()方法比较。
<details>
<summary>Example18</summary>

```java
public class AutoboxingAndUnboxingDemo2 {
    public static void main(String[] args) {
        Integer a = 127;
        Integer b = 127;
        Integer c = 128;
        Integer d = 128;

        System.out.println(a == b); //true
        System.out.println(c == d); //false
        System.out.println(c.equals(d)); //true
    }
}
```
</details>

## 增强for循环
&emsp;&emsp;在使用普通for循环时，需要先获取遍历对象的长度，循环中还需要确定数组的索引是多少。这种写法明显笔记繁琐。而增强for与之相比，功能更强并且代码更加简洁，无需知道遍历的次数和数组的索引即可进行遍历。
<details>
<summary>Example19</summary>

```java
public class EnhanceForDemo1 {
    public static void main(String[] args) {
        String[] params = new String[]{"hello","world"};
        //增强for循环对象为数组
        for(String str : params){
            System.out.println(str);
        }

        List<String> lists = Arrays.asList("hello","world");
        //增强for循环对象实现Iterable接口
        for(String str : lists){
            System.out.println(str);
        }
    }
}
```
</details>

<details>
<summary>jad反编译结果</summary>

```java
public class EnhanceForDemo1
{

    public EnhanceForDemo1()
    {
    }

    public static void main(String args[])
    {
        String params[] = {
            "hello", "world"
        };
        String args1[] = params;
        int i = args1.length;
        for(int j = 0; j < i; j++)
        {
            String str = args1[j];
            System.out.println(str);
        }

        List lists = Arrays.asList(new String[] {
            "hello", "world"
        });
        String str;
        for(Iterator iterator = lists.iterator(); iterator.hasNext(); System.out.println(str))
            str = (String)iterator.next();

    }
}
```
</details>

&emsp;&emsp;可以看到，在对数组进行增强for循环时，其内部仍是使用的for循环进行遍历，只是获取数组大小这一步由编译器完成了。而对于继承了Iterator接口的对象进行增强for循环遍历时，在编译器解析后调用了其迭代器的`hasNext()`方法和`next()`方法进行遍历。

## 变长参数
&emsp;&emsp;变长参数也是一个比较小众的用法，所谓变长参数，就是方法可以接受长度不定确定的参数。一般我们开发不会使用到变长参数，而且变长参数也不推荐使用，它会使我们的程序变的难以处理。但是我们有必要了解一下变长参数的特性。
<details>
<summary>Example20</summary>

```java
public class VariableLengthParameterDemo1 {
    public static void printMessage(String... args) {
        for (String str : args) {
            System.out.println("str = " + str);
        }
    }

    public static void main(String[] args) {
        VariableLengthParameterDemo1.printMessage("l", "am", "cxuan");
    }
}
```
</details>


<details>
<summary>Example20反编译结果</summary>

```java
public class VariableLengthParameterDemo1
{

    public VariableLengthParameterDemo1()
    {
    }

    public static transient void printMessage(String args[])
    {
        String as[] = args;
        int i = as.length;
        for(int j = 0; j < i; j++)
        {
            String str = as[j];
            System.out.println((new StringBuilder()).append("str = ").append(str).toString());
        }

    }

    public static void main(String args[])
    {
        printMessage(new String[] {
            "l", "am", "cxuan"
        });
    }
}
```
</details>

&emsp;&emsp;通过上面示例发现编译后有很多地方发生了变化：
1. 可变长参数变成了数组
2. "str = " + str字符串拼接转换成了StringBuilder进行拼接
3. 增强for循环变成了原始的循环
**可见** 可变长参数就是在编译的时候转换成了数组，使用变长参数有两个条件，一是变长的那一部分参数具有相同的类型，二是变长参数必须位于方法参数列表的最后面。
