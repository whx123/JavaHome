### Java 基础
1. equals与 == 的区别
2. final, finally, finalize 的区别
3. 重载和重写的区别
4. 两个对象的 hashCode()相同，则 equals()是否也一定为 true？
5. 抽象类和接口有什么区别
6. BIO、NIO、AIO 有什么区别？
7. String，Stringbuffer，StringBuilder的区别
8. JAVA中的几种基本数据类型是什么，各自占用多少字节
9. Java 中，Comparator 与Comparable 有什么不同？
10. String类能被继承吗，为什么。
11. 说说Java中多态的实现原理
12. Java泛型和类型擦除
13. int 和 Integer 有什么区别
14. 说说反射的用途及实现原理，反射创建类实例的三种方式是什么
15. 面向对象的特征
16. &和&&的区别
17. Java 中 IO 流分为几种?
18. 讲讲类的实例化顺序，比如父类静态数据，构造函数，子类静态数据，构造函数。
19. Java创建对象有几种方式
20. Java 中 sleep 方法和 wait 方法的区别，还有yield()呢？
21. 守护线程是什么？用什么方法实现守护线程
22. notify()和 notifyAll()有什么区别？
23. Java中的四种引用及其应用场景是什么？
24. Java中的异常层次结构
25. 静态内部类与非静态内部类的区别
26. String s 与new String的区别
27. 反射中，Class.forName和ClassLoader区别
28. JDK动态代理与cglib实现的区别
29. error和exception的区别，CheckedException，RuntimeException的区别。
30. 深拷贝和浅拷贝区别
31. JDK 和 JRE 有什么区别？
32. String 类的常用方法都有那些
33. 说说自定义注解的场景及实现
34. 说一下你熟悉的设计模式？
35. 简单工厂和抽象工厂有什么区别？
36. 什么是值传递和引用传递？
37. 是否可以在static环境中访问非static变量
38. Java支持多继承么,为什么？
39. 用最有效率的方法计算2乘以8 ？
40. 构造器是否可被重写
41. char 型变量中能不能存贮一个中文汉字，为什么？
42. 如何实现对象克隆？
43. object中定义了哪些方法？
44. hashCode的作用是什么？
45. for-each与常规for循环的效率对比
46. 写出几种单例模式实现。
47. 请列出 5 个运行时异常。
48. 有没有可能 2 个不相等的对象有相同的 hashcode
49. 访问修饰符public,private,protected,以及不写（默认）时的区别？
50. final 在 java 中有什么作用？
51. java 中的 Math.round(-1.5) 等于多少？
52. String 属于基础的数据类型吗？
53. 如何将字符串反转？
54. 描述动态代理的几种实现方式，分别说出相应的优缺点
55. 在自己的代码中，如果创建一个java.lang.String类，这个类是否可以被类加载器加载？为什么。
56. 谈谈你对java.lang.Object对象中hashCode和equals方法的理解。在什么场景下需要重新实现这两个方法。
57. 在jdk1.5中，引入了泛型，泛型的存在是用来解决什么问题。
58. 什么是序列化，怎么序列化，反序列呢？
59. java8的新特性。
60. 匿名内部类是什么？如何访问在其外面定义的变量？
61. break和continue的区别？
62. String s = "Hello";s = s + " world!";这两行代码执行后，原始的 String 对象中的内容变了没有？
63. 怎样将GB2312编码的字符串转换为ISO-8859-1编码的字符串？
64. try-catch-finally-return执行顺序
65. Java 7 新的 try-with-resources 语句
66. 简述一下面向对象的”六原则一法则”。
67. switch 是否能作用在byte 上，是否能作用在long 上，是否能作用在String上？
68. 数组有没有length()方法？String有没有length()方法？
69. 是否可以从一个静态（static）方法内部发出对非静态（non-static）方法的调用？
70. String s = new String("jay");创建了几个字符串对象？
71. 匿名内部类是否可以继承其它类？是否可以实现接口？
72. Java语言如何进行异常处理，关键字：throws、throw、try、catch、finally分别如何使用？
73. float f=3.4;是否正确？
74. String s = “123”;这个语句有几个对象产生？
75. Reader和InputStream区别？
76. 列举出JAVA中6个比较常用的包
77. Java 中的 Math. round(-1. 5) 等于多少？
78. 同步和异步有什么区别？
79. Java 中应该使用什么数据类型来代表价格？
80. 64 位 JVM 中，int 的长度是多数？
81. 进程与线程的区别？
82. 字节流与字符流的区别
83. Java事件机制包括哪三个部分？分别介绍下。
84. 为什么等待和通知是在 Object 类而不是 Thread 中声明的？
85. 每个对象都可上锁，这是在 Object 类而不是 Thread 类中声明 wait 和 notify 的另一个原因。
86. 为什么 char 数组比 Java 中的 String 更适合存储密码？
87. 如何使用双重检查锁定在 Java 中创建线程安全的单例？
88. 如果你的Serializable类包含一个不可序列化的成员，会发生什么？你是如何解决的？
89. 什么是 serialVersionUID ？如果你不定义这个, 会发生什么？
90. 为什么Java中 wait 方法需要在 synchronized 的方法中调用？
91. 常见的序列化协议有哪些
92. @transactional注解在什么情况下会失效，为什么。
93. notify和notifyall的区别。
94. 数组在内存中如何分配；
95. 什么是 Busy spin？我们为什么要使用它？
96. Java 中怎么获取一份线程 dump 文件？
97. 父类的静态方法能否被子类重写
98. 什么是不可变对象
99. 如何正确的退出多层嵌套循环？
100. SimpleDateFormat是线程安全的吗?你一般怎么格式化
101. 抽象类必须要有抽象方法吗？
102. 怎么实现动态代理？有哪些应用
103. 什么是内部类？内部类的作用
104. 泛型中extends和super的区别
105. 成员内部类、静态内部类、局部内部类和匿名内部类的理解，以及项目中的应用
106. utf-8编码中的中文占几个字节；int型几个字节？
107. 说说你对Java注解的理解