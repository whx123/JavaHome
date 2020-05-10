equals与 ==的区别？
![](httpsuser-gold-cdn.xitu.io2020510171ff375ad2f6e94w=720&h=404&f=png&s=237039)

==
- 基本类型：比较的是值是否相同；（byte,short,char,int,long,float,double,boolean等基本类型）
- 引用类型：比较的是在内存中的存放地址是否相同；

equals
- 引用类型：默认情况下，比较的是内存地址值。
- 只不过 String 和 Integer 等重写了 equals 方法，把它变成了值比较。
- String类中被复写的equals()方法其实是比较两个字符串的内容。

请解释字符串比较之中“==”和equals()的区别？
- ==：比较的是两个字符串内存地址（堆内存）的数值是否相等，属于数值比较；
- equals()：比较的是两个字符串的内容，属于内容比较。