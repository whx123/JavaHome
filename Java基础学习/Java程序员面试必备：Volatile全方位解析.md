### 前言
volatile是Java程序员必备的基础，也是面试官非常喜欢问的一个话题，本文跟大家一起开启volatile学习之旅，如果有不正确的地方，也麻烦大家指出哈，一起相互学习~

- 1.volatile的用法
- 2.volatile变量的作用
- 3.现代计算机的内存模型（计算机模型，总线，MESI协议，嗅探技术）
- 4.Java内存模型（JMM）
- 5.并发编程的3个特性（原子性、可见性、有序性、happen-before、as-if-serial、指令重排）
- 6.volatile的底层原理（如何保证可见性，如何保证指令重排，内存屏障）
- 7.volatile的典型场景（状态标志，DCL单例模式）
- 8.volatile常见面试题&&答案解析
- 公众号：捡田螺的小男孩

**github 地址**
> https://github.com/whx123/JavaHome

### 1.volatile的用法
volatile关键字是Java虚拟机提供的的**最轻量级的同步机制**，它作为一个修饰符出现，用来**修饰变量**，但是这里不包括局部变量哦。我们来看个demo吧，代码如下:
```
/**
 *  @Author 捡田螺的小男孩
 *  @Date 2020/08/02
 *  @Desc volatile的可见性探索
 */
public class VolatileTest  {

    public static void main(String[] args) throws InterruptedException {
        Task task = new Task();

        Thread t1 = new Thread(task, "线程t1");
        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(1000);
                    System.out.println("开始通知线程停止");
                    task.stop = true; //修改stop变量值。
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }
        }, "线程t2");
        t1.start();  //开启线程t1
        t2.start();  //开启线程t2
        Thread.sleep(1000);
    }
}

class Task implements Runnable {
    boolean stop = false;
    int i = 0;

    @Override
    public void run() {
        long s = System.currentTimeMillis();
        while (!stop) {
            i++;
        }
        System.out.println("线程退出" + (System.currentTimeMillis() - s));
    }
}
```
**运行结果：**
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/057e2eec568a40fe9f47e05f3b47bdb7~tplv-k3u1fbpfcp-zoom-1.image)
可以发现线程t2，虽然把stop设置为true了，但是线程t1对t2的**stop变量视而不可见**，因此，它一直在死循环running中。如果给变量stop加上volatile修饰，线程t1是可以停下来的,运行结果如下：
```
volatile boolean stop = false;
```
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/81326a66e9cc4350af71b0e9ff4dc304~tplv-k3u1fbpfcp-zoom-1.image)


### 2. vlatile修饰变量的作用

从以上例子，我们可以发现变量stop，加了vlatile修饰之后，线程t1对stop就可见了。其实，vlatile的作用就是：**保证变量对所有线程可见性**。当然，vlatile还有个作用就是，**禁止指令重排**，但是它**不保证原子性**。
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b5177218cf2b491da99d43392c7bd271~tplv-k3u1fbpfcp-zoom-1.image)

所以当面试官问你**volatile的作用或者特性**，都可以这么回答：
- 保证变量对所有线程可见性;
- 禁止指令重排序
- 不保证原子性

### 3. 现代计算机的内存模型（计算机模型，MESI协议，嗅探技术，总线）
为了更好理解volatile，先回顾一下计算机的内存模型与JMM（Java内存模型）吧~

#### 计算机模型
计算机执行程序时，指令是由CPU处理器执行的，而打交道的数据是在主内存当中的。

由于计算机的存储设备与处理器的运算速度有几个数量级的差距，总不能每次CPU执行完指令，然后等主内存慢悠悠存取数据吧，
所以现代计算机系统加入一层读写速度接近处理器运算速度的高速缓存（Cache），以作为来作为内存与处理器之间的缓冲。

在多路处理器系统中，每个处理器都有自己的高速缓存，而它们共享同一主内存。**计算机抽象内存模型**如下：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f995fb284a7241b9b95431e79a1c37b0~tplv-k3u1fbpfcp-zoom-1.image)

- 程序执行时，把需要用到的数据，从主内存拷贝一份到高速缓存。
- CPU处理器计算时，从它的高速缓存中读取，把计算完的数据写入高速缓存。
- 当程序运算结束，把高速缓存的数据刷新会主内存。

