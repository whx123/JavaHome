## 前言
前几天，在茫茫的互联网海洋中寻寻觅觅，我收藏了800道Java基础经典面试题，有小伙伴私聊我要答案。所以感觉没有答案的面试题是没有灵魂的，于是今天先整理基础篇的前80道答案出来~

所有的Java面试题已经上传github，答案也上传了一部分~
> https://github.com/whx123/JavaHome/tree/master/Java%E9%9D%A2%E8%AF%95%E9%A2%98%E9%9B%86%E7%BB%93%E5%8F%B7

## Java 基础
### 1. equals与==的区别
![](https://user-gold-cdn.xitu.io/2020/5/10/171ff375ad2f6e94?w=720&h=404&f=png&s=237039)
**==**
- 如果是基本类型，==表示判断它们值是否相等；
- 如果是引用对象，==表示判断两个对象指向的内存地址是否相同。

**equals**
- 如果是字符串，表示判断字符串内容是否相同；
- 如果是object对象的方法，比较的也是引用的内存地址值；
- 如果自己的类重写equals方法，可以自定义两个对象是否相等。

### 2. final, finally, finalize 的区别

- final 用于修饰属性,方法和类, 分别表示属性不能被重新赋值, 方法不可被覆盖, 类不可被继承.
- finally 是异常处理语句结构的一部分，一般以ty-catch-finally出现，finally代码块表示总是被执行.
- finalize 是Object类的一个方法，该方法一般由垃圾回收器来调用，当我们调用System.gc() 方法的时候，由垃圾回收器调用finalize()方法，回收垃圾，JVM并不保证此方法总被调用.

### 3. 重载和重写的区别
- 重写必须继承，重载不用。
- 重载表示同一个类中可以有多个名称相同的方法，但这些方法的参数列表各不相同（即参数个数或类型不同）
- 重写表示子类中的方法与父类中的某个方法的名称和参数完全相同啦，通过子类实例对象调用这个方法时，将调用子类中的定义方法，这相当于把父类中定义的那个完全相同的方法给覆盖了，这是面向对象编程的多态性的一种表现。
- 重写的方法修饰符大于等于父类的方法，即访问权限只能比父类的更大，不能更小，而重载和修饰符无关。
- 重写覆盖的方法中，只能比父类抛出更少的异常，或者是抛出父类抛出的异常的子异常，因为不能坑爹啊哈哈~

### 4. 两个对象的hashCode()相同，则 equals()是否也一定为 true？
两个对象equals相等，则它们的hashcode必须相等，如果两个对象的hashCode()相同，则equals()不一定为true。

**hashCode 的常规协定：**

- 在 Java 应用程序执行期间，在对同一对象多次调用 hashCode 方法时，必须一致地返回相同的整数，前提是将对象进行 equals 比较时所用的信息没有被修改。从某一应用程序的一次执行到同一应用程序的另一次执行，该整数无需保持一致。
- 两个对象的equals()相等，那么对这两个对象中的每个对象调用 hashCode 方法都必须生成相同的整数结果。
- 两个对象的equals()不相等，那么对这两个对象中的任一对象上调用 hashCode 方法不要求一定生成不同的整数结果。但是，为不相等的对象生成不同整数结果可以提高哈希表的性能。


### 5. 抽象类和接口有什么区别
- 抽象类要被子类继承，接口要被子类实现。
- 抽象类可以有构造方法，接口中不能有构造方法。
- 抽象类中可以有普通成员变量，接口中没有普通成员变量，它的变量只能是公共的静态的常量
- 一个类可以实现多个接口，但是只能继承一个父类，这个父类可以是抽象类。
- 接口只能做方法声明，抽象类中可以作方法声明，也可以做方法实现。
- 抽象级别（从高到低）：接口>抽象类>实现类。
- 抽象类主要是用来抽象类别，接口主要是用来抽象方法功能。
- 抽象类的关键字是abstract，接口的关键字是interface

### 6. BIO、NIO、AIO 有什么区别？
这个答案来自互联网哈，个人觉得是最好理解的~

> - BIO：线程发起 IO 请求，不管内核是否准备好 IO 操作，从发起请求起，线程一直阻塞，直到操作完成。
> - NIO：线程发起 IO 请求，立即返回；内核在做好 IO 操作的准备之后，通过调用注册的回调函数通知线程做 IO 操作，线程开始阻塞，直到操作完成。
> - AIO：线程发起 IO 请求，立即返回；内存做好 IO 操作的准备之后，做 IO 操作，直到操作完成或者失败，通过调用注册的回调函数通知线程做 IO 操作完成或者失败。
 

BIO 是一个连接一个线程。,NIO 是一个请求一个线程。,AIO 是一个有效请求一个线程。
 
> - BIO：同步并阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善。
> - NIO：同步非阻塞，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。
> - AIO：异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的 IO 请求都是由 OS 先完成了再通知服务器应用去启动线程进行处理。

### 7. String，Stringbuffer，StringBuilder的区别

**String：**

- String类是一个不可变的类，一旦创建就不可以修改。
- String是final类，不能被继承
- String实现了equals()方法和hashCode()方法

**StringBuffer：**

- 继承自AbstractStringBuilder，是可变类。
- StringBuffer是线程安全的
- 可以通过append方法动态构造数据。

**StringBuilder：**

- 继承自AbstractStringBuilder，是可变类。
- StringBuilder是非线性安全的。
- 执行效率比StringBuffer高。

### 8. JAVA中的几种基本数据类型是什么，各自占用多少字节呢
| 基本类型 | 位数| 字节 |
| ------ | ------ | ------ |
| int | 32 | 4 |
| short | 16 | 2 |
| long | 64 | 8 |
| byte | 8 | 1 |
| char | 16 | 2 |
| float | 32 | 4 |
| double | 64 | 8|
| boolean | ？ | ？ |

对于boolean，官方文档未明确定义，它依赖于 JVM 厂商的具体实现。逻辑上理解是占用 1位，但是实际中会考虑计算机高效存储因素

感兴趣的小伙伴，可以去看[官网](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)

### 9. Comparator与Comparable有什么区别？
在牛客网看到这道题的答案，觉得写的最好~

> 链接：https://www.nowcoder.com/questionTerminal/99f7d1f4f8374e419a6d6924d35d9530
来源：牛客网
> - Comparable & Comparator 都是用来实现集合中元素的比较、排序的，只是 Comparable 是在集合内部定义的方法实现的排序，Comparator 是在集合外部实现的排序，所以，如想实现排序，就需要在集合外定义 Comparator 接口的方法或在集合内实现 Comparable 接口的方法。
> -  Comparator位于包java.util下，而Comparable位于包 java.lang下。
> -  Comparable 是一个对象本身就已经支持自比较所需要实现的接口（如 String、Integer 自己就可以完成比较大小操作，已经实现了Comparable接口） 自定义的类要在加入list容器中后能够排序，可以实现Comparable接口，在用Collections类的sort方法排序时，如果不指定Comparator，那么就以自然顺序排序， 这里的自然顺序就是实现Comparable接口设定的排序方式。
> -  而 Comparator 是一个专用的比较器，当这个对象不支持自比较或者自比较函数不能满足你的要求时，你可以写一个比较器来完成两个对象之间大小的比较。 
> - 可以说一个是自已完成比较，一个是外部程序实现比较的差别而已。 用 Comparator 是策略模式（strategy design pattern），就是不改变对象自身，而用一个策略对象（strategy object）来改变它的行为。 比如：你想对整数采用绝对值大小来排序，Integer 是不符合要求的，你不需要去修改 Integer 类（实际上你也不能这么做）去改变它的排序行为，只要使用一个实现了 Comparator 接口的对象来实现控制它的排序就行了。

### 10. String类能被继承吗，为什么。
首先，String是一个final修饰的类，final修饰的类不可以被继承。
```
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
```
**String类为什么不能被继承呢？**

有两个原因：
- 效率性，String 类作为最常用的类之一，禁止被继承和重写，可以提高效率。
- 安全性，String 类中有很多调用底层的本地方法，调用了操作系统的 API，如果方法可以重写，可能被植入恶意代码，破坏程序。


###  11. 说说Java中多态的实现原理
- 多态机制包括静态多态（编译时多态）和动态多态（运行时多态）
- 静态多态比如说重载，动态多态一般指在运行时才能确定调用哪个方法。
- 我们通常所说的多态一般指运行时多态，也就是编译时不确定究竟调用哪个具体方法，一直等到运行时才能确定。
- 多态实现方式：子类继承父类（extends）和类实现接口（implements）
- 多态核心之处就在于对父类方法的改写或对接口方法的实现，以取得在运行时不同的执行效果。
- Java 里对象方法的调用是依靠类信息里的方法表实现的，对象方法引用调用和接口方法引用调用的大致思想是一样的。当调用对象的某个方法时，JVM查找该对象类的方法表以确定该方法的直接引用地址，有了地址后才真正调用该方法。

举个例子吧，假设有个Fruit父类，一个taste味道方法，两个子类Apple和Pear，如下：
```
abstract class Fruit {
    abstract String taste() ;
}

class Apple extends Fruit {
    @Override
    String taste() {
        return "酸酸的";
    }
}

class Pear extends Fruit {
    @Override
    String taste() {
        return "甜甜的";
    }
}
public class Test {
    public static void main(String[] args) {
        Fruit f = new Apple();
        System.out.println(f.taste());
    }
}
```
程序运行，当调用对象Fruit f的方法taste时，JVM查找Fruit对象类的方法表以确定taste方法的直接引用地址，到底来自Apple还是Pear，确定后才真正调用对应子类的taste方法，

### 12. Java泛型和类型擦除
这个面试题，可以看我这篇文章哈~

[Java程序员必备基础：泛型解析](https://juejin.im/post/5e1954c0f265da3e1e0568de)

### 13. int和Integer 有什么区别，还有Integer缓存的实现
这里考察3个知识点吧：
- int 是基本数据类型，interger 是 int 的封装类
- int 默认值为 0 ，而interger 默认值为 null， Interger使用需要判空处理
- Integer的缓存机制：为了节省内存和提高性能，Integer类在内部通过使用相同的对象引用实现缓存和重用，Integer类默认在-128 ~ 127 之间，可以通过 -XX:AutoBoxCacheMax进行修改，且这种机制仅在自动装箱的时候有用，在使用构造器创建Integer对象时无用。

看个Integer的缓存的例子，加深一下印象哈：

```
Integer a = 10;
Integer b = 10;

Integer c = 129;
Integer d = 129;
System.out.println(a == b);
System.out.println(c == d);
输出结果：
true
false
```

### 14. 说说反射的用途及实现原理，Java获取反射的三种方法
这道面试题，看我这篇文章哈：[谈谈Java反射：从入门到实践，再到原理](https://juejin.im/post/5de3242e6fb9a071886675d7)

Java获取反射的**三种方法**：
- 第一种，使用 Class.forName 静态方法。
- 第二种，使用类的.class 方法
- 第三种，使用实例对象的 getClass() 方法。

### 15. 面向对象的特征
面向对象的三大特征：
- 封装
- 继承
- 多态

### 16. &和&&的区别
- 按位与, a&b 表示把a和b都转换成二进制数，再进行与的运算；
- &和&&都是逻辑运算符号，&&又叫短路运算符
- 逻辑与，a&& b ，a&b 都表示当且仅当两个操作数均为 true时，其结果才为 true，否则为false。
- 逻辑与跟短路与的差别是非常巨大的，虽然二者都要求运算符左右两端的布尔值都是true，整个表达式的值才是true。但是，&&之所以称为短路运算，是因为如果&&左边的表达式的值是false，右边的表达式会被直接短路掉，不会进行运算。

### 17. Java中IO流分为几种?
- Java中的流分为两种：一种是字节流，另一种是字符流。
- IO流分别由四个抽象类来表示（两输入两输出）:InputStream，OutputStream，Reader，Writer。

### 18. 讲讲类的实例化顺序，比如父类静态数据，构造函数，子类静态数据，构造函数。
直接看个例子吧：
```
public class Parent {
    {
        System.out.println("父类非静态代码块");
    }
    static {
        System.out.println("父类静态块");
    }
    public Parent() {
        System.out.println("父类构造器");
    }
}
public class Son extends Parent {
    public Son() {
        System.out.println("子类构造器");
    }
    static {
        System.out.println("子类静态代码块");
    }
    {
        System.out.println("子类非静态代码块");
    }
}
public class Test {
    public static void main(String[] args) {
        Son son = new Son();
    }
}
```
运行结果：

```
父类静态块
子类静态代码块
父类非静态代码块
父类构造器
子类非静态代码块
子类构造器
```

所以，**类实例化顺序为：**
父类静态代码块/静态域->子类静态代码块/静态域 -> 父类非静态代码块 -> 父类构造器 -> 子类非静态代码块 -> 子类构造器

### 19. Java创建对象有几种方式
Java创建对象有5种方式
- 用new语句创建对象。
- 使用反射，使用Class.newInstance()创建对象/调用类对象的构造方法——Constructor
- 调用对象的clone()方法。
- 运用反序列化手段，调用java.io.ObjectInputStream对象的readObject()方法.
- 使用Unsafe

### 20. 如何将GB2312编码的字符串转换为ISO-8859-1编码的字符串呢？

```
public class Test {
    public static void main(String[] args) throws UnsupportedEncodingException {
        String str = "捡田螺的小男孩";
        String strIso = new String(str.getBytes("GB2312"), "ISO-8859-1");
        System.out.println(strIso);
    }
}
```
### 21. 守护线程是什么？用什么方法实现守护线程
- 守护线程是运行在后台的一种特殊进程。
- 它独立于控制终端并且周期性地执行某种任务或等待处理某些发生的事件。
- 在 Java 中垃圾回收线程就是特殊的守护线程。

### 22. notify()和 notifyAll()有什么区别？
- notify是唤醒一个处于该对象wait的线程，而notifyAll是唤醒所有处于该对象wait的线程。
- 但是唤醒不等于就能执行了，需要得到锁对象才能有权利继续执行，而锁只有一把，所以多个线程被唤醒时需要争取该锁。

### 23. Java语言是如何处理异常的，关键字throws、throw、try、catch、finally怎么使用？
这道面试题，可以看我这篇文章哈：[Java程序员必备：异常的十个关键知识点](https://juejin.im/post/5dc68c0f51882528957fadb3)

### 24. 谈谈Java的异常层次结构

![](https://user-gold-cdn.xitu.io/2020/5/17/17221f084e60767b?w=1280&h=890&f=png&s=352606)

从前从前，有位老人，他的名字叫**Throwable**，他生了两个儿子，大儿子叫**Error**,二儿子叫**Exception**。

**Error**

表示编译时或者系统错误，如虚拟机相关的错误，OutOfMemoryError等，error是无法处理的。

**Exception**

代码异常，Java程序员关心的基类型通常是Exception。它能被程序本身可以处理，这也是它跟Error的区别。

它可以分为RuntimeException（运行时异常）和CheckedException（可检查的异常）。

**常见的RuntimeException异常：**
```
- NullPointerException 空指针异常
- ArithmeticException 出现异常的运算条件时，抛出此异常
- IndexOutOfBoundsException 数组索引越界异常
- ClassNotFoundException 找不到类异常
- IllegalArgumentException(非法参数异常)
```

**常见的 Checked Exception 异常：**

```
- IOException (操作输入流和输出流时可能出现的异常)
- ClassCastException(类型转换异常类)
```
- Checked Exception就是编译器要求你必须处置的异常。
- 与之相反的是，Unchecked Exceptions，它指编译器不要求强制处置的异常，它包括Error和RuntimeException 以及他们的子类。

### 25. 静态内部类与非静态内部类有什么区别
这道面试题，可以看我这篇文章哈：[Java程序员必备基础：内部类解析](https://juejin.im/post/5e105e1ef265da5d61695a45)

- 静态内部类可以有静态成员(方法，属性)，而非静态内部类则不能有静态成员(方法，属性)。
- 静态内部类只能够访问外部类的静态成员和静态方法,而非静态内部类则可以访问外部类的所有成员(方法，属性)。
- 实例化静态内部类与非静态内部类的方式不同
- 调用内部静态类的方法或静态变量,可以通过类名直接调用


### 26. String s与new String与有什么区别
```
String str ="whx";
String newStr =new String ("whx");
```

**String str ="whx"**

先在常量池中查找有没有"whx" 这个对象,如果有，就让str指向那个"whx".如果没有，在常量池中新建一个“whx”对象，并让str指向在常量池中新建的对象"whx"。

**String newStr =new String ("whx");**

是在堆中建立的对象"whx" ,在栈中创建堆中"whx" 对象的内存地址。

如图所示：

![](https://user-gold-cdn.xitu.io/2020/1/30/16ff695269a49781?w=917&h=545&f=png&s=56724)

网上这篇文章讲的挺好的：
[String和New String()的区别](https://blog.csdn.net/fullstack/article/details/23885879)

### 27. 反射中，Class.forName和ClassLoader的区别
Class.forName和ClassLoader都可以对类进行加载。它们区别在哪里呢？
**ClassLoader**负责加载 Java 类的字节代码到 Java 虚拟机中。Class.forName其实是调用了ClassLoader，如下：

![](https://user-gold-cdn.xitu.io/2020/1/31/16ffbda909fc7bee?w=1163&h=263&f=png&s=38067)
这里面，forName0的第二个参数为true，表示对加载的类进行初始化化。其实还可以调用```Class<?> forName(String name, boolean initialize,
    ClassLoader loader)```方法实现一样的功能，它的源码如下：

![](https://user-gold-cdn.xitu.io/2020/1/31/16ffbdf82a973dd0?w=1080&h=757&f=png&s=120576)

所以，Class.forName和ClassLoader的区别，就是在类加载的时候，class.forName有参数控制是否对类进行初始化。

### 28. JDK动态代理与cglib实现的区别
- java动态代理是利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。
- cglib动态代理是利用asm开源包，对代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。
- JDK动态代理只能对实现了接口的类生成代理，而不能针对类
- cglib是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法。因为是继承，所以该类或方法最好不要声明成final 

网上这篇文章写得不错，[描述Java动态代理的几种实现方式，分别说出相应的优缺点](https://blog.csdn.net/qq_23000805/article/details/89573804)

### 29. error和exception的区别，CheckedException，RuntimeException的区别。
![](https://user-gold-cdn.xitu.io/2020/2/1/16ffc91a38aadaa0?w=1370&h=953&f=png&s=122496)

**Error:** 表示编译时或者系统错误，如虚拟机相关的错误，OutOfMemoryError等，error是无法处理的。

**Exception:** 代码异常，Java程序员关心的基类型通常是Exception。它能被程序本身可以处理，这也是它跟Error的区别。

它可以分为RuntimeException（运行时异常）和CheckedException（可检查的异常）。
常见的RuntimeException异常：
```
- NullPointerException 空指针异常
- ArithmeticException 出现异常的运算条件时，抛出此异常
- IndexOutOfBoundsException 数组索引越界异常
- ClassNotFoundException 找不到类异常
- IllegalArgumentException(非法参数异常)
```

常见的 Checked Exception 异常：
```
- IOException (操作输入流和输出流时可能出现的异常)
- ClassCastException(类型转换异常类)
```

有兴趣可以看我之前写得这篇文章：
[Java程序员必备：异常的十个关键知识点](https://juejin.im/post/5dc68c0f51882528957fadb3)

### 30. 深拷贝和浅拷贝区别

**浅拷贝**

复制了对象的引用地址，两个对象指向同一个内存地址，所以修改其中任意的值，另一个值都会随之变化。
![](https://user-gold-cdn.xitu.io/2020/2/1/16ffca9fd5f38501?w=1140&h=596&f=png&s=68287)

**深拷贝**

将对象及值复制过来，两个对象修改其中任意的值另一个值不会改变

![](https://user-gold-cdn.xitu.io/2020/2/1/16ffcab48469215e?w=1146&h=554&f=png&s=54807)

### 31. JDK 和 JRE 有什么区别？
- JDK：Java Development Kit 的简称，Java 开发工具包，提供了 Java 的开发环境和运行环境。
- JRE：Java Runtime Environment 的简称，Java 运行环境，为 Java 的运行提供了所需环境。

### 32. String 类的常用方法都有那些呢？
- indexOf()：返回指定字符的索引。
- charAt()：返回指定索引处的字符。
- replace()：字符串替换。
- trim()：去除字符串两端空白。
- split()：分割字符串，返回一个分割后的字符串数组。
- getBytes()：返回字符串的 byte 类型数组。
- length()：返回字符串长度。
- toLowerCase()：将字符串转成小写字母。
- toUpperCase()：将字符串转成大写字符。
- substring()：截取字符串。
- equals()：字符串比较。

### 33. 谈谈自定义注解的场景及实现
- 之前我这边有这么一个业务场景，用Redis控制接口调用频率，有使用过自定义注解。
- 通过 AOP（动态代理机制）给方法添加切面，通过反射来获取方法包含的注解，如果包含自定义关键字注解，就通过Redis进行校验拦截请求。

有关于注解，建议大家看一下java编程思想的注解篇章哈~

### 34. 说说你熟悉的设计模式有哪些？

设计模式分为三大类：
- 创建型模式：工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式（5种）
- 结构型模式：适配器模式、装饰者模式、代理模式、外观模式、桥接模式、组合模式、享元模式。（7种）
- 行为型模式：策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式。（11种）

最好平时积累一下，单例模式（7种实现方式），工厂模式，模板方法设计模式，策略模式，装饰者模式、代理模式这几种怎么写吧~

个人觉得，可以看看Hollis大神相关设计模式的文章哈，写得特别好！[设计模式（二）——单例模式](http://www.hollischuang.com/archives/1373)

### 35. 抽象工厂和工厂方法模式的区别？

![](https://user-gold-cdn.xitu.io/2020/5/17/1722243c2208ae97?w=1231&h=321&f=png&s=33937)
可以看一下这篇文章介绍：[抽象工厂模式-与-工厂方法模式区别](https://blog.csdn.net/wyxhd2008/article/details/5597975)
### 36. 什么是值传递和引用传递？
- 值传递是对基本型变量而言的,传递的是该变量的一个副本，改变副本不影响原变量.
- 引用传递一般是对于对象型变量而言的,传递的是该对象地址的一个副本, 并不是原对象本身 。 所以对引用对象进行操作会同时改变原对象.

### 37. 可以在static环境中访问非static变量吗？
> static变量在Java中是属于类的，它在所有的实例中的值是一样的。当类被Java虚拟机载入的时候，会对static变量进行初始化。因为静态的成员属于类，随着类的加载而加载到静态方法区内存，当类加载时，此时不一定有实例创建，没有实例，就不可以访问非静态的成员。类的加载先于实例的创建，因此静态环境中，不可以访问非静态！

### 38. Java支持多继承么,为什么？
不支持多继承，原因:
- 安全性的考虑，如果子类继承的多个父类里面有相同的方法或者属性，子类将不知道具体要继承哪个。
-  Java提供了接口和内部类以达到实现多继承功能，弥补单继承的缺陷。

### 39. 用最有效率的方法计算2乘以8？

```
2 << 3
```
- 将一个数左移n位，就相当于这个数乘以了2的n次方。
- 那么，一个数乘以8只要将其左移3位即可。
- 而cpu直接支持位移运算，且效率最高。

### 40. 构造器是否可被重写？
构造器是不能被继承的，因为每个类的类名都不相同，而构造器名称与类名相同，所以谈不上继承。 
又由于构造器不能被继承，所以相应的就不能被重写了。

### 41. char型变量中能不能存贮一个中文汉字，为什么？
 在Java中，char类型占2个字节，而且Java默认采用Unicode编码，一个Unicode码是16位，所以一个Unicode码占两个字节，Java中无论汉子还是英文字母都是用Unicode编码来表示的。所以，在Java中，char类型变量可以存储一个中文汉字。
 
```
char ch = '啦';
System.out.println("char:" + ch);
```
### 42. 如何实现对象克隆？
- 实现 Cloneable 接口，重写 clone() 方法。
- Object 的 clone() 方法是浅拷贝，即如果类中属性有自定义引用类型，只拷贝引用，不拷贝引用指向的对象。
- 对象的属性的Class 也实现 Cloneable 接口，在克隆对象时也手动克隆属性，完成深拷贝
- 结合序列化(JDK java.io.Serializable 接口、JSON格式、XML格式等)，完成深拷贝

### 43. object中定义了哪些方法？

- getClass(); 获取类结构信息
- hashCode() 获取哈希码
- equals(Object) 默认比较对象的地址值是否相等，子类可以重写比较规则
- clone() 用于对象克隆
- toString() 把对象转变成字符串
- notify() 多线程中唤醒功能
- notifyAll() 多线程中唤醒所有等待线程的功能
- wait()  让持有对象锁的线程进入等待
- wait(long timeout) 让持有对象锁的线程进入等待，设置超时毫秒数时间
- wait(long timeout, int nanos) 让持有对象锁的线程进入等待，设置超时纳秒数时间
- finalize() 垃圾回收前执行的方法


### 44. hashCode的作用是什么？
> - hashCode的存在主要是用于查找的快捷性，如Hashtable，HashMap等，hashCode是用来在散列存储结构中确定对象的存储地址的；
> - 如果两个对象相同，就是适用于equals(java.lang.Object) 方法，那么这两个对象的hashCode一定要相同；
> - 如果对象的equals方法被重写，那么对象的hashCode也尽量重写，并且产生hashCode使用的对象，一定要和equals方法中使用的一致，否则就会违反上面提到的第2点；
> - 两个对象的hashCode相同，并不一定表示两个对象就相同，也就是不一定适用于equals(java.lang.Object) 方法，只能够说明这两个对象在散列存储结构中.

这篇文章讲得挺详细的：[Java中hashCode的作用](https://blog.csdn.net/fenglibing/article/details/8905007)

### 45. for-each与常规for循环的效率对比
关于这个问题,《Effective Java》给我们做的解答：
> for-each能够让代码更加清晰，并且减少了出错的机会。 下面的惯用代码适用于集合与数组类型：
> 
> ```
> for (Element e : elements) {
>      doSomething(e); 
> }
> ```
> 使用for-each循环与常规的for循环相比，并不存在性能损失，即使对数组进行迭代也是如此。实际上，在有些场合下它还能带来微小的性能提升，因为它只计算一次数组索引的上限。

### 46. 写出几种单例模式实现，懒汉模式和饿汉模式区别
7种：
- 第一种（懒汉，线程不安全）
- 第二种（懒汉，线程安全）
- 第三种（饿汉）
- 第四种（饿汉，变种）
- 第五种（静态内部类）
- 第六种（枚举）：
- 第七种（双重校验锁）

可以看这篇文章：[单例模式的七种写法](https://www.iteye.com/blog/cantellow-838473)

### 47. 请列出 5 个运行时异常。

```
- NullPointerException 空指针异常
- ArithmeticException 出现异常的运算条件时，抛出此异常
- IndexOutOfBoundsException 数组索引越界异常
- ClassNotFoundException 找不到类异常
- IllegalArgumentException(非法参数异常)
```
### 48. 2个不相等的对象有可能具有相同的 hashcode吗？
有可能哈~

**hashCode 的常规协定：**

- 在 Java 应用程序执行期间，在对同一对象多次调用 hashCode 方法时，必须一致地返回相同的整数，前提是将对象进行 equals 比较时所用的信息没有被修改。从某一应用程序的一次执行到同一应用程序的另一次执行，该整数无需保持一致。
- 两个对象的equals()相等，那么对这两个对象中的每个对象调用 hashCode 方法都必须生成相同的整数结果。
- 两个对象的equals()不相等，那么对这两个对象中的任一对象上调用 hashCode 方法不要求一定生成不同的整数结果。但是，为不相等的对象生成不同整数结果可以提高哈希表的性能。

### 49. 访问修饰符public,private,protected,以及default的区别？

![](https://user-gold-cdn.xitu.io/2020/5/17/172222190f5c2bf7?w=1232&h=276&f=png&s=23829)

### 50. 谈谈final在java中的作用？
- final 修饰的类叫最终类，该类不能被继承。
- final 修饰的方法不能被重写。
- final 修饰的变量叫常量，常量必须初始化，初始化之后值就不能被修改。

### 51. java中的Math.round(-1.5) 等于多少呢？

![](https://user-gold-cdn.xitu.io/2020/5/17/172228be1252e870?w=847&h=394&f=png&s=44655)

JDK 中的 java.lang.Math 类:
- round() ：返回四舍五入，负 .5 小数返回较大整数，如 -1.5 返回 -1。
- ceil() ：返回小数所在两整数间的较大值，如 -1.5 返回 -1.0。
- floor() ：返回小数所在两整数间的较小值，如 -1.5 返回 -2.0。

### 52. String属于基础的数据类型吗？
String 不属于基础类型，基础类型有 8 种：byte、boolean、char、short、int、float、long、double，而 String 属于对象。

### 53. 如何将字符串反转呢？
- 使用 StringBuilder 或 StringBuffer 的 reverse 方法，本质都调用了它们的父类 AbstractStringBuilder 的 reverse 方法实现。（JDK1.8）
- 使用chatAt函数，倒过来输出；

![](https://user-gold-cdn.xitu.io/2020/5/17/1722295d9a7ea883?w=855&h=557&f=png&s=56301)

### 54. 描述动态代理的几种实现方式，它们分别有什么优缺点
- JDK动态代理
- CGLIB动态代理
- JDK原声动态代理时java原声支持的、不需要任何外部依赖、但是它只能基于接口进行代理
- CGLIB通过继承的方式进行代理、无论目标对象没有没实现接口都可以代理，但是无法处理final的情况

### 55. 在自己的代码中，如果创建一个java.lang.String类，这个类是否可以被类加载器加载？为什么。

不可以。因为JDK处于安全性的考虑，基于双亲委派模型，优先加载JDK的String类，如果java.lang.String已经加载，便不会再次被加载。

### 56. 谈谈你对java.lang.Object对象中hashCode和equals方法的理解。在什么场景下需要重新实现这两个方法。

> - 在我们的业务系统中判断对象时有时候需要的不是一种严格意义上的相等，而是一种业务上的对象相等。在这种情况下，原生的equals方法就不能满足我们的需求了，所以这个时候我们需要重写equals方法，来满足我们的业务系统上的需求。
> - 那么为什么在重写equals方法的时候需要重写hashCode方法呢？ 如果只重写了equals方法而没有重写hashCode方法的话，则会违反约定的第二条：相等的对象必须具有相等的散列码.所以hashCode和equals方法都需要重写

可以看网上这篇文章哈：[java为什么要重写hashCode和equals方法](https://blog.csdn.net/zknxx/article/details/53862572)

### 57. 在jdk1.5中，引入了泛型，泛型的存在是用来解决什么问题。

```
Java 泛型（generics）是 JDK 5 中引入的一个新特性，其本质是参数化类型，解决不确定具体对象类型的问题。
```

这个面试题，可以看我这篇文章哈~[Java程序员必备基础：泛型解析](https://juejin.im/post/5e1954c0f265da3e1e0568de)

### 58. 什么是序列化，怎么序列化，反序列呢？
- 序列化：把Java对象转换为字节序列的过程
- 反序列：把字节序列恢复为Java对象的过程
![](https://user-gold-cdn.xitu.io/2020/5/17/17222b156f8b230e?w=1280&h=487&f=png&s=189822)

可以看我这篇文章哈~ [Java程序员必备：序列化全方位解析](https://juejin.im/post/5e7f150d51882573b3309ceb)

### 59. java8的新特性。
- Lambda 表达式：Lambda允许把函数作为一个方法的参数
- Stream API ：新添加的Stream API（java.util.stream） 把真正的函数式编程风格引入到Java中
- 方法引用：方法引用提供了非常有用的语法，可以直接引用已有Java类或对象（实例）的方法或构造器。
- 默认方法：默认方法就是一个在接口里面有了一个实现的方法。
- Optional 类 ：Optional 类已经成为 Java 8 类库的一部分，用来解决空指针异常。
- Date Time API ：加强对日期与时间的处理。

### 60. 匿名内部类是什么？如何访问在其外面定义的变量呢？
匿名内部类就是没有名字的内部类，日常开发中使用的比较多。

```
public class Outer {

    private void test(final int i) {
        new Service() {
            public void method() {
                for (int j = 0; j < i; j++) {
                    System.out.println("匿名内部类" );
                }
            }
        }.method();
    }
 }
 //匿名内部类必须继承或实现一个已有的接口 
 interface Service{
    void method();
}

```
匿名内部类还有以下特点：
- 没有名字
- 匿名内部类必须继承一个抽象类或者实现一个接口。
- 匿名内部类不能定义任何静态成员和静态方法。
- 当所在的方法的形参需要被匿名内部类使用时，必须声明为 final。
- 匿名内部类不能是抽象的，它必须要实现继承的类或者实现的接口的所有抽象方法。
- 匿名内部类不能访问外部类方法中的局部变量，除非该变量被声明为final类型 


可以看我这篇文章哈~[Java程序员必备基础：内部类解析](https://juejin.im/post/5e105e1ef265da5d61695a45)


### 61. break和continue有什么区别？
- break可以使流程跳出switch语句体，也可以在循环结构终止本层循环体，从而提前结束本层循环。
- continue的作用是跳过本次循环体中余下尚未执行的语句，立即进行下一次的循环条件判定，可以理解为仅结束本次循环

### 62. String s = "Hello";s = s + " world!";这两行代码执行后，原始的 String 对象中的内容是否会改变？
没有。因为 String 被设计成不可变(immutable)类，所以它的所有对象都是不可变对象。

### 63. String s="a"+"b"+"c"+"d";创建了几个对象？
1个而已啦。
> Java 编译器对字符串常量直接相加的表达式进行优化，不等到运行期去进行加法运算，在编译时就去掉了加号，直接将其编译成一个这些常量相连的结果。所以 "a"+"b"+"c"+"d" 相当于直接定义一个 "abcd" 的字符串。

### 64. try-catch-finally-return执行顺序
try-catch-finally-return 执行描述:
- 如果不发生异常，不会执行catch部分。
- 不管有没有发生异常，finally都会执行到。
- 即使try和catch中有return时，finally仍然会执行
- finally是在return后面的表达式运算完后再执行的。（此时并没有返回运算后的值，而是先把要返回的值保存起来，若finally中无return，则不管finally中的代码怎么样，返回的值都不会改变，仍然是之前保存的值），该情况下函数返回值是在finally执行前确定的)
- finally部分就不要return了，要不然，就回不去try或者catch的return了。

看一个例子
```
 public static void main(String[] args) throws IOException {
        System.out.println("result：" + test());
    }

    private static int test() {
        int temp = 1;
        try {
            System.out.println("start execute try,temp is:"+temp);
            return ++temp;
        } catch (Exception e) {
            System.out.println("start execute catch temp is: "+temp);
            return ++temp;
        } finally {
            System.out.println("start execute finally,temp is:" + temp);
            ++temp;
        }
    }
```

运行结果：
```
start execute try,temp is:1
start execute finally,temp is:2
result:2
```
分析：
- 先执行try部分，输出日志，执行```++temp```表达式，temp变为2,这个值被保存起来。
- 因为没有发生异常，所以catch代码块跳过。
- 执行finally代码块，输出日志，执行```++temp```表达式.
- 返回try部分保存的值2.

### 65. Java 7新的 try-with-resources语句，平时有使用吗
try-with-resources，是Java7提供的一个新功能，它用于自动资源管理。
- 资源是指在程序用完了之后必须要关闭的对象。
- try-with-resources保证了每个声明了的资源在语句结束的时候会被关闭
- 什么样的对象才能当做资源使用呢？只要实现了java.lang.AutoCloseable接口或者java.io.Closeable接口的对象，都OK。
 
在```try-with-resources```出现之前
```
try{
    //open resources like File, Database connection, Sockets etc
} catch (FileNotFoundException e) {
    // Exception handling like FileNotFoundException, IOException etc
}finally{
    // close resources
}
```

Java7，```try-with-resources```出现之后，使用资源实现
```
try(// open resources here){
    // use resources
} catch (FileNotFoundException e) {
    // exception handling
}
// resources are closed as soon as try-catch block is executed.
```

Java7使用资源demo
```
public class Java7TryResourceTest {
    public static void main(String[] args) {
        try (BufferedReader br = new BufferedReader(new FileReader(
                "C:/jaywei.txt"))) {
            System.out.println(br.readLine());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

使用了```try-with-resources```的好处
- 代码更加优雅，行数更少。
- 资源自动管理，不用担心内存泄漏问题。

### 66. 简述一下面向对象的”六原则一法则”。
- 单一职责原则:一个类只做它该做的事情。
- 开闭原则：软件实体应当对扩展开放，对修改关闭。
- 依赖倒转原则：面向接口编程。
- 接口隔离原则：接口要小而专，绝不能大而全。
- 合成聚合复用原则：优先使用聚合或合成关系复用代码。
- 迪米特法则：迪米特法则又叫最少知识原则，一个对象应当对其他对象有尽可能少的了解。

### 67. switch是否能作用在byte 上，是否能作用在long 上，是否能作用在String上？
- switch可作用于char byte short int
- switch可作用于char byte short int对应的包装类
- switch不可作用于long double float boolean，以及他们的包装类

### 68. 数组有没有length()方法？String有没有length()方法？
- 数组没有length()方法，而是length；
- String有length()方法

### 69. 是否可以从一个静态（static）方法内部发出对非静态（non-static）方法的调用？
不可以。
- 非static方法是要与对象实例息息相关的，必须在创建一个对象后，才可以在该对象上进行非static方法调用，而static方法跟类相关的，不需要创建对象，可以由类直接调用。
- 当一个static方法被调用时，可能还没有创建任何实例对象，如果从一个static方法中发出对非static方法的调用，那个非static方法是关联到哪个对象上的呢？这个逻辑是不成立的
- 因此，一个static方法内部不可以发出对非static方法的调用。

### 70. String s = new String("jay");创建了几个字符串对象？
一个或两个
> - 第一次调用 new String("jay"); 时，会在堆内存中创建一个字符串对象，同时在字符串常量池中创建一个对象 "jay"
> - 第二次调用 new String("jay"); 时，只会在堆内存中创建一个字符串对象，指向之前在字符串常量池中创建的 "jay"

可以看老王这篇文章，很清晰~
[别再问我 new 字符串创建了几个对象了！我来证明给你看！](https://mp.weixin.qq.com/s?__biz=MzIwOTE2MzU4NA==&mid=2247484162&idx=1&sn=ff743ff5c975a373036749490042a868&chksm=9779472da00ece3b6186d8ce8e79cd1503311327ad4b38f569ccd29a9d15dec065ff201480d2&token=815554431&lang=zh_CN#rd)

### 71. this和super关键字的作用
this：
- 对象内部指代自身的引用
- 解决成员变量和局部变量同名问题
- 可以调用成员变量，不能调用局部变量
- 可以调用成员方法
- 在普通方法中可以省略 this
- 在静态方法当中不允许出现 this 关键字
 
super：
- 调用父类 的成员或者方法
- 调用父类的构造函数

### 72. 我们能将int强制转换为 byte类型的变量吗？如果该值大于byte 类型的范围，将会出现什么现象？
可以，我们可以做强制转换，但是在Java中，int是32位，byte是8位，如果强制做转化，int类型的高24位将会被丢弃。

```
public class Test {
    public static void main(String[] args)  {
        int a =129;
        byte b = (byte) a;
        System.out.println(b);
        int c =10;
        byte d = (byte) c;
        System.out.println(d);

    }
}
输出：
-127
10
```
### 73. float f=3.4;正确吗？
不正确，精度不准确,应该用强制类型转换
![](https://user-gold-cdn.xitu.io/2020/5/17/17222ee41ca4cc4c?w=466&h=125&f=png&s=8438)

### 74. 接口可否继承接口？抽象类是否可实现接口？抽象类是否可继承实体类？
都可以的
### 75. Reader和InputStream区别？
- InputStream是表示字节输入流的所有类的超类
- Reader是用于读取字符流的抽象类

### 76. 列举出JAVA中6个比较常用的包
- java.lang;
- java.util;
- java.io;
- java.sql;
- java.awt;
- java.net;

### 77.  JDK 7有哪些新特性
- Try-with-resource语句 
- NIO2 文件处理Files
- switch可以支持字符串判断条件
- 泛型推导
- 多异常统一处理

### 78. 同步和异步有什么区别？
- 同步，可以理解为在执行完一个函数或方法之后，一直等待系统返回值或消息，这时程序是出于阻塞的，只有接收到返回的值或消息后才往下执行其他的命令。
- 异步，执行完函数或方法后，不必阻塞性地等待返回值或消息，只需要向系统委托一个异步过程，那么当系统接收到返回值或消息时，系统会自动触发委托的异步过程，从而完成一个完整的流程。
- 同步，就是实时处理（如打电话）
- 异步，就是分时处理（如收发短信）

参考这篇文章~ [同步和异步的区别](https://blog.csdn.net/tennysonsky/article/details/45111623)

### 79. 实际开发中，Java一般使用什么数据类型来代表价格？
java中使用BigDecimal来表示价格是比较好的。

可以看这篇文章，写得非常好
[老大说：谁要再用double定义商品金额，就自己收拾东西走](https://mp.weixin.qq.com/s?__biz=MzU4ODI1MjA3NQ==&mid=2247485507&idx=1&sn=5c65f4a62ceab57a23bfe810a161035b&chksm=fddede87caa957918b6c131440c2c7aba5b21d72b296588776c6b85b6f8934bb4b6bfda97858&token=815554431&lang=zh_CN#rd)
### 80. 64 位 JVM 中，int 的长度是多数？
int数据类型占4个字节 32位，跟JVM位数没关系的

## 公众号
![](https://user-gold-cdn.xitu.io/2020/5/16/1721b50d00331393?w=900&h=500&f=png&s=389569)
- 欢迎关注我个人公众号，交个朋友，一起学习哈~
