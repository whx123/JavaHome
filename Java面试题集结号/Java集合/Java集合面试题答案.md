## 前言
来了来了，50道Java集合面试题也来啦~ 已经上传github:
> https://github.com/whx123/JavaHome

### 1. Arraylist与LinkedList区别
可以从它们的底层数据结构、效率、开销进行阐述哈
- ArrayList是数组的数据结构，LinkedList是链表的数据结构。
- 随机访问的时候，ArrayList的效率比较高，因为LinkedList要移动指针，而ArrayList是基于索引(index)的数据结构，可以直接映射到。
- 插入、删除数据时，LinkedList的效率比较高，因为ArrayList要移动数据。
- LinkedList比ArrayList开销更大，因为LinkedList的节点除了存储数据，还需要存储引用。

### 2. Collections.sort和Arrays.sort的实现原理

Collection.sort是对list进行排序，Arrays.sort是对数组进行排序。

#### Collections.sort底层实现

Collections.sort方法调用了list.sort方法
![](https://user-gold-cdn.xitu.io/2020/5/27/1725341b6dbcca6a?w=787&h=139&f=png&s=13963)
list.sort方法调用了Arrays.sort的方法
![](https://user-gold-cdn.xitu.io/2020/5/27/1725341d6c861c7a?w=530&h=140&f=png&s=11349)
因此，**Collections.sort方法底层就是调用的Array.sort方法**

#### Arrays.sort底层实现
Arrays的sort方法，如下：
![](https://user-gold-cdn.xitu.io/2020/5/27/17253551bc95b431?w=912&h=307&f=png&s=32161)
如果比较器为null，进入sort（a）方法。如下：
![](https://user-gold-cdn.xitu.io/2020/5/27/17253549f9433160?w=956&h=213&f=png&s=25612)
因此，Arrays的sort方法底层就是：
- legacyMergeSort(a)，归并排序，
- ComparableTimSort.sort()：即Timsort排序。

#### Timesort排序
Timsort排序是结合了合并排序（merge.sort）和插入排序（insertion sort）而得出的排序方法；

1.当数组长度小于某个值，采用的是二分插入排序算法，如下：

![](https://user-gold-cdn.xitu.io/2020/5/27/172567bc65ac1d96?w=755&h=425&f=png&s=50372)

2. 找到各个run，并入栈。

![](https://user-gold-cdn.xitu.io/2020/5/27/172568f27aa6725d?w=905&h=767&f=png&s=70722)

3. 按规则合并run。

![](https://user-gold-cdn.xitu.io/2020/5/27/172568e815ffcd33?w=765&h=379&f=png&s=31321)

### 3. HashMap原理，java8做了什么改变
- HashMap是以键值对存储数据的集合容器
- HashMap是非线性安全的。
- HashMap底层数据结构：数组+(链表、红黑树)，jdk8之前是用数组+链表的方式实现，jdk8引进了红黑树
- Hashmap数组的默认初始长度是16，key和value都允许null的存在
- HashMap的内部实现数组是Node[]数组，上面存放的是key-value键值对的节点。HashMap通过put和get方法存储和获取。
- HashMap的put方法，首先计算key的hashcode值，定位到对应的数组索引，然后再在该索引的单向链表上进行循环遍历，用equals比较key是否存在，如果存在则用新的value覆盖原值，如果没有则向后追加。
- jdk8中put方法：先判断Hashmap是否为空，为空就扩容，不为空计算出key的hash值i，然后看table[i]是否为空，为空就直接插入，不为空判断当前位置的key和table[i]是否相同，相同就覆盖，不相同就查看table[i]是否是红黑树节点，如果是的话就用红黑树直接插入键值对，如果不是开始遍历链表插入，如果遇到重复值就覆盖，否则直接插入，如果链表长度大于8，转为红黑树结构，执行完成后看size是否大于阈值threshold，大于就扩容，否则直接结束。
- Hashmap解决hash冲突，使用的是链地址法，即数组+链表的形式来解决。put执行首先判断table[i]位置，如果为空就直接插入，不为空判断和当前值是否相等，相等就覆盖，如果不相等的话，判断是否是红黑树节点，如果不是，就从table[i]位置开始遍历链表，相等覆盖，不相等插入。
- HashMap的get方法就是计算出要获取元素的hash值，去对应位置获取即可。
- HashMap的扩容机制，Hashmap的扩容中主要进行两步，第一步把数组长度变为原来的两倍，第二部把旧数组的元素重新计算hash插入到新数组中，jdk8时，不用重新计算hash，只用看看原来的hash值新增的一位是零还是1，如果是1这个元素在新数组中的位置，是原数组的位置加原数组长度，如果是零就插入到原数组中。扩容过程第二部一个非常重要的方法是transfer方法，采用头插法，把旧数组的元素插入到新数组中。
- HashMap大小为什么是2的幂次方？效率高+空间分布均匀

有关于HashMap这些常量设计目的，也可以看我这篇文章：
[面试加分项-HashMap源码中这些常量的设计目的](https://juejin.im/post/5d7195f9f265da03a6533942)

### 4. List 和 Set，Map 的区别
- List 以索引来存取元素，有序的，元素是允许重复的，可以插入多个null。
- Set 不能存放重复元素，无序的，只允许一个null
- Map 保存键值对映射，映射关系可以一对一、多对一
- List 有基于数组、链表实现两种方式
- Set、Map 容器有基于哈希存储和红黑树两种方式实现
- Set 基于 Map 实现，Set 里的元素值就是 Map的键值

### 5. poll()方法和 remove()方法的区别？
Queue队列中，poll() 和 remove() 都是从队列中取出一个元素，在队列元素为空的情况下，remove() 方法会抛出异常，poll() 方法只会返回 null 。

看一下源码的解释吧：
```
 /**
     * Retrieves and removes the head of this queue.  This method differs
     * from {@link #poll poll} only in that it throws an exception if this
     * queue is empty.
     *
     * @return the head of this queue
     * @throws NoSuchElementException if this queue is empty
     */
    E remove();
    
        /**
     * Retrieves and removes the head of this queue,
     * or returns {@code null} if this queue is empty.
     *
     * @return the head of this queue, or {@code null} if this queue is empty
     */
    E poll();
```

### 6. HashMap，HashTable，ConcurrentHash的共同点和区别

**HashMap**
- 底层由链表+数组+红黑树实现
- 可以存储null键和null值
- 线性不安全
- 初始容量为16，扩容每次都是2的n次幂
- 加载因子为0.75，当Map中元素总数超过Entry数组的0.75，触发扩容操作.
- 并发情况下，HashMap进行put操作会引起死循环，导致CPU利用率接近100%
- HashMap是对Map接口的实现


**HashTable**
- HashTable的底层也是由链表+数组+红黑树实现。
- 无论key还是value都不能为null
- 它是线性安全的，使用了synchronized关键字。
- HashTable实现了Map接口和Dictionary抽象类
- Hashtable初始容量为11

**ConcurrentHashMap**
- ConcurrentHashMap的底层是数组+链表+红黑树
- 不能存储null键和值
- ConcurrentHashMap是线程安全的
- ConcurrentHashMap使用锁分段技术确保线性安全
- JDK8为何又放弃分段锁，是因为多个分段锁浪费内存空间，竞争同一个锁的概率非常小，分段锁反而会造成效率低。

### 7. 写一段代码在遍历 ArrayList 时移除一个元素
因为foreach删除会导致快速失败问题，fori顺序遍历会导致重复元素没删除，所以正确解法如下：

第一种遍历，倒叙遍历删除
```
for(int i=list.size()-1; i>-1; i--){
  if(list.get(i).equals("jay")){
    list.remove(list.get(i));
  }
}
```
第二种，迭代器删除
```
Iterator itr = list.iterator();
while(itr.hasNext()) {
      if(itr.next().equals("jay") {
        itr.remove();
      }
}
```

### 8. Java中怎么打印数组？
数组是不能直接打印的哈，如下:

```
public class Test {

    public static void main(String[] args) {
        String[] jayArray = {"jay", "boy"};
        System.out.println(jayArray);
    }
}
//output
[Ljava.lang.String;@1540e19d
```

打印数组可以用流的方式Strem.of().foreach()，如下:
```
public class Test {

    public static void main(String[] args) {
        String[] jayArray = {"jay", "boy"};
        Stream.of(jayArray).forEach(System.out::println);
    }
}
//output
jay
boy
```
打印数组，最优雅的方式可以用这个APi,Arrays.toString()
```
public class Test {
    public static void main(String[] args) {
        String[] jayArray = {"jay", "boy"};
        System.out.println(Arrays.toString(jayArray));
    }
}
//output
[jay, boy]
```

### 9. TreeMap底层？
- TreeMap实现了SotredMap接口，它是有序的集合。
- TreeMap底层数据结构是一个红黑树，每个key-value都作为一个红黑树的节点。
- 如果在调用TreeMap的构造函数时没有指定比较器，则根据key执行自然排序。

![](https://user-gold-cdn.xitu.io/2020/6/12/172a4245313c7162?w=864&h=431&f=png&s=31334)

### 10. HashMap 的扩容过程
Hashmap的扩容：
- 第一步把数组长度变为原来的两倍，
- 第二步把旧数组的元素重新计算hash插入到新数组中。
- jdk8时，不用重新计算hash，只用看看原来的hash值新增的一位是零还是1，如果是1这个元素在新数组中的位置，是原数组的位置加原数组长度，如果是零就插入到原数组中。扩容过程第二步一个非常重要的方法是transfer方法，采用头插法，把旧数组的元素插入到新数组中。

### 11. HashSet是如何保证不重复的
可以看一下HashSet的add方法，元素E作为HashMap的key，我们都知道HashMap的可以是不允许重复的，哈哈。
```
 public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```
### 12. HashMap 是线程安全的吗，为什么不是线程安全的？死循环问题？
不是线性安全的。

并发的情况下，扩容可能导致死循环问题。

### 13. LinkedHashMap的应用，底层，原理
- LinkedHashMap维护着一个运行于所有条目的双重链接列表。此链接列表定义了迭代顺序，该迭代顺序可以是插入顺序（insert-order）或者是访问顺序，其中默认的迭代访问顺序就是插入顺序，即可以按插入的顺序遍历元素，这点和HashMap有很大的不同。
- LRU算法可以用LinkedHashMap实现。

### 14. 哪些集合类是线程安全的？哪些不安全？
线性安全的
- Vector：比Arraylist多了个同步化机制。
- Hashtable：比Hashmap多了个线程安全。
- ConcurrentHashMap:是一种高效但是线程安全的集合。
- Stack：栈，也是线程安全的，继承于Vector。

线性不安全的
- Hashmap
- Arraylist
- LinkedList
- HashSet
- TreeSet
- TreeMap

### 15. ArrayList 和 Vector 的区别是什么？
- Vector是线程安全的，ArrayList不是线程安全的。
- ArrayList在底层数组不够用时在原来的基础上扩展0.5倍，Vector是扩展1倍。
- Vector只要是关键性的操作，方法前面都加了synchronized关键字，来保证线程的安全性。

![](https://user-gold-cdn.xitu.io/2020/5/28/172588218a417dc8?w=604&h=210&f=png&s=18179)

### 16. Collection与Collections的区别是什么？
- Collection<E>是Java集合框架中的基本接口，如List接口也是继承于它

```
public interface List<E> extends Collection<E> {
```
- Collections是Java集合框架提供的一个工具类，其中包含了大量用于操作或返回集合的静态方法。如下：

```
public static <T extends Comparable<? super T>> void sort(List<T> list) {
    list.sort(null);
}
```

### 17. 如何决定使用 HashMap 还是TreeMap？
这个点，主要考察HashMap和TreeMap的区别。

TreeMap实现SortMap接口，能够把它保存的记录根据键排序，默认是按key的升序排序，也可以指定排序的比较器。当用Iterator遍历TreeMap时，得到的记录是排过序的。

### 18. 如何实现数组和 List之间的转换？

#### List 转 Array
List 转Array，必须使用集合的 toArray(T[] array)，如下：
```
List<String> list = new ArrayList<String>();
list.add("jay");
list.add("tianluo");

// 使用泛型，无需显式类型转换
String[] array = list.toArray(new String[list.size()]);
System.out.println(array[0]);
```

如果直接使用 toArray 无参方法，返回值只能是 Object[] 类，强转其他类型可能有问题，demo如下：

```
List<String> list = new ArrayList<String>();
list.add("jay");
list.add("tianluo");

String[] array = (String[]) list.toArray();
System.out.println(array[0]);
```
运行结果：

```
Exception in thread "main" java.lang.ClassCastException: [Ljava.lang.Object; cannot be cast to [Ljava.lang.String;
	at Test.main(Test.java:14)
```

#### Array 转List
使用Arrays.asList() 把数组转换成集合时，不能使用修改集合相关的方法啦，如下：

```
String[] str = new String[] { "jay", "tianluo" };
List list = Arrays.asList(str);
list.add("boy");
```
运行结果如下：

```
Exception in thread "main" java.lang.UnsupportedOperationException
	at java.util.AbstractList.add(AbstractList.java:148)
	at java.util.AbstractList.add(AbstractList.java:108)
	at Test.main(Test.java:13)
```

因为 Arrays.asList不是返回java.util.ArrayList,而是一个内部类ArrayList。
![](https://user-gold-cdn.xitu.io/2020/5/31/1726a89f75bf8725?w=804&h=281&f=png&s=31691)


可以这样使用弥补这个缺点：

```
//方式一：
ArrayList< String> arrayList = new ArrayList<String>(strArray.length);
Collections.addAll(arrayList, strArray);
//方式二：
ArrayList<String> list = new ArrayList<String>(Arrays.asList(strArray)) ;
```
### 19. 迭代器 Iterator 是什么？怎么用，有什么特点？

```
public interface Collection<E> extends Iterable<E> {

Iterator<E> iterator();
```

方法如下：

```
next() 方法获得集合中的下一个元素
hasNext() 检查集合中是否还有元素
remove() 方法将迭代器新返回的元素删除
forEachRemaining(Consumer<? super E> action) 方法，遍历所有元素
```

Iterator 主要是用来遍历集合用的，它的特点是更加安全，因为它可以确保，在当前遍历的集合元素被更改的时候，就会抛出 ConcurrentModificationException 异常。

使用demo如下：
```
List<String> list = new ArrayList<>();
Iterator<String> it = list. iterator();
while(it. hasNext()){
  String obj = it. next();
  System. out. println(obj);
}
```

### 20. Iterator 和 ListIterator 有什么区别？

![](https://user-gold-cdn.xitu.io/2020/6/6/1728696f00e6cc3e?w=547&h=189&f=png&s=17728)

![](https://user-gold-cdn.xitu.io/2020/6/6/1728698274a8137f?w=527&h=363&f=png&s=34263)

- ListIterator 比 Iterator有更多的方法。
- ListIterator只能用于遍历List及其子类，Iterator可用来遍历所有集合，
- ListIterator遍历可以是逆向的，因为有previous()和hasPrevious()方法，而Iterator不可以。
- ListIterator有add()方法，可以向List添加对象，而Iterator却不能。
- ListIterator可以定位当前的索引位置，因为有nextIndex()和previousIndex()方法，而Iterator不可以。
- ListIterator可以实现对象的修改，set()方法可以实现。Iierator仅能遍历，不能修改哦。

### 21. 怎么确保一个集合不能被修改？
很多朋友很可能想到用final关键字进行修饰，final修饰的这个成员变量，如果是基本数据类型，表示这个变量的值是不可改变的，如果是引用类型，则表示这个引用的地址值是不能改变的，但是这个引用所指向的对象里面的内容还是可以改变滴~验证一下，如下：
```
public class Test {
    //final 修饰
    private static final Map<Integer, String> map = new HashMap<Integer, String>();
    {
        map.put(1, "jay");
        map.put(2, "tianluo");
    }

    public static void main(String[] args) {
        map.put(1, "boy");
        System.out.println(map.get(1));
    }
}
```
运行结果如下:

```
//可以洗发现，final修饰，集合还是会被修改呢
boy
```

嘻嘻，那么，到底怎么确保一个集合不能被修改呢，看以下这三哥们~
- unmodifiableMap
- unmodifiableList
- unmodifiableSet

再看一下demo吧
```
public class Test {

    private static  Map<Integer, String> map = new HashMap<Integer, String>();
    {
        map.put(1, "jay");
        map.put(2, "tianluo");

    }

    public static void main(String[] args) {
        map = Collections.unmodifiableMap(map);
        map.put(1, "boy");
        System.out.println(map.get(1));
    }
}

```
运行结果：

```
// 可以发现，unmodifiableMap确保集合不能修改啦，抛异常了
Exception in thread "main" java.lang.UnsupportedOperationException
	at java.util.Collections$UnmodifiableMap.put(Collections.java:1457)
	at Test.main(Test.java:14)
```

### 22. 快速失败(fail-fast)和安全失败(fail-safe)的区别是什么？
#### 快速失败
在用迭代器遍历一个集合对象时，如果遍历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出Concurrent Modification Exception。

```
public class Test {

    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);

        Iterator iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
            list.add(3);
            System.out.println(list.size());
        }

    }
}
```
运行结果：

```
1
Exception in thread "main" java.util.ConcurrentModificationException
3
	at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:909)
	at java.util.ArrayList$Itr.next(ArrayList.java:859)
	at Test.main(Test.java:12)
```
#### 安全失败
采用安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，在拷贝的集合上进行遍历。

```
public class Test {

    public static void main(String[] args) {
        List<Integer> list = new CopyOnWriteArrayList<>();
        list.add(1);
        list.add(2);

        Iterator iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
            list.add(3);
            System.out.println("list size:"+list.size());
        }

    }
}

```
运行结果：

```
1
list size:3
2
list size:4
```
其实，在java.util.concurrent 并发包的集合，如 ConcurrentHashMap, CopyOnWriteArrayList等，默认为都是安全失败的。

### 23. 什么是Java优先级队列(Priority Queue)？

优先队列PriorityQueue是Queue接口的实现，可以对其中元素进行排序
- 优先队列中元素默认排列顺序是升序排列
- 但对于自己定义的类来说，需要自己定义比较器
```
public class PriorityQueue<E> extends AbstractQueue<E>
    implements java.io.Serializable {
    ...
     private final Comparator<? super E> comparator;
```

方法：

```
peek()//返回队首元素
poll()//返回队首元素，队首元素出队列
add()//添加元素
size()//返回队列元素个数
isEmpty()//判断队列是否为空，为空返回true,不空返回false
```

特点：
- 1.基于优先级堆 
- 2.不允许null值
- 3.线程不安全
- 4.出入队时间复杂度O(log(n))
- 5.调用remove()返回堆内最小值

### 24. JAVA8的ConcurrentHashMap为什么放弃了分段锁，有什么问题吗，如果你来设计，你如何设计。

jdk8 放弃了分段锁而是用了Node锁，减低锁的粒度，提高性能，并使用CAS操作来确保Node的一些操作的原子性，取代了锁。

可以跟面试官聊聊悲观锁和CAS乐观锁的区别，优缺点哈~


### 25. 阻塞队列的实现，ArrayBlockingQueue的底层实现？
ArrayBlockingQueue是数组实现的线程安全的有界的阻塞队列，继承自AbstractBlockingQueue,间接的实现了Queue接口和Collection接口。底层以数组的形式保存数据(实际上可看作一个循环数组)。常用的操作包括 add ,offer,put，remove,poll,take,peek。

![](https://user-gold-cdn.xitu.io/2020/6/12/172a58069511d5db?w=808&h=537&f=png&s=41200)

可以结合线程池跟面试官讲一下哦~

### 26. Java 中的 LinkedList是单向链表还是双向链表？

哈哈，看源码吧，是双向链表
```
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

### 27. 说一说ArrayList 的扩容机制吧
ArrayList扩容的本质就是计算出新的扩容数组的size后实例化，并将原有数组内容复制到新数组中去。

```

 public boolean add(E e) {
     //扩容
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
    }
  private void ensureCapacityInternal(int minCapacity) {
      ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

private static int calculateCapacity(Object[] elementData, int minCapacity) {
        //如果传入的是个空数组则最小容量取默认容量与minCapacity之间的最大值
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }
    
  private void ensureExplicitCapacity(int minCapacity) {
        modCount++;
        // 如果最小需要空间比elementData的内存空间要大，则需要扩容
        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
    
       private void grow(int minCapacity) {
        // 获取elementData数组的内存空间长度
        int oldCapacity = elementData.length;
        // 扩容至原来的1.5倍
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //校验容量是否够
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        //若预设值大于默认的最大值，检查是否溢出
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // 调用Arrays.copyOf方法将elementData数组指向新的内存空间
         //并将elementData的数据复制到新的内存空间
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
### 28. HashMap 的长度为什么是2的幂次方，以及其他常量定义的含义~
为了能让HashMap存取高效，数据分配均匀。

看着呢，以下等式相等，但是位移运算比取余效率高很多呢~

```
hash%length=hash&(length-1)
```


可以看下我这篇文章哈~
[面试加分项-HashMap源码中这些常量的设计目的](https://juejin.im/post/5d7195f9f265da03a6533942)

### 29. ConcurrenHashMap 原理？1.8 中为什么要用红黑树？

聊到ConcurrenHashMap，需要跟面试官聊到安全性，分段锁segment，为什么放弃了分段锁，与及选择CAS，其实就是都是从效率和安全性触发，嘻嘻~

```
java8不是用红黑树来管理hashmap,而是在hash值相同的情况下(且重复数量大于8),用红黑树来管理数据。
红黑树相当于排序数据。可以自动的使用二分法进行定位。性能较高。
```



### 30. ArrayList的默认大小
ArrayList 的默认大小是 10 个元素

```
/**
  * Default initial capacity.
  */
private static final int DEFAULT_CAPACITY = 10;
```
### 31. 为何Collection不从Cloneable和Serializable接口继承？
> - Collection表示一个集合，包含了一组对象元素。如何维护它的元素对象是由具体实现来决定的。因为集合的具体形式多种多样，例如list允许重复，set则不允许。而克隆（clone）和序列化（serializable）只对于具体的实体，对象有意义，你不能说去把一个接口，抽象类克隆，序列化甚至反序列化。所以具体的collection实现类是否可以克隆，是否可以序列化应该由其自身决定，而不能由其超类强行赋予。 
> - 如果collection继承了clone和serializable，那么所有的集合实现都会实现这两个接口，而如果某个实现它不需要被克隆，甚至不允许它序列化（序列化有风险），那么就与collection矛盾了。

### 32. Enumeration和Iterator接口的区别？

```
public interface Enumeration<E> {
    boolean hasMoreElements();
    E nextElement();
}
public interface Iterator<E> {
    boolean hasNext();
    E next();
    void remove();
}
```
- 函数接口不同
- Enumeration速度快，占用内存少，但是不是快速失败的，线程不安全。
- Iterator允许删除底层数据，枚举不允许
- Iterator安全性高，因为其他线程不能够修改正在被Iterator遍历的集合里面的对象。

### 33. 我们如何对一组对象进行排序？
可以用 Collections.sort（）+ Comparator.comparing（），因为对对象排序，实际上是对对象的属性排序哈~

```
public class Student {

    private String name;
    private int score;

    public Student(String name, int score){
        this.name = name;
        this.score = score;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getScore() {
        return score;
    }

    public void setScore(int score) {
        this.score = score;
    }

    @Override
    public String toString() {
        return "Student: " + this.name + " 分数：" + Integer.toString( this.score );
    }
}

public class Test {

    public static void main(String[] args) {

        List<Student> studentList = new ArrayList<>();
        studentList.add(new Student("D", 90));
        studentList.add(new Student("C", 100));
        studentList.add(new Student("B", 95));
        studentList.add(new Student("A", 95));

        Collections.sort(studentList, Comparator.comparing(Student::getScore).reversed().thenComparing(Student::getName));
        studentList.stream().forEach(p -> System.out.println(p.toString()));
    }
}
```
### 34. 当一个集合被作为参数传递给一个函数时，如何才可以确保函数不能修改它？
这个跟之前那个不可变集合一样道理哈~

> 在作为参数传递之前，使用Collections.unmodifiableCollection(Collection c)方法创建一个只读集合，这将确保改变集合的任何操作都会抛出UnsupportedOperationException。


### 35. 说一下HashSet的实现原理？
-  不能保证元素的排列顺序，顺序有可能发生变化。
-  元素可以为null
-  hashset保证元素不重复~ （这个面试官很可能会问什么原理，这个跟HashMap有关的哦）
-  HashSet，需要谈谈它俩hashcode()和equles()哦~
- 实际是基于HashMap实现的，HashSet 底层使用HashMap来保存所有元素的

看看它的add方法吧~
```
    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
```

### 36. Array 和 ArrayList 有何区别？
- 定义一个 Array 时，必须指定数组的数据类型及数组长度，即数组中存放的元素个数固定并且类型相同。
- ArrayList 是动态数组，长度动态可变，会自动扩容。不使用泛型的时候，可以添加不同类型元素。

### 37. 为什么HashMap中String、Integer这样的包装类适合作为key？
String、Integer等包装类的特性能够保证Hash值的不可更改性和计算准确性，能够有效的减少Hash碰撞的几率~

因为
- 它们都是final修饰的类，不可变性，保证key的不可更改性，不会存在获取hash值不同的情况~
- 它们内部已重写了equals()、hashCode()等方法，遵守了HashMap内部的规范


### 38. 如果想用Object作为hashMap的Key？；
重写hashCode()和equals()方法啦~ (这个答案来自互联网哈~)
> - 重写hashCode()是因为需要计算存储数据的存储位置，需要注意不要试图从散列码计算中排除掉一个对象的关键部分来提高性能，这样虽然能更快但可能会导致更多的Hash碰撞；
> - 重写equals()方法，需要遵守自反性、对称性、传递性、一致性以及对于任何非null的引用值x，x.equals(null)必须返回false的这几个特性，目的是为了保证key在哈希表中的唯一性；

### 39. 讲讲红黑树的特点？

- 每个节点或者是黑色，或者是红色。
- 根节点是黑色。
- 每个叶子节点（NIL）是黑色。 [注意：这里叶子节点，是指为空(NIL或NULL)的叶子节点！]
- 如果一个节点是红色的，则它的子节点必须是黑色的。
- 从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。

### 40. Java集合类框架的最佳实践有哪些？
其实这些点，结合平时工作，代码总结讲出来，更容易吸引到面试官呢 (这个答案来自互联网哈~)
> - 1.根据应用需要正确选择要使用的集合类型对性能非常重要，比如：假如知道元素的大小是固定的，那么选用Array类型而不是ArrayList类型更为合适。
> - 2.有些集合类型允许指定初始容量。因此，如果我们能估计出存储的元素的数目，我们可以指定初始容量来避免重新计算hash值或者扩容等。
> - 3.为了类型安全、可读性和健壮性等原因总是要使用泛型。同时，使用泛型还可以避免运行时的ClassCastException。
> - 4.使用JDK提供的不变类(immutable class)作为Map的键可以避免为我们自己的类实现hashCode()和equals()方法。
> - 5.编程的时候接口优于实现
> - 6.底层的集合实际上是空的情况下，返回为长度是0的集合或数组而不是null。


### 41.谈谈线程池阻塞队列吧~
- ArrayBlockingQueue
- LinkedBlockingQueue
- DelayQueue
- PriorityBlockingQueue
- SynchronousQueue

**ArrayBlockingQueue：** （有界队列）是一个用数组实现的有界阻塞队列，按FIFO排序量。

**LinkedBlockingQueue：** （可设置容量队列）基于链表结构的阻塞队列，按FIFO排序任务，容量可以选择进行设置，不设置的话，将是一个无边界的阻塞队列，最大长度为Integer.MAX_VALUE，吞吐量通常要高于ArrayBlockingQuene；newFixedThreadPool线程池使用了这个队列

**DelayQueue：**（延迟队列）是一个任务定时周期的延迟执行的队列。根据指定的执行时间从小到大排序，否则根据插入到队列的先后排序。newScheduledThreadPool线程池使用了这个队列。

**PriorityBlockingQueue：**（优先级队列）是具有优先级的无界阻塞队列；

**SynchronousQueue：**（同步队列）一个不存储元素的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQuene，newCachedThreadPool线程池使用了这个队列。
针对面试题：线程池都有哪几种工作队列？

我觉得，回答以上几种ArrayBlockingQueue，LinkedBlockingQueue，SynchronousQueue等，说出它们的特点，并结合使用到对应队列的常用线程池(如newFixedThreadPool线程池使用LinkedBlockingQueue)，进行展开阐述， 就可以啦。

有兴趣的朋友，可以看看我的这篇文章哦~

[面试必备：Java线程池解析](https://juejin.im/post/5d1882b1f265da1ba84aa676#heading-15)

### 42. HashSet和TreeSet有什么区别？
- Hashset 的底层是由哈希表实现的，Treeset 底层是由红黑树实现的。
- HashSet中的元素没有顺序，TreeSet保存的元素有顺序性（实现Comparable接口）
- HashSet的add()，remove()，contains()方法的时间复杂度是O(1);TreeSet中，add()，remove()，contains()方法的时间复杂度是O(logn)


### 43. Set里的元素是不能重复的，那么用什么方法来区分重复与否呢? 是用==还是equals()?
元素重复与否是使用equals()方法进行判断的，这个可以跟面试官说说==和equals()的区别，hashcode()和equals

### 44. 说出ArrayList,LinkedList的存储性能和特性
这道面试题，跟ArrayList,LinkedList，就是换汤不换药的~
- ArrayList,使用数组方式存储数据，查询时，ArrayList是基于索引(index)的数据结构，可以直接映射到，速度较快；但是插入数据需要移动数据，效率就比LinkedList慢一点~
- LinkedList,使用双向链表实现存储,按索引数据需要进行前向或后向遍历，查询相对ArrayList慢一点；但是插入数据速度较快。
- LinkedList比ArrayList开销更大，因为LinkedList的节点除了存储数据，还需要存储引用。

### 45. HashMap在JDK1.7和JDK1.8中有哪些不同？
互联网上这个答案太详细啦（来源[Java集合必会14问](https://www.jianshu.com/p/939b8a672070)）
![](https://user-gold-cdn.xitu.io/2020/6/12/172a5cecccd07908?w=975&h=583&f=png&s=154020)

### 46. ArrayList集合加入1万条数据，应该怎么提高效率

> 因为ArrayList的底层是数组实现,并且数组的默认值是10,如果插入10000条要不断的扩容,耗费时间,所以我们调用ArrayList的指定容量的构造器方法ArrayList(int size) 就可以实现不扩容,就提高了性能。

### 47. 如何对Object的list排序
看例子吧，哈哈，这个跟对象排序也是一样的呢~
```
public class Person {

    private String name;
    private Integer age;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public Integer getAge() {
        return age;
    }
    public void setAge(Integer age) {
        this.age = age;
    }
    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }
}

public class Test {

    public static void main(String[] args) {

        List<Person> list = new ArrayList<>();
        list.add(new Person("jay", 18));
        list.add(new Person("tianLuo", 10));

        list.stream().forEach(p -> System.out.println(p.getName()+" "+p.getAge()));
        // 用comparing比较对象属性
        list.sort(Comparator.comparing(Person::getAge));

        System.out.println("排序后");

        list.stream().forEach(p -> System.out.print(p.getName()+" "+p.getAge()+" "));
    }
}
```


### 48. ArrayList 和 HashMap 的默认大小是多数？

在 Java 7 中，ArrayList 的默认大小是 10 个元素，HashMap 的默认大小是16个元素（必须是2的幂）。

### 49. 有没有有顺序的Map实现类，如果有，他们是怎么保证有序的
- Hashmap和Hashtable 都不是有序的。
- TreeMap和LinkedHashmap都是有序的。（TreeMap默认是key升序，LinkedHashmap默认是数据插入顺序）
- TreeMap是基于比较器Comparator来实现有序的。
- LinkedHashmap是基于链表来实现数据插入有序的。

### 50. HashMap是怎么解决哈希冲突的

> Hashmap解决hash冲突，使用的是链地址法，即数组+链表的形式来解决。put执行首先判断table[i]位置，如果为空就直接插入，不为空判断和当前值是否相等，相等就覆盖，如果不相等的话，判断是否是红黑树节点，如果不是，就从table[i]位置开始遍历链表，相等覆盖，不相等插入。


### 个人公众号

![](https://user-gold-cdn.xitu.io/2019/7/28/16c381c89b127bbb?w=344&h=344&f=jpeg&s=8943)

- 如果你是个爱学习的好孩子，可以关注我公众号，一起学习讨论。
- 如果你觉得本文有哪些不正确的地方，可以评论，也可以关注我公众号，私聊我，大家一起学习进步哈。