随着科学技术的发展，为了效率，高速缓存又衍生出一级缓存（L1），二级缓存（L2），甚至三级缓存(L3);
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/05fa9ff22d7f42ae82122bfc6c233bd9~tplv-k3u1fbpfcp-zoom-1.image)

当多个处理器的运算任务都涉及同一块主内存区域，可能导致**缓存数据不一致**问题。如何解决这个问题呢？有两种方案

> - 1、通过在总线加LOCK#锁的方式。
> - 2、通过缓存一致性协议（Cache Coherence Protocol）

#### 总线

> 总线（Bus）是计算机各种功能部件之间传送信息的公共通信干线，它是由导线组成的传输线束， 按照计算机所传输的信息种类，计算机的总线可以划分为数据总线、地址总线和控制总线，分别用来传输数据、数据地址和控制信号。

CPU和其他功能部件是通过总线通信的，如果在总线加LOCK#锁，那么在锁住总线期间，其他CPU是无法访问内存，这样一来，**效率就比较低了**。

#### MESI协议
为了解决一致性问题，还可以通过缓存一致性协议。即各个处理器访问缓存时都遵循一些协议，在读写时要根据协议来进行操作，这类协议有MSI、MESI（IllinoisProtocol）、MOSI、Synapse、Firefly及DragonProtocol等。比较著名的就是Intel的MESI（Modified Exclusive Shared Or Invalid）协议，它的核心思想是：

 >  当CPU写数据时，如果发现操作的变量是共享变量，即在其他CPU中也存在该变量的副本，会发出信号通知其他CPU将该变量的缓存行置为无效状态，因此当其他CPU需要读取这个变量时，发现自己缓存中缓存该变量的缓存行是无效的，那么它就会从内存重新读取。

CPU中每个缓存行标记的4种状态（M、E、S、I）,也了解一下吧:

| 缓存状态 | 描述 |
|-----|-----|
|  M，被修改（Modified) | 该缓存行只被该CPU缓存，与主存的值不同，会在它被其他CPU读取之前写入内存，并设置为Shared  | 
| E，独享的（Exclusive) | 该缓存行只被该CPU缓存，与主存的值相同，被其他CPU读取时置为Shared，被其他CPU写时置为Modified  | 
| S，共享的（Shared) | 该缓存行可能被多个CPU缓存，各个缓存中的数据与主存数据相同| 
| I，无效的（Invalid） | 该缓存行数据是无效，需要时需重新从主存载入 | 


MESI协议是如何实现的？如何保证当前处理器的内部缓存、主内存和其他处理器的缓存数据在总线上保持一致的？**多处理器总线嗅探**

#### 嗅探技术
> 在多处理器下，为了保证各个处理器的缓存是一致的，就会实现缓存缓存一致性协议，每个处理器通过嗅探在总线上传播的数据来检查自己的缓存值是不是过期了，如果处理器发现自己缓存行对应的内存地址被修改，就会将当前处理器的缓存行设置无效状态，当处理器对这个数据进行修改操作的时候，会重新从系统内存中把数据库读到处理器缓存中。

### 4. Java内存模型（JMM）

- Java虚拟机规范试图定义一种Java内存模型,来**屏蔽掉各种硬件和操作系统的内存访问差异**，以实现让Java程序在各种平台上都能达到一致的内存访问效果。
- Java内存模型**类比**于计算机内存模型。
- 为了更好的执行性能，java内存模型并没有限制执行引擎使用处理器的特定寄存器或缓存来和主内存打交道，也没有限制编译器进行调整代码顺序优化。所以Java内存模型**会存在缓存一致性问题和指令重排序问题的**。
- Java内存模型规定所有的变量都是存在主内存当中（类似于计算机模型中的物理内存），每个线程都有自己的工作内存（类似于计算机模型的高速缓存）。这里的**变量**包括实例变量和静态变量，但是**不包括局部变量**，因为局部变量是线程私有的。
- 线程的工作内存保存了被该线程使用的变量的主内存副本，**线程对变量的所有操作都必须在工作内存中进行**，而不能直接操作操作主内存。并且每个线程不能访问其他线程的工作内存。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a6ca8c98a8cc42c998dd4fe3324ded79~tplv-k3u1fbpfcp-zoom-1.image)

