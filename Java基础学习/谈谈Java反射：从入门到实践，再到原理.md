## 前言
反射是Java底层框架的灵魂技术，学习反射非常有必要，本文将从入门概念，到实践，再到原理讲解反射，希望对大家有帮助。

## 反射理解

### 官方解析
[Oracle 官方对反射](https://docs.oracle.com/javase/8/docs/technotes/guides/reflection/index.html)的解释是：
```
Reflection is commonly used by programs which require the ability to examine or
modify the runtime behavior of applications running in the Java virtual machine.
This is a relatively advanced feature and should be used only by developers who
have a strong grasp of the fundamentals of the language. With that caveat in
mind, reflection is a powerful technique and can enable applications to perform
operations which would otherwise be impossible.
```

Java 的**反射机制**是指在运行状态中，对于任意一个类都能够知道这个类所有的属性和方法； 并且对于任意一个对象，都能够调用它的任意一个方法；这种动态获取信息以及动态调用对象方法的功能成为Java语言的反射机制。

### 白话理解
#### 正射
万物有阴必有阳，有正必有反。既然有反射，就必有“正射”。

那么**正射**是什么呢？

我们在编写代码时，当需要使用到某一个类的时候，都会先了解这个类是做什么的。然后实例化这个类，接着用实例化好的对象进行操作，这就是正射。
```
Student student = new Student();
student.doHomework("数学");
```

#### 反射
反射就是，一开始并不知道我们要初始化的类对象是什么，自然也无法使用 new 关键字来创建对象了。
```
 Class clazz = Class.forName("reflection.Student");
 Method method = clazz.getMethod("doHomework", String.class);
 Constructor constructor = clazz.getConstructor();
 Object object = constructor.newInstance();
 method.invoke(object, "语文");
```

#### 正射与反射对比
以上两段代码，执行效果是一样的，如图

![](https://user-gold-cdn.xitu.io/2019/12/13/16efff4ccf899e66?w=1071&h=716&f=png&s=91450)

但是，其实现的过程还是有很大的差别的：

- 第一段代码在未运行前就已经知道了要运行的类是```Student```；
- 第二段代码则是到整个程序运行的时候，从字符串```reflection.Student```，才知道要操作的类是```Student```。


#### 结论

反射就是在运行时才知道要操作的类是什么，并且可以在运行时获取类的完整构造，并调用对应的方法。

## Class 对象理解
要理解Class对象，我们先来了解一下**RTTI**吧。
**RTTI（Run-Time Type Identification）运行时类型识别**，其作用是在运行时识别一个对象的类型和类的信息。

Java是如何让我们在运行时识别对象和类的信息的？主要有两种方式：
一种是传统的**RRTI**，它假定我们在编译期已知道了所有类型。
另一种是反射机制，它允许我们在运行时发现和使用类的信息。

**每个类都有一个Class对象**，每当编译一个新类就产生一个Class对象（更恰当地说，是被保存在一个同名的.class文件中）。比如创建一个Student类，那么，JVM就会创建一个Student对应Class类的Class对象，该Class对象保存了Student类相关的类型信息。


![](https://user-gold-cdn.xitu.io/2019/12/21/16f286b0e2fce054?w=1066&h=538&f=png&s=60629)

**Class类的对象作用**是运行时提供或获得某个对象的类型信息

## 反射的基本使用

### 获取 Class 类对象

获取反射中的Class对象有三种方法。

**第一种，使用 Class.forName 静态方法。**
```
Class class1 = Class.forName("reflection.TestReflection");
```

**第二种，使用类的.class 方法**
```
Class class2 = TestReflection.class;
```

**第三种，使用实例对象的 getClass() 方法。**

```
TestReflection testReflection = new TestReflection();
Class class3 = testReflection.getClass();
```

![](https://user-gold-cdn.xitu.io/2019/12/16/16f0c05f6b2a341a?w=1065&h=628&f=png&s=85470)


### 反射创造对象，获取方法，成员变量，构造器
本小节学习反射的基本API用法，如获取方法，成员变量等。
#### 反射创造对象
通过反射创建类对象主要有两种方式：
![](https://user-gold-cdn.xitu.io/2019/12/19/16f1b85fc34ce537?w=657&h=366&f=png&s=23704)

**实例代码:**
```
//方式一
Class class1 = Class.forName("reflection.Student");
Student student = (Student) class1.newInstance();
System.out.println(student);

//方式二
Constructor constructor = class1.getConstructor();
Student student1 = (Student) constructor.newInstance();
System.out.println(student1);
```
运行结果：

![](https://user-gold-cdn.xitu.io/2019/12/19/16f1ede509708fec?w=868&h=525&f=png&s=68487)

#### 反射获取类的构造器


![](https://user-gold-cdn.xitu.io/2019/12/18/16f1662225a43b9e?w=938&h=479&f=png&s=52644)

**看一个例子吧：**

```
Class class1 = Class.forName("reflection.Student");
Constructor[] constructors = class1.getDeclaredConstructors();
for (int i = 0; i < constructors.length; i++) {
    System.out.println(constructors[i]);
 }
```
![](https://user-gold-cdn.xitu.io/2019/12/18/16f165e1e8d36c90?w=1104&h=641&f=png&s=81960)

#### 反射获取类的成员变量

![](https://user-gold-cdn.xitu.io/2019/12/18/16f19b116e71c366?w=1018&h=528&f=png&s=52897)

**看demo：**

```
// student 一个私有属性age，一个公有属性email
public class Student {

    private Integer age;

    public String email;
}

public class TestReflection {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        Class class1 = Class.forName("reflection.Student");
        Field email = class1.getField("email");
        System.out.println(email);
        Field age = class1.getField("age");
        System.out.println(age);
    }
}

```
**运行结果：**

![](https://user-gold-cdn.xitu.io/2019/12/18/16f19bc4e032ace2?w=1385&h=663&f=png&s=94943)
即```getField(String name)``` 根据参数变量名，返回一个具体的具有public属性的成员变量，如果该变量**不是public属性**，则报异常。


#### 反射获取类的方法

![](https://user-gold-cdn.xitu.io/2019/12/19/16f19c250c645069?w=1022&h=463&f=png&s=43225)

**demo**

```
public class Student {

    private void testPrivateMethod() {
        
    }
    public void testPublicMethod() {
        
    }
}

public class TestReflection {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        Class class1 = Class.forName("reflection.Student");

        Method[] methods = class1.getMethods();
        for (int i = 0; i < methods.length; i++) {
            System.out.println(methods[i]);
        }
    }
}

```
**运行结果：**

![](https://user-gold-cdn.xitu.io/2019/12/19/16f19c70f7446654?w=1346&h=721&f=png&s=112136)


## 反射的实现原理
通过上一小节学习，我们已经知道反射的基本API用法了。接下来，跟着一个例子，学习反射方法的执行链路。

```
public class TestReflection {
    public static void main(String[] args) throws Exception {
        Class clazz = Class.forName("reflection.TestReflection");
        Method method = clazz.getMethod("target", String.class);
        method.invoke(null, "666");
    }

    public static void target(String str) {
        //打印堆栈信息
        new Exception("#" +str).printStackTrace();
        System.out.println("invoke target method");
    }
}
```

**堆栈信息反映出反射调用链路：**
```
java.lang.Exception: #666
invoke target method
	at reflection.TestReflection.target(TestReflection.java:17)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at reflection.TestReflection.main(TestReflection.java:11)
```
**invoke方法执行时序图**

![](https://user-gold-cdn.xitu.io/2019/12/21/16f27996f9e5ed0f?w=1794&h=1030&f=png&s=154261)

**我们跟着反射链路去看一下源码，先看Method的invoke方法：**

```
public Object invoke(Object obj, Object... args)
    throws IllegalAccessException, IllegalArgumentException,
       InvocationTargetException
{
    //校验权限
    if (!override) {
        if (!Reflection.quickCheckMemberAccess(clazz, modifiers)) {
            Class<?> caller = Reflection.getCallerClass();
            checkAccess(caller, clazz, obj, modifiers);
        }
    }
    MethodAccessor ma = methodAccessor;             // read volatile
    if (ma == null) {
        ma = acquireMethodAccessor(); //获取MethodAccessor
    }
    //返回MethodAccessor.invoke
    return ma.invoke(obj, args);
}

```

由上可知道，Method 的 invoke 方法，其实是返回接口MethodAccessor的invoke方法。MethodAccessor接口有三个实现类，到底调用的是哪个类的 invoke 方法呢？
![](https://user-gold-cdn.xitu.io/2019/12/21/16f2882973a9ed4b?w=1232&h=466&f=png&s=55007)

进入acquireMethodAccessor方法，可以看到MethodAccessor由ReflectionFactory 的 newMethodAccessor方法决定。

![](https://user-gold-cdn.xitu.io/2019/12/21/16f288699bf4fce4?w=963&h=536&f=png&s=56491)

再进ReflectionFactory的newMethodAccessor方法，我们可以看到返回的是DelegatingMethodAccessorImpl对象，也就是说调用的是它的invoke方法。

![](https://user-gold-cdn.xitu.io/2019/12/21/16f289806c3e4768?w=1975&h=416&f=png&s=63263)

再看DelegatingMethodAccessorImpl的invoke方法

![](https://user-gold-cdn.xitu.io/2019/12/21/16f26933b822b50e?w=1395&h=577&f=png&s=63941)

DelegatingMethodAccessorImpl的invoke方法返回的是MethodAccessorImpl的invoke方法，而MethodAccessorImpl的invoke方法，由它的子类NativeMethodAccessorImpl重写，这时候返回的是本地方法invoke0，如下

![](https://user-gold-cdn.xitu.io/2019/12/21/16f269db5ee96133?w=1925&h=762&f=png&s=113499)

因此，Method的invoke方法，是由本地方法invoke0决定的，再底层就是c++相关了，有兴趣的朋友可以继续往下研究。


## 反射的一些应用以及问题
### 反射应用
反射是Java框架的灵魂技术，很多框架都使用了反射技术，如spring，Mybatis，Hibernate等。

####  JDBC 的数据库的连接
在JDBC连接数据库中，一般包括**加载驱动，获得数据库连接**等步骤。而加载驱动，就是引入相关Jar包后，通过**Class.forName()** 即反射技术，加载数据库的驱动程序。

#### Spring 框架的使用
Spring 通过 XML 配置模式装载 Bean，也是反射的一个典型例子。

**装载过程：**

- 将程序内XML 配置文件加载入内存中
- Java类解析xml里面的内容，得到相关字节码信息
- 使用反射机制，得到Class实例
- 动态配置实例的属性，使用

**这样做当然是有好处的：**

不用每次都去new实例了，并且可以修改配置文件，比较灵活。


### 反射存在的问题
#### 性能问题
java反射的性能并不好，原因主要是编译器没法对反射相关的代码做优化。
有兴趣的朋友，可以看一下这个文章[java-reflection-why-is-it-so-slow](https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow)

#### 安全问题
我们知道单例模式的设计过程中，会强调**将构造器设计为私有**，因为这样可以防止从外部构造对象。但是反射可以获取类中的域、方法、构造器，**修改访问权限**。所以这样并不一定是安全的。

看个例子吧，通过反射使用私有构造器实例化。

```
public class Student {
    private String name;
    private Student(String name) {
        System.out.println("我是私有构造器，我被实例化了");
        this.name = name;
    }
    public void doHomework(String subject) {
        System.out.println("我的名字是" + name);
        System.out.println("我在做"+subject+"作业");
    }
}
public class TestReflection {
    public static void main(String[] args) throws Exception {
        Class clazz = Class.forName("reflection.Student");
        // 获取私有构造方法对象
        Constructor constructor = clazz.getDeclaredConstructor(String.class);
        // true指示反射的对象在使用时应该取消Java语言访问检查。
        constructor.setAccessible(true);
        Student student = (Student) constructor.newInstance("jay@huaxiao");
        student.doHomework("数学");
    }
}
```
**运行结果：**

![](https://user-gold-cdn.xitu.io/2019/12/21/16f283232305ee69?w=1165&h=605&f=png&s=104011)
显然，反射不管你是不是私有，一样可以调用。
所以，使用反射通常需要程序的运行没有**安全限制**。如果一个程序对安全性有强制要求，最好不要使用反射啦。

## 参考与感谢
- [反射的实现原理](https://www.jianshu.com/p/c8de67db8adb)
- [通过反射获取私有构造方法并使用](https://blog.csdn.net/wangyanming123/article/details/51355251)
- [大白话说Java反射：入门、使用、原理](https://www.cnblogs.com/chanshuyi/p/head_first_of_reflection.html)
- [设计模式之单例模式六（防反射攻击）](https://blog.csdn.net/Wenlong_L/article/details/82811996)
- [Reflection：Java反射机制的应用场景](https://segmentfault.com/a/1190000010162647)
- [深入理解Java类型信息(Class对象)与反射机制](https://blog.csdn.net/javazejian/article/details/70768369)
- 《Java编程思想》

## 个人公众号

![](https://user-gold-cdn.xitu.io/2019/7/28/16c381c89b127bbb?w=344&h=344&f=jpeg&s=8943)

- 如果你是个爱学习的好孩子，可以关注我公众号，一起学习讨论。
- 如果你觉得本文有哪些不正确的地方，可以评论，也可以关注我公众号，私聊我，大家一起学习进步哈。




