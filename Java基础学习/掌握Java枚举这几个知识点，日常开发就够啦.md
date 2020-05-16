### 前言
春节来临之际，祝大家新年快乐。整理了Java枚举的相关知识，算是比较基础的，希望大家一起学习进步。
![](https://user-gold-cdn.xitu.io/2020/1/23/16fd1949a15e2788?w=1223&h=508&f=png&s=108187)


> [本章节所有代码demo已上传github](https://github.com/whx123/EnumTest) 

### 一、枚举类型是什么？
JDK5引入了一种新特性，关键字enum可以将一组具名的值的有限集合创建为一种新的类型，而这些具名的值可以作为常规的程序组件使用，这就是枚举类型。

一个枚举的简单例子

```
enum SeasonEnum {
    SPRING,SUMMER,FALL,WINTER;
}
```
### 二、 枚举类的常用方法
Enum常用方法有以下几种:
- name();  返回enum实例声明时的名字。
- ordinal();  返回一个int值，表示enum实例在声明的次序。
- equals();  返回布尔值，enum实例判断相等
- compareTo() 比较enum实例与指定对象的顺序
- values();  返回enum实例的数组
- valueOf(String name)  由名称获取枚举类中定义的常量 

直接看例子吧：
```
enum Shrubbery {
    GROUND,CRAWLING, HANGING
}
public class EnumClassTest {
    public static void main(String[] args) {
        //values 返回enum实例的数组
        for (Shrubbery temp : Shrubbery.values()) {
            // name 返回实例enum声明的名字
            System.out.println(temp.name() + " ordinal is " + temp.ordinal() + " ,equal result is " +
                    Shrubbery.CRAWLING.equals(temp) + ",compare result is " + Shrubbery.CRAWLING.compareTo(temp));
        }
        //由名称获取枚举类中定义的常量值
        System.out.println(Shrubbery.valueOf("CRAWLING"));
    }
}

```
运行结果：

```
GROUND ordinal is 0 ,equal result is false,compare result is 1
CRAWLING ordinal is 1 ,equal result is true,compare result is 0
HANGING ordinal is 2 ,equal result is false,compare result is -1
CRAWLING
```


### 三、枚举类的真面目
枚举类型到底是什么类呢？我们新建一个简单枚举例子，看看它的庐山真面目。如下：
```
public enum Shrubbery {
    GROUND,CRAWLING, HANGING
}
```
使用javac编译上面的枚举类，可得Shrubbery.class文件。

```
javac Shrubbery.java
```
再用javap命令，反编译得到字节码文件。如：执行```javap Shrubbery.class```可到以下字节码文件。
```
Compiled from "Shrubbery.java"
public final class enumtest.Shrubbery extends java.lang.Enum<enumtest.Shrubbery> {
  public static final enumtest.Shrubbery GROUND;
  public static final enumtest.Shrubbery CRAWLING;
  public static final enumtest.Shrubbery HANGING;
  public static enumtest.Shrubbery[] values();
  public static enumtest.Shrubbery valueOf(java.lang.String);
  static {};
}
```
从字节码文件可以发现：
- Shrubbery枚举变成了一个final修饰的类，也就是说，它不能被继承啦。
- Shrubbery是java.lang.Enum的子类。
- Shrubbery定义的枚举值都是public static final修饰的，即都是静态常量。

为了看得更仔细，javap反编译加多个参数-c，执行如下命令：

```
javap -c Shrubbery.class
```
静态代码块的字节码文件如下：
```
  static {};
    Code:
       0: new           #4                  // class enumtest/Shrubbery
       3: dup
       4: ldc           #7                  // String GROUND
       6: iconst_0
       7: invokespecial #8                  // Method "<init>":(Ljava/lang/String;I)V
      10: putstatic     #9                  // Field GROUND:Lenumtest/Shrubbery;
      13: new           #4                  // class enumtest/Shrubbery
      16: dup
      17: ldc           #10                 // String CRAWLING
      19: iconst_1
      20: invokespecial #8                  // Method "<init>":(Ljava/lang/String;I)V
      23: putstatic     #11                 // Field CRAWLING:Lenumtest/Shrubbery;
      26: new           #4                  // class enumtest/Shrubbery
      29: dup
      30: ldc           #12                 // String HANGING
      32: iconst_2
      33: invokespecial #8                  // Method "<init>":(Ljava/lang/String;I)V
      36: putstatic     #13                 // Field HANGING:Lenumtest/Shrubbery;
      39: iconst_3
      40: anewarray     #4                  // class enumtest/Shrubbery
      43: dup
      44: iconst_0
      45: getstatic     #9                  // Field GROUND:Lenumtest/Shrubbery;
      48: aastore
      49: dup
      50: iconst_1
      51: getstatic     #11                 // Field CRAWLING:Lenumtest/Shrubbery;
      54: aastore
      55: dup
      56: iconst_2
      57: getstatic     #13                 // Field HANGING:Lenumtest/Shrubbery;
      60: aastore
      61: putstatic     #1                  // Field $VALUES:[Lenumtest/Shrubbery;
      64: return
}
```
- 0-39行实例化了Shrubbery枚举类的GROUND,CRAWLING, HANGING；
- 40-64为创建Shrubbery[]数组$VALUES,并将上面的三个实例化对象放入数组的操作。
- 因此，枚举类方法values()返回enum枚举实例的数组是不是豁然开朗啦。

### 四、枚举类的优点
枚举类有什么优点呢？就是我们为什么要选择使用枚举类呢？因为它可以增强**代码的可读性，可维护性**，同时，它也具有**安全**性。

#### 枚举类可以增强可读性、可维护性
假设现在有这样的业务场景：订单完成后，通知买家评论。很容易有以下代码：
```
//订单已完成
if(3==orderStatus){
//do something    
}
```
很显然，这段代码出现了魔法数，如果你没写注释，谁知道3表示订单什么状态呢，不仅阅读起来比较困难，维护起来也很蛋疼？如果使用**枚举类**呢，如下：
```
public enum OrderStatusEnum {
    UNPAID(0, "未付款"), PAID(1, "已付款"), SEND(2, "已发货"), FINISH(3, "已完成"),;

    private int index;

    private String desc;

    public int getIndex() {
        return index;
    }

    public String getDesc() {
        return desc;
    }

    OrderStatusEnum(int index, String desc) {
        this.index = index;
        this.desc = desc;
    }
}

 //订单已完成
 if(OrderStatusEnum.FINISH.getIndex()==orderStatus){
  //do something
 }
```
可见，枚举类让这段代码可读性更强，也比较好维护，后面加个新的订单状态，直接添加多一种枚举状态就可以了。有些朋友认为，```public static final int```这种静态常量也可以实现该功能呀，如下：

```
public class OrderStatus {
    //未付款
    public static final int UNPAID = 0;
    public static final int PAID = 1;
    public static final int SENDED = 2;
    public static final int FINISH = 3;
    
}

 //订单已完成
 if(OrderStatus.FINISH==orderStatus){
     //do something
 }
```
当然，静态常量这种方式实现，可读性是没有任何问题的，日常工作中代码这样写也无可厚非。但是，定义int值相同的变量，容易混淆，如你定义```PAID```和```SENDED```状态都是2，编译器是不会报错的。

因此，枚举类第一个优点就是**可读性，可维护性都不错**，所以推荐。


#### 枚举类安全性
除了可读性、可维护性外，枚举类还有个巨大的优点，就是安全性。

从上一节枚举类字节码分析，我们知道：
- 一个枚举类是被final关键字修饰的，不能被继承。
- 并且它的变量都是public static final修饰的，都是静态变量。

当一个Java类第一次被真正使用到的时候静态资源被初始化、Java类的加载和初始化过程都是线程安全的。

### 五、枚举的常见用法
#### enum组织常量
在JDK5之前，常量定义都是这样，先定义一个类或者接口，属性类型都是public static final...，有了枚举之后，可以把常量组织到枚举类了，如下：

```
enum SeasonEnum {
    SPRING,SUMMER,FALL,WINTER,;
}
```
#### enum与switch 环环相扣
一般来说，switch-case中只能使用整数值，但是枚举实例天生就具备整数值的次序，因此，在switch语句中是可以使用enum的，如下：
```
enum OrderStatusEnum {
   UNPAID, PAID, SEND, FINISH
}
public class OrderStatusTest {
    public static void main(String[] args) {
        changeByOrderStatus(OrderStatusEnum.FINISH);
    }

    static void changeByOrderStatus(OrderStatusEnum orderStatusEnum) {
        switch (orderStatusEnum) {
            case UNPAID:
                System.out.println("老板，你下单了，赶紧付钱吧");
                break;
            case PAID:
                System.out.println("我已经付钱啦");
                break;
            case SENDED:
                System.out.println("已发货");
                break;
            case FINISH:
                System.out.println("订单完成啦");
                break;
        }
    }
}

```
在日常开发中，enum与switch一起使用，会让你的代码可读性更好哦。

#### 向枚举中添加新的方法
可以向枚举类添加新方法的，如get方法，普通方法等，以下是日常工作最常用的一种枚举写法：
```
public enum OrderStatusEnum {
    UNPAID(0, "未付款"), PAID(1, "已付款"), SENDED(2, "已发货"), FINISH(3, "已完成"),;

    //成员变量
    private int index;
    private String desc;

    //get方法
    public int getIndex() {
        return index;
    }

    public String getDesc() {
        return desc;
    }

    //构造器方法
     OrderStatusEnum(int index, String desc) {
        this.index = index;
        this.desc = desc;
    }

    //普通方法
    public static OrderStatusEnum of(int index){
        for (OrderStatusEnum temp : values()) {
            if (temp.getIndex() == index) {
                return temp;
            }
        }
        return null;
    }
}

```

#### 枚举实现接口
所有枚举类都继承于java.lang.Enum,所以枚举不能再继承其他类了。但是枚举可以实现接口呀，这给枚举增添了不少色彩。如下：

```
public interface ISeasonBehaviour {

    void showSeasonBeauty();

    String getSeasonName();
}

public enum SeasonEnum implements ISeasonBehaviour {
    SPRING(1,"春天"),SUMMER(2,"夏天"),FALL(3,"秋天"),WINTER(4,"冬天"),
    ;

    private int index;
    private String name;

    SeasonEnum(int index, String name) {
        this.index = index;
        this.name = name;
    }

    public int getIndex() {
        return index;
    }
    public String getName() {
        return name;
    }

    //接口方法
    @Override
    public void showSeasonBeauty() {
        System.out.println("welcome to " + this.name);
    }

    //接口方法
    @Override
    public String getSeasonName() {
        return this.name;
    }
}
```

#### 使用接口组织枚举
```
public interface Food {
    enum Coffee implements Food{  
        BLACK_COFFEE,DECAF_COFFEE,LATTE,CAPPUCCINO  
    }  
    enum Dessert implements Food{  
        FRUIT, CAKE, GELATO  
    }  
}
```

### 六、枚举类比较是用==还是equals？
先看一个例子，如下：
```
public class EnumTest {
    public static void main(String[] args) {

        Shrubbery s1 = Shrubbery.CRAWLING;
        Shrubbery s2 = Shrubbery.GROUND;
        Shrubbery s3 = Shrubbery.CRAWLING;

        System.out.println("s1==s2,result: " + (s1 == s2));
        System.out.println("s1==s3,result: " + (s1 == s3));
        System.out.println("Shrubbery.CRAWLING.equals(s1)，result: "+Shrubbery.CRAWLING.equals(s1));
        System.out.println("Shrubbery.CRAWLING.equals(s2),result: "+Shrubbery.CRAWLING.equals(s2));

    }
}
```
运行结果：

```
s1==s2,result: false
s1==s3,result: true
Shrubbery.CRAWLING.equals(s1)，result: true
Shrubbery.CRAWLING.equals(s2),result: false
```

可以发现不管用==还是equals，都是可以的。其实枚举的equals方法，就是用==比较的，如下：

```
public final boolean equals(Object other) {
    return this==other;
}
```

### 七、枚举实现的单例
effective java提过，**最佳的单例实现模式就是枚举模式**。单例模式的实现有好几种方式，为什么是枚举实现的方式最佳呢？

因为枚举实现的单例有以下优点：
- 枚举单例写法简单
- 枚举可解决线程安全问题
- 枚举可解决反序列化会破坏单例的问题

一个枚举单例demo如下：
```
public class SingletonEnumTest {
   public enum SingletonEnum {
        INSTANCE,;
        private String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }

    public static void main(String[] args) {
        SingletonEnum.INSTANCE.setName("jay@huaxiao");
        System.out.println(SingletonEnum.INSTANCE.getName());
    }
}
```

有关于枚举实现单例，想深入了解的朋友可以看Hollis大神这篇文章，写得真心好！[为什么我墙裂建议大家使用枚举来实现单例。](https://juejin.im/post/5b285d236fb9a00e9b39fdd2#heading-0)


### 八、EnumSet 和EnumMap

#### EnumSet

先来看看EnumSet的继承体系图

![](https://user-gold-cdn.xitu.io/2020/1/23/16fcfec7f74422dc?w=798&h=495&f=png&s=38785)

显然，EnumSet也实现了set接口，相比于HashSet，它有以下优点：
- 消耗较少的内存
- 效率更高，因为是位向量实现的。
- 可以预测的遍历顺序（enum常量的声明顺序）
- 拒绝加null

**EnumSet就是set的高性能实现，它的要求就是存放必须是同一枚举类型。** EnumSet的常用方法：
- allof()  创建一个包含指定枚举类里所有枚举值的EnumSet集合
- range()  获取某个范围的枚举实例
- of()  创建一个包括参数中所有枚举元素的EnumSet集合
- complementOf（） 初始枚举集合包括指定枚举集合的补集

看实例，最实际：
```
public class EnumTest {
    public static void main(String[] args) {
        // Creating a set
        EnumSet<SeasonEnum> set1, set2, set3, set4;

        // Adding elements
        set1 = EnumSet.of(SeasonEnum.SPRING,  SeasonEnum.FALL, SeasonEnum.WINTER);
        set2 = EnumSet.complementOf(set1);
        set3 = EnumSet.allOf(SeasonEnum.class);
        set4 = EnumSet.range(SeasonEnum.SUMMER,SeasonEnum.WINTER);
        System.out.println("Set 1: " + set1);
        System.out.println("Set 2: " + set2);
        System.out.println("Set 3: " + set3);
        System.out.println("Set 4: " + set4);
    }
}
```
输出结果：

```
Set 1: [SPRING, FALL, WINTER]
Set 2: [SUMMER]
Set 3: [SPRING, SUMMER, FALL, WINTER]
Set 4: [SUMMER, FALL, WINTER]
```

#### EnumMap
EnumMap的继承体系图如下：

![](https://user-gold-cdn.xitu.io/2020/1/23/16fd0815ebfd3f03?w=689&h=332&f=png&s=23426)

EnumMap也实现了Map接口，相对于HashMap，它也有这些优点：
- 消耗较少的内存
- 效率更高
- 可以预测的遍历顺序
- 拒绝null
 
**EnumMap就是map的高性能实现。** 它的常用方法跟HashMap是一致的，唯一约束是枚举相关。

看实例，最实际：
```
public class EnumTest {
    public static void main(String[] args) {
        Map<SeasonEnum, String> map = new EnumMap<>(SeasonEnum.class);
        map.put(SeasonEnum.SPRING, "春天");
        map.put(SeasonEnum.SUMMER, "夏天");
        map.put(SeasonEnum.FALL, "秋天");
        map.put(SeasonEnum.WINTER, "冬天");
        System.out.println(map);
        System.out.println(map.get(SeasonEnum.SPRING));
    }
}
```
运行结果

```
{SPRING=春天, SUMMER=夏天, FALL=秋天, WINTER=冬天}
春天
```

### 九、待更新
有关于枚举关键知识点，亲爱的朋友，你有没有要补充的呢？

### 参考与感谢
- [关于Java中枚举Enum的深入剖析](https://droidyue.com/blog/2016/11/29/dive-into-enum/)
- [深度分析Java的枚举类型—-枚举的线程安全性及序列化问题](http://www.hollischuang.com/archives/197)
-  [为什么我墙裂建议大家使用枚举来实现单例。](https://juejin.im/post/5b285d236fb9a00e9b39fdd2#heading-3)
-  [深入理解Java枚举类型(enum)](https://blog.csdn.net/javazejian/article/details/71333103#enumset%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86%E5%89%96%E6%9E%90)
-  [深入理解 Java 枚举](https://juejin.im/post/5c90efacf265da60ce379e17#heading-16)
-  [EnumSet in Java](https://www.geeksforgeeks.org/enumset-class-java/)
-  [Java 枚举(enum) 详解7种常见的用法](https://blog.csdn.net/qq_27093465/article/details/52180865)
- 《Java编程思想》

### 个人公众号

![](https://user-gold-cdn.xitu.io/2019/7/28/16c381c89b127bbb?w=344&h=344&f=jpeg&s=8943)

- 如果你是个爱学习的好孩子，可以关注我公众号，一起学习讨论。
- 如果你觉得本文有哪些不正确的地方，可以评论，也可以关注我公众号，私聊我，大家一起学习进步哈。