举个例子吧，假设i的初始值是0，执行以下语句：
```
i = i+1;
```
首先，执行线程t1从主内存中读取到i=0，到工作内存。然后在工作内存中，赋值i+1,工作内存就得到i=1，最后把结果写回主内存。因此，如果是单线程的话，该语句执行是没问题的。但是呢，线程t2的本地工作内存还没过期，那么它读到的数据就是脏数据了。如图：
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c0abe171336e4f89a58722a1569d64ce~tplv-k3u1fbpfcp-zoom-1.image)

Java内存模型是围绕着如何在并发过程中如何处理**原子性、可见性和有序性**这3个特征来建立的，我们再来一起回顾一下~

### 5.并发编程的3个特性（原子性、可见性、有序性）

#### 原子性

原子性，指操作是不可中断的，要么执行完成，要么不执行，基本数据类型的访问和读写都是具有原子性，当然（long和double的非原子性协定除外）。我们来看几个小例子：
```
i =666； // 语句1
i = j;   // 语句2
i = i+1;  //语句 3
i++;   // 语句4
```
- 语句1操作显然是原子性的，将数值666赋值给i，即线程执行这个语句时，直接将数值666写入到工作内存中。
- 语句2操作看起来也是原子性的，但是它实际上涉及两个操作，先去读j的值，再把j的值写入工作内存，两个操作分开都是原子操作，但是合起来就不满足原子性了。
- 语句3读取i的值，加1，再写回主存，这个就不是原子性操作了。
- 语句4 等同于语句3，也是非原子性操作。

#### 可见性

- 可见性就是指当一个线程修改了共享变量的值时，其他线程能够立即得知这个修改。
- Java内存模型是通过在变量修改后将新值同步回主内存，在变量读取前从主内存刷新变量值这种依赖主内存作为传递媒介的方式来实现可见性的，无论是普通变量还是volatile变量都是如此。
- volatile变量，保证新值能立即同步回主内存，以及每次使用前立即从主内存刷新，所以我们说volatile保证了多线程操作变量的可见性。
- synchronized和Lock也能够保证可见性，线程在释放锁之前，会把共享变量值都刷回主存。final也可以实现可见性。

 
#### 有序性

Java虚拟机这样描述Java程序的有序性的：如果在本线程内观察，所有的操作都是有序的；如果在一个线程中，观察另一个线程，所有的操作都是无序的。

后半句意思就是，在Java内存模型中，**允许编译器和处理器对指令进行重排序**，会影响到多线程并发执行的正确性；前半句意思就是**as-if-serial**的语义，即不管怎么重排序（编译器和处理器为了提高并行度），（单线程）程序的执行结果不会被改变。

比如以下程序代码：
```
double pi  = 3.14;    //A
double r   = 1.0;     //B
double area = pi * r * r; //C
```
步骤C依赖于步骤A和B，因为指令重排的存在，程序执行顺讯可能是A->B->C,也可能是B->A->C,但是C不能在A或者B前面执行，这将违反as-if-serial语义。
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/87387195b86749ddbe1f36582a562988~tplv-k3u1fbpfcp-zoom-1.image)

看段代码吧，假设程序先执行read方法，再执行add方法，结果一定是输出sum=2嘛？
```
bool flag = false;
int b = 0;

public void read() {
   b = 1;              //1
   flag = true;        //2
}

public void add() {
   if (flag) {         //3
       int sum =b+b;   //4
       System.out.println("bb sum is"+sum); 
   } 
}

```

如果是单线程，结果应该没问题，如果是多线程，线程t1对步骤1和2进行了**指令重排序**呢？结果sum就不是2了，而是0，如下图所示：
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/093c6e37453c462985bed78c4d239979~tplv-k3u1fbpfcp-zoom-1.image)

这是为啥呢？**指令重排序**了解一下，指令重排是指在程序执行过程中,**为了提高性能**, **编译器和CPU可能会对指令进行重新排序**。CPU重排序包括指令并行重排序和内存系统重排序，重排序类型和重排序执行过程如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d8a24934eab24bf9b80c402039371aa2~tplv-k3u1fbpfcp-zoom-1.image)

