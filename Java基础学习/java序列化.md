### 前言
相信大家日常开发中，经常看到Java对象“implements Serializable”。那么，它到底有什么用呢？本文从以下几个角度来解析序列这一块知识点~
- 什么是Java序列化？
- 为什么需要序列化？
- 序列化用途
- Java序列化常用API
- 序列化的使用
- 序列化底层
- 日常开发序列化的注意点
- 序列化常见面试题


### 一、什么是Java序列化？

- 序列化：把Java对象转换为字节序列的过程
- 反序列：把字节序列恢复为Java对象的过程
![](https://user-gold-cdn.xitu.io/2020/4/16/1718072401688be6?w=1618&h=615&f=png&s=97837)

### 二、为什么需要序列化？
Java对象是运行在JVM的堆内存中的，如果JVM停止后，它的生命也就戛然而止。

![](https://user-gold-cdn.xitu.io/2020/4/18/17188de5182865ab?w=1322&h=802&f=png&s=94894)
如果想在JVM停止后，把这些对象保存到磁盘或者通过网络传输到另一远程机器，怎么办呢？磁盘这些硬件可不认识Java对象，它们只认识二进制这些机器语言，所以我们就要把这些对象转化为字节数组，这个过程就是序列化啦~

> 打个比喻，作为大城市漂泊的码农，搬家是常态。当我们搬书桌时，桌子太大了就通不过比较小的门，因此我们需要把它拆开再搬过去，这个拆桌子的过程就是序列化。 而我们把书桌复原回来（安装）的过程就是反序列化啦。

### 三、序列化用途
序列化使得对象可以脱离程序运行而独立存在，它主要有两种用途：

![](https://user-gold-cdn.xitu.io/2020/4/19/17190104b236e38c?w=886&h=444&f=png&s=33182)
- 1） 序列化机制可以让对象地保存到硬盘上，减轻内存压力的同时，也起了持久化的作用；

> 比如 Web服务器中的Session对象，当有 10+万用户并发访问的，就有可能出现10万个Session对象，内存可能消化不良，于是Web容器就会把一些seesion先序列化到硬盘中，等要用了，再把保存在硬盘中的对象还原到内存中。

- 2） 序列化机制让Java对象在网络传输不再是天方夜谭。
> 我们在使用Dubbo远程调用服务框架时，需要把传输的Java对象实现Serializable接口，即让Java对象序列化，因为这样才能让对象在网络上传输。


### 四、Java序列化常用API

```
java.io.ObjectOutputStream
java.io.ObjectInputStream
java.io.Serializable
java.io.Externalizable
```
#### Serializable 接口
Serializable接口是一个标记接口，没有方法或字段。一旦实现了此接口，就标志该类的对象就是可序列化的。

```
public interface Serializable {
}
```
#### Externalizable 接口
Externalizable继承了Serializable接口，还定义了两个抽象方法：writeExternal()和readExternal()，如果开发人员使用Externalizable来实现序列化和反序列化，需要重写writeExternal()和readExternal()方法
```
public interface Externalizable extends java.io.Serializable {
    void writeExternal(ObjectOutput out) throws IOException;
    void readExternal(ObjectInput in) throws IOException, ClassNotFoundException;
}
```

#### java.io.ObjectOutputStream类
表示对象输出流，它的writeObject(Object obj)方法可以对指定obj对象参数进行序列化，再把得到的字节序列写到一个目标输出流中。

#### java.io.ObjectInputStream
表示对象输入流，
它的readObject()方法，从输入流中读取到字节序列，反序列化成为一个对象，最后将其返回。


### 五、序列化的使用
序列化如何使用？来看一下，序列化的使用的几个关键点吧：
- 声明一个实体类，实现Serializable接口
- 使用ObjectOutputStream类的writeObject方法，实现序列化
- 使用ObjectInputStream类的readObject方法，实现反序列化

#### 声明一个Student类，实现Serializable
```
public class Student implements Serializable {

    private Integer age;
    private String name;

    public Integer getAge() {
        return age;
    }
    public void setAge(Integer age) {
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```
#### 使用ObjectOutputStream类的writeObject方法，对Student对象实现序列化
把Student对象设置值后，写入一个文件，即序列化，哈哈~
```
ObjectOutputStream objectOutputStream = new ObjectOutputStream( new FileOutputStream("D:\\text.out"));
Student student = new Student();
student.setAge(25);
student.setName("jayWei");
objectOutputStream.writeObject(student);

objectOutputStream.flush();
objectOutputStream.close();
```
看看序列化的可爱模样吧，test.out文件内容如下（使用UltraEdit打开）：
![](https://user-gold-cdn.xitu.io/2020/4/18/1718cb65a1d08785?w=896&h=311&f=png&s=59011)
#### 使用ObjectInputStream类的readObject方法，实现反序列化，重新生成student对象
再把test.out文件读取出来，反序列化为Student对象
```
ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream("D:\\text.out"));
Student student = (Student) objectInputStream.readObject();
System.out.println("name="+student.getName());
```
![](https://user-gold-cdn.xitu.io/2020/4/18/1718cb9ec7ca958f?w=1227&h=395&f=png&s=46746)
### 六、序列化底层

#### Serializable底层
Serializable接口，只是一个空的接口，没有方法或字段，为什么这么神奇，实现了它就可以让对象序列化了？
```
public interface Serializable {
}
```
为了验证Serializable的作用，把以上demo的Student对象，去掉实现Serializable接口，看序列化过程怎样吧~

![](https://user-gold-cdn.xitu.io/2020/4/18/1718cbbb01a02999?w=607&h=542&f=png&s=46516)

序列化过程中抛出异常啦，堆栈信息如下：
```
Exception in thread "main" java.io.NotSerializableException: com.example.demo.Student
	at java.io.ObjectOutputStream.writeObject0(ObjectOutputStream.java:1184)
	at java.io.ObjectOutputStream.writeObject(ObjectOutputStream.java:348)
	at com.example.demo.Test.main(Test.java:13)
```
顺着堆栈信息看一下，原来有重大发现，如下~
![](https://user-gold-cdn.xitu.io/2020/4/18/1718c980c904c2ee?w=802&h=636&f=png&s=69506)
**原来底层是这样：**
ObjectOutputStream 在序列化的时候，会判断被序列化的Object是哪一种类型，String？array？enum？还是 Serializable，如果都不是的话，抛出 NotSerializableException异常。所以呀，**Serializable真的只是一个标志，一个序列化标志**~

#### writeObject（Object）
序列化的方法就是writeObject，基于以上的demo，我们来分析一波它的核心方法调用链吧~（建议大家也去debug看一下这个方法，感兴趣的话）
![](https://user-gold-cdn.xitu.io/2020/4/18/1718d1fbad278f8f?w=668&h=1003&f=png&s=60938)

writeObject直接调用的就是writeObject0（）方法，

```
public final void writeObject(Object obj) throws IOException {
    ......
    writeObject0(obj, false);
    ......
}
```
writeObject0 主要实现是对象的不同类型，调用不同的方法写入序列化数据，这里面如果对象实现了Serializable接口，就调用writeOrdinaryObject()方法~
```
private void writeObject0(Object obj, boolean unshared)
        throws IOException
    {
    ......
   //String类型
    if (obj instanceof String) {
        writeString((String) obj, unshared);
   //数组类型
    } else if (cl.isArray()) {
        writeArray(obj, desc, unshared);
   //枚举类型
    } else if (obj instanceof Enum) {
        writeEnum((Enum<?>) obj, desc, unshared);
   //Serializable实现序列化接口
    } else if (obj instanceof Serializable) {
        writeOrdinaryObject(obj, desc, unshared);
    } else{
        //其他情况会抛异常~
        if (extendedDebugInfo) {
            throw new NotSerializableException(
                cl.getName() + "\n" + debugInfoStack.toString());
        } else {
            throw new NotSerializableException(cl.getName());
        }
    }
    ......
```

writeOrdinaryObject()会先调用writeClassDesc(desc)，写入该类的生成信息，然后调用writeSerialData方法,写入序列化数据
```
    private void writeOrdinaryObject(Object obj,
                                     ObjectStreamClass desc,
                                     boolean unshared)
        throws IOException
    {
            ......
            //调用ObjectStreamClass的写入方法
            writeClassDesc(desc, false);
            // 判断是否实现了Externalizable接口
            if (desc.isExternalizable() && !desc.isProxy()) {
                writeExternalData((Externalizable) obj);
            } else {
                //写入序列化数据
                writeSerialData(obj, desc);
            }
            .....
    }
```
writeSerialData（）实现的就是写入被序列化对象的字段数据

```
  private void writeSerialData(Object obj, ObjectStreamClass desc)
        throws IOException
    {
        for (int i = 0; i < slots.length; i++) {
            if (slotDesc.hasWriteObjectMethod()) {
                   //如果被序列化的对象自定义实现了writeObject()方法，则执行这个代码块
                    slotDesc.invokeWriteObject(obj, this);
            } else {
                // 调用默认的方法写入实例数据
                defaultWriteFields(obj, slotDesc);
            }
        }
    }
```
defaultWriteFields（）方法，获取类的基本数据类型数据，直接写入底层字节容器；获取类的obj类型数据，循环递归调用writeObject0()方法，写入数据~
```
   private void defaultWriteFields(Object obj, ObjectStreamClass desc)
        throws IOException
    {   
        // 获取类的基本数据类型数据，保存到primVals字节数组
        desc.getPrimFieldValues(obj, primVals);
        //primVals的基本类型数据写到底层字节容器
        bout.write(primVals, 0, primDataSize, false);

        // 获取对应类的所有字段对象
        ObjectStreamField[] fields = desc.getFields(false);
        Object[] objVals = new Object[desc.getNumObjFields()];
        int numPrimFields = fields.length - objVals.length;
        // 获取类的obj类型数据，保存到objVals字节数组
        desc.getObjFieldValues(obj, objVals);
        //对所有Object类型的字段,循环
        for (int i = 0; i < objVals.length; i++) {
            ......
              //递归调用writeObject0()方法，写入对应的数据
            writeObject0(objVals[i],
                             fields[numPrimFields + i].isUnshared());
            ......
        }
    }
```

### 七、日常开发序列化的一些注意点
- static静态变量和transient 修饰的字段是不会被序列化的
- serialVersionUID问题
- 如果某个序列化类的成员变量是对象类型，则该对象类型的类必须实现序列化
- 子类实现了序列化，父类没有实现序列化，父类中的字段丢失问题


#### static静态变量和transient 修饰的字段是不会被序列化的
static静态变量和transient 修饰的字段是不会被序列化的,我们来看例子分析一波~ Student类加了一个类变量gender和一个transient修饰的字段specialty
```
public class Student implements Serializable {

    private Integer age;
    private String name;

    public static String gender = "男";
    transient  String specialty = "计算机专业";

    public String getSpecialty() {
        return specialty;
    }

    public void setSpecialty(String specialty) {
        this.specialty = specialty;
    }

    @Override
    public String toString() {
        return "Student{" +"age=" + age + ", name='" + name + '\'' + ", gender='" + gender + '\'' + ", specialty='" + specialty + '\'' +
                '}';
    }
    ......
```
打印学生对象，序列化到文件，接着修改静态变量的值，再反序列化，输出反序列化后的对象~
![](https://user-gold-cdn.xitu.io/2020/4/19/1718e0bcd6cdcbfe?w=1240&h=527&f=png&s=83206)
运行结果：

```
序列化前Student{age=25, name='jayWei', gender='男', specialty='计算机专业'}
序列化后Student{age=25, name='jayWei', gender='女', specialty='null'}
```
对比结果可以发现：

- 1）序列化前的静态变量性别明明是‘男’，序列化后再在程序中修改，反序列化后却变成‘女’了，**what**？显然这个静态属性并没有进行序列化。其实，**静态（static）成员变量是属于类级别的，而序列化是针对对象的~所以不能序列化哦**。
- 2）经过序列化和反序列化过程后，specialty字段变量值由'计算机专业'变为空了，为什么呢？其实是因为transient关键字，**它可以阻止修饰的字段被序列化到文件中**，在被反序列化后，transient 字段的值被设为初始值，比如int型的值会被设置为 0，对象型初始值会被设置为null。

#### serialVersionUID问题
serialVersionUID 表面意思就是**序列化版本号ID**，其实每一个实现Serializable接口的类，都有一个表示序列化版本标识符的静态变量，或者默认等于1L，或者等于对象的哈希码。
```
private static final long serialVersionUID = -6384871967268653799L;
```
**serialVersionUID有什么用？**

JAVA序列化的机制是通过判断类的serialVersionUID来验证版本是否一致的。在进行反序列化时，JVM会把传来的字节流中的serialVersionUID和本地相应实体类的serialVersionUID进行比较，如果相同，反序列化成功，如果不相同，就抛出InvalidClassException异常。

接下来，我们来验证一下吧，修改一下Student类，再反序列化操作

![](https://user-gold-cdn.xitu.io/2020/4/19/1718f8eb4c01274f?w=593&h=315&f=png&s=29002)

```
Exception in thread "main" java.io.InvalidClassException: com.example.demo.Student;
local class incompatible: stream classdesc serialVersionUID = 3096644667492403394,
local class serialVersionUID = 4429793331949928814
	at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:687)
	at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1876)
	at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1745)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2033)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1567)
	at java.io.ObjectInputStream.readObject(ObjectInputStream.java:427)
	at com.example.demo.Test.main(Test.java:20)
```
从日志堆栈异常信息可以看到，文件流中的class和当前类路径中的class不同了，它们的serialVersionUID不相同，所以反序列化抛出InvalidClassException异常。那么，如果确实需要修改Student类，又想反序列化成功，怎么办呢？可以手动指定serialVersionUID的值，一般可以设置为1L或者，或者让我们的编辑器IDE生成

```
private static final long serialVersionUID = -6564022808907262054L;
```
实际上，阿里开发手册，强制要求序列化类新增属性时，不能修改serialVersionUID字段~
![](https://user-gold-cdn.xitu.io/2020/4/19/1718f78ab09a17cd?w=925&h=140&f=png&s=39827)

#### 如果某个序列化类的成员变量是对象类型，则该对象类型的类必须实现序列化
给Student类添加一个Teacher类型的成员变量，其中Teacher是没有实现序列化接口的

```
public class Student implements Serializable {
    
    private Integer age;
    private String name;
    private Teacher teacher;
    ...
}
//Teacher 没有实现
public class Teacher  {
......
}
```
序列化运行，就报NotSerializableException异常啦
```
Exception in thread "main" java.io.NotSerializableException: com.example.demo.Teacher
	at java.io.ObjectOutputStream.writeObject0(ObjectOutputStream.java:1184)
	at java.io.ObjectOutputStream.defaultWriteFields(ObjectOutputStream.java:1548)
	at java.io.ObjectOutputStream.writeSerialData(ObjectOutputStream.java:1509)
	at java.io.ObjectOutputStream.writeOrdinaryObject(ObjectOutputStream.java:1432)
	at java.io.ObjectOutputStream.writeObject0(ObjectOutputStream.java:1178)
	at java.io.ObjectOutputStream.writeObject(ObjectOutputStream.java:348)
	at com.example.demo.Test.main(Test.java:16)
```
其实这个可以在上小节的底层源码分析找到答案，一个对象序列化过程，会循环调用它的Object类型字段，递归调用序列化的，也就是说，序列化Student类的时候，会对Teacher类进行序列化，但是对Teacher没有实现序列化接口，因此抛出NotSerializableException异常。所以如果某个实例化类的成员变量是对象类型，则该对象类型的类必须实现序列化
![](https://user-gold-cdn.xitu.io/2020/4/19/1718fa928a6ff178?w=852&h=415&f=png&s=52155)

#### 子类实现了Serializable，父类没有实现Serializable接口的话，父类不会被序列化。
子类Student实现了Serializable接口，父类User没有实现Serializable接口
```
//父类实现了Serializable接口
public class Student  extends User implements Serializable {

    private Integer age;
    private String name;
}
//父类没有实现Serializable接口
public class User {
    String userId;
}

Student student = new Student();
student.setAge(25);
student.setName("jayWei");
student.setUserId("1");

ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream("D:\\text.out"));
objectOutputStream.writeObject(student);

objectOutputStream.flush();
objectOutputStream.close();

//反序列化结果
ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream("D:\\text.out"));
Student student1 = (Student) objectInputStream.readObject();
System.out.println(student1.getUserId());
//output
/** 
 * null
 */
```
从反序列化结果，可以发现，父类属性值丢失了。因此子类实现了Serializable接口，父类没有实现Serializable接口的话，父类不会被序列化。


### 八、序列化常见面试题
- 序列化的底层是怎么实现的？
- 序列化时，如何让某些成员不要序列化？
- 在 Java 中,Serializable 和 Externalizable 有什么区别
- serialVersionUID有什么用？
- 是否可以自定义序列化过程, 或者是否可以覆盖 Java 中的默认序列化过程？
- 在 Java 序列化期间,哪些变量未序列化？

#### 1.序列化的底层是怎么实现的？
本文第六小节可以回答这个问题，如回答Serializable关键字作用，序列化标志啦，源码中，它的作用啦~还有，可以回答writeObject几个核心方法，如直接写入基本类型，获取obj类型数据，循环递归写入，哈哈~

#### 2.序列化时，如何让某些成员不要序列化？
可以用transient关键字修饰，它可以阻止修饰的字段被序列化到文件中，在被反序列化后，transient 字段的值被设为初始值，比如int型的值会被设置为 0，对象型初始值会被设置为null。
#### 3.在 Java 中,Serializable 和 Externalizable 有什么区别
Externalizable继承了Serializable，给我们提供 writeExternal() 和 readExternal() 方法, 让我们可以控制 Java的序列化机制, 不依赖于Java的默认序列化。正确实现 Externalizable 接口可以显著提高应用程序的性能。

#### 4.serialVersionUID有什么用？
可以看回本文第七小节哈，JAVA序列化的机制是通过判断类的serialVersionUID来验证版本是否一致的。在进行反序列化时，JVM会把传来的字节流中的serialVersionUID和本地相应实体类的serialVersionUID进行比较，如果相同，反序列化成功，如果不相同，就抛出InvalidClassException异常。

#### 5.是否可以自定义序列化过程, 或者是否可以覆盖 Java 中的默认序列化过程？
可以的。我们都知道,对于序列化一个对象需调用 ObjectOutputStream.writeObject(saveThisObject), 并用 ObjectInputStream.readObject() 读取对象, 但 Java 虚拟机为你提供的还有一件事, 是定义这两个方法。如果在类中定义这两种方法, 则 JVM 将调用这两种方法, 而不是应用默认序列化机制。同时，可以声明这些方法为私有方法，以避免被继承、重写或重载。 
#### 6.在 Java 序列化期间,哪些变量未序列化？
static静态变量和transient 修饰的字段是不会被序列化的。静态（static）成员变量是属于类级别的，而序列化是针对对象的。transient关键字修字段饰，可以阻止该字段被序列化到文件中。

### 参考与感谢
- [Java基础学习总结——Java对象的序列化和反序列化](https://www.cnblogs.com/xdp-gacl/p/3777987.html)
- [10个艰难的Java面试题与答案](https://segmentfault.com/a/1190000019962661)

### 个人公众号


![](https://user-gold-cdn.xitu.io/2020/5/12/172066fcb54c1643?w=900&h=500&f=png&s=133410)
- 觉得写得好的小伙伴给个点赞+关注啦，谢谢~
- 如果有写得不正确的地方，麻烦指出，感激不尽。
- 同时非常期待小伙伴们能够关注我公众号，后面慢慢推出更好的干货~嘻嘻
- - github地址：https://github.com/whx123/JavaHome