实际上，可以给flag加上volatile关键字，来保证有序性。当然，也可以通过synchronized和Lock来保证有序性。synchronized和Lock保证某一时刻是只有一个线程执行同步代码，相当于是让线程顺序执行程序代码了，自然就保证了有序性。

实际上Java内存模型的有序性并不是仅靠volatile、synchronized和Lock来保证有序性的。这是因为Java语言中，有一个先行发生原则（happens-before）：
- **程序次序规则**：在一个线程内，按照控制流顺序，书写在前面的操作先行发生于书写在后面的操作。
- **管程锁定规则**：一个unLock操作先行发生于后面对同一个锁额lock操作
- **volatile变量规则**：对一个变量的写操作先行发生于后面对这个变量的读操作
- **线程启动规则**：Thread对象的start()方法先行发生于此线程的每个一个动作
- **线程终止规则**：线程中所有的操作都先行发生于线程的终止检测，我们可以通过Thread.join()方法结束、Thread.isAlive()的返回值手段检测到线程已经终止执行
- **线程中断规则**：对线程interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生
- **对象终结规则**：一个对象的初始化完成先行发生于他的finalize()方法的开始
- **传递性**：如果操作A先行发生于操作B，而操作B又先行发生于操作C，则可以得出操作A先行发生于操作C

根据happens-before的八大规则，我们回到刚的例子，一起分析一下。给flag加上volatile关键字，look look它是如何保证有序性的，
```
volatile bool flag = false;
int b = 0;

public void read() {
   b = 1;              //1
   flag = true;        //2
}

public void add() {
   if (flag) {         //3
       int sum =b+b;   //4
       System.out.println("bb sum is"+sum); 
   } 
}
```
- 首先呢，flag加上volatile关键字，那就禁止了指令重排，也就是1 happens-before 2了
- 根据**volatile变量规则**，2 happens-before 3
- 由**程序次序规则**，得出 3 happens-before 4
- 最后由**传递性**，得出1 happens-before 4，因此妥妥的输出sum=2啦~

### 6.volatile底层原理

以上讨论学习，我们知道volatile的语义就是保证变量对所有线程可见性以及禁止指令重排优化。那么，它的底层是如何保证可见性和禁止指令重排的呢？

#### 图解volatile是如何保证可见性的？
在这里，先看几个图吧，哈哈~

假设flag变量的初始值false，现在有两条线程t1和t2要访问它，就可以简化为以下图：
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7ddbe230c8dc4501a77ffbe0587b5ba6~tplv-k3u1fbpfcp-zoom-1.image)

如果线程t1执行以下代码语句，并且flag没有volatile修饰的话；t1刚修改完flag的值，还没来得及刷新到主内存，t2又跑过来读取了，很容易就数据flag不一致了，如下：

```
flag=true;
```
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cc065cf75803496aa1efafd6d68ba968~tplv-k3u1fbpfcp-zoom-1.image)

如果flag变量是由volatile修饰的话，就不一样了，如果线程t1修改了flag值，volatile能保证修饰的flag变量后，可以**立即同步回主内存**。如图：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/27e9e195810a4a71bdeb38dd128b27e4~tplv-k3u1fbpfcp-zoom-1.image)

细心的朋友会发现，线程t2不还是flag旧的值吗，这不还有问题嘛？其实volatile还有一个保证，就是**每次使用前立即先从主内存刷新最新的值**，线程t1修改完后，线程t2的变量副本会过期了，如图：
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7e67dcdfe9d9412dab89961bf92b5b53~tplv-k3u1fbpfcp-zoom-1.image)

显然，这里还不是底层，实际上volatile保证可见性和禁止指令重排都跟**内存屏障**有关，我们编译volatile相关代码看看~

#### DCL单例模式（volatile）&编译对比
DCL单例模式（Double Check Lock，双重检查锁）比较常用，它是需要volatile修饰的，所以就拿这段代码编译吧
```
public class Singleton {  
    private volatile static Singleton instance;  
    private Singleton (){}  
    public static Singleton getInstance() {  
    if (instance == null) {  
        synchronized (Singleton.class) {  
        if (instance == null) {  
            instance = new Singleton();  
        }  
        }  
    }  
    return instance;  
    }  
}  
```
编译这段代码后，观察有volatile关键字和没有volatile关键字时的instance所生成的汇编代码发现，有volatile关键字修饰时，会多出一个lock addl $0x0,(%esp)，即多出一个lock前缀指令
```
0x01a3de0f: mov    $0x3375cdb0,%esi   ;...beb0cd75 33  
                                        ;   {oop('Singleton')}  
0x01a3de14: mov    %eax,0x150(%esi)   ;...89865001 0000  
0x01a3de1a: shr    $0x9,%esi          ;...c1ee09  
0x01a3de1d: movb   $0x0,0x1104800(%esi)  ;...c6860048 100100  
0x01a3de24: lock addl $0x0,(%esp)     ;...f0830424 00  
                                        ;*putstatic instance  
                                        ; - Singleton::getInstance@24 
```
 lock指令相当于一个**内存屏障**，它保证以下这几点：
 > - 1.重排序时不能把后面的指令重排序到内存屏障之前的位置
 > - 2.将本处理器的缓存写入内存
 > - 3.如果是写入动作，会导致其他处理器中对应的缓存无效。

显然，第2、3点不就是volatile保证可见性的体现嘛，第1点就是禁止指令重排列的体现。

#### 内存屏障

内存屏障四大分类：（Load 代表读取指令，Store代表写入指令）

| 内存屏障类型 | 抽象场景| 描述|
|-----|-----|-----|
|LoadLoad屏障| Load1; LoadLoad; Load2|在Load2要读取的数据被访问前，保证Load1要读取的数据被读取完毕。|
|StoreStore屏障|Store1; StoreStore; Store2|在Store2写入执行前，保证Store1的写入操作对其它处理器可见|
|LoadStore屏障|Load1; LoadStore; Store2| 在Store2被写入前，保证Load1要读取的数据被读取完毕。|
|StoreLoad屏障|Store1; StoreLoad; Load2|在Load2读取操作执行前，保证Store1的写入对所有处理器可见。|

为了实现volatile的内存语义，Java内存模型采取以下的保守策略
- 在每个volatile写操作的前面插入一个StoreStore屏障。
- 在每个volatile写操作的后面插入一个StoreLoad屏障。
- 在每个volatile读操作的后面插入一个LoadLoad屏障。
- 在每个volatile读操作的后面插入一个LoadStore屏障。

有些小伙伴，可能对这个还是有点疑惑，内存屏障这玩意太抽象了。我们照着代码看下吧（LoadLoad内存屏障也是在flag后面哈，图片有误）：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a85eff53f99f420c8139bb69b2d4f6ae~tplv-k3u1fbpfcp-zoom-1.image)

内存屏障保证前面的指令先执行，所以这就保证了禁止了指令重排啦，同时内存屏障保证缓存写入内存和其他处理器缓存失效，这也就保证了可见性，哈哈~

### 7.volatile的典型场景
通常来说，使用volatile必须具备以下2个条件：
- 1）对变量的写操作不依赖于当前值
- 2）该变量没有包含在具有其他变量的不变式中

实际上，volatile场景一般就是**状态标志**，以及**DCL单例模式**。

#### 7.1 状态标志
深入理解Java虚拟机，书中的例子：
```
Map configOptions;
char[] configText;
// 此变量必须定义为 volatile
volatile boolean initialized = false;

// 假设以下代码在线程 A 中运行
// 模拟读取配置信息, 当读取完成后将 initialized 设置为 true 以告知其他线程配置可用
configOptions = new HashMap();
configText = readConfigFile(fileName);
processConfigOptions(configText, configOptions);
initialized = true;
      
// 假设以下代码在线程 B 中运行
// 等待 initialized 为 true, 代表线程 A 已经把配置信息初始化完成
while(!initialized) {
   sleep();
}
// 使用线程 A 中初始化好的配置信息
doSomethingWithConfig();
```
#### 7.2 DCL单例模式
```
class Singleton{
    private volatile static Singleton instance = null;
     
    private Singleton() {   
    }
     
    public static Singleton getInstance() {
        if(instance==null) {
            synchronized (Singleton.class) {
                if(instance==null)
                    instance = new Singleton();
            }
        }
        return instance;
    }
}

```

### 8. volatile相关经典面试题
- 谈谈volatile的特性
- volatile的内存语义  
- 说说并发编程的3大特性
- 什么是内存可见性，什么是指令重排序？
- volatile是如何解决java并发中可见性的问题
- volatile如何防止指令重排
- volatile可以解决原子性嘛？为什么？
- volatile底层的实现机制
- volatile和synchronized的区别？

#### 8.1 谈谈volatile的特性

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8e624231e978455ebec33c7380ffba0d~tplv-k3u1fbpfcp-zoom-1.image)

#### 8.2  volatile的内存语义

- 当写一个 volatile 变量时，JMM 会把该线程对应的本地内存中的共享变量值刷新到主内存。
- 当读一个 volatile 变量时，JMM 会把该线程对应的本地内存置为无效。线程接下来将从主内存中读取共享变量。

#### 8.3 说说并发编程的3大特性
- 原子性
- 可见性
- 有序性

#### 8.4 什么是内存可见性，什么是指令重排序？
- 可见性就是指当一个线程修改了共享变量的值时，其他线程能够立即得知这个修改。
- 指令重排是指JVM在编译Java代码的时候，或者CPU在执行JVM字节码的时候，对现有的指令顺序进行重新排序。

#### 8.5 volatile是如何解决java并发中可见性的问题

底层是通过内存屏障实现的哦，volatile能保证修饰的变量后，可以立即同步回主内存，每次使用前立即先从主内存刷新最新的值。

#### 8.6 volatile如何防止指令重排

也是内存屏障哦，跟面试官讲下Java内存的保守策略：
- 在每个volatile写操作的前面插入一个StoreStore屏障。
- 在每个volatile写操作的后面插入一个StoreLoad屏障。
- 在每个volatile读操作的后面插入一个LoadLoad屏障。
- 在每个volatile读操作的后面插入一个LoadStore屏障。

再讲下volatile的语义哦，重排序时不能把内存屏障后面的指令重排序到内存屏障之前的位置

#### 8.7 volatile可以解决原子性嘛？为什么？

不可以，可以直接举i++那个例子，原子性需要synchronzied或者lock保证

```
public class Test {
    public volatile int race = 0;
     
    public void increase() {
        race++;
    }
     
    public static void main(String[] args) {
        final Test test = new Test();
        for(int i=0;i<10;i++){
            new Thread(){
                public void run() {
                    for(int j=0;j<100;j++)
                        test.increase();
                };
            }.start();
        }
        
        //等待所有累加线程结束
        while(Thread.activeCount()>1)  
            Thread.yield();
        System.out.println(test.race);
    }
}
```

 #### 8.8 volatile底层的实现机制
 
 可以看本文的第六小节，volatile底层原理哈，主要你要跟面试官讲述，volatile如何保证可见性和禁止指令重排，需要讲到内存屏障~
 
 #### 8.9 volatile和synchronized的区别？
 - volatile修饰的是变量，synchronized一般修饰代码块或者方法
 - volatile保证可见性、禁止指令重排，但是不保证原子性；synchronized可以保证原子性
 - volatile不会造成线程阻塞，synchronized可能会造成线程的阻塞，所以后面才有锁优化那么多故事~
 - 哈哈，你还有补充嘛~

推荐之前写的一篇文章：
[Synchronized解析——如果你愿意一层一层剥开我的心](https://juejin.im/post/6844903918653145102)


### 公众号
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/07e9fd0521c244adab8556fee99a2011~tplv-k3u1fbpfcp-zoom-1.image)

### 参考与感谢
- <<深入理解Java虚拟机>>
- [Java并发编程：volatile关键字解析](https://www.cnblogs.com/dolphin0520/p/3920373.html)
- [面试官最爱的volatile关键字](https://juejin.im/post/6844903520760496141)
- [ 面试官没想到一个Volatile，我都能跟他扯半小时](https://juejin.im/post/6844904149536997384)
- [再有人问你Java内存模型是什么，就把这篇文章发给他。](http://47.103.216.138/archives/2550)
- [【并发编程】MESI--CPU缓存一致性协议](https://www.cnblogs.com/z00377750/p/9180644.html)
- [漫画：volatile对指令重排的影响 ](https://www.sohu.com/a/211287207_684445)
- [volatile三大特性详解](https://www.jianshu.com/p/765e3abbe89a)

