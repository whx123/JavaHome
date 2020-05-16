### 前言
最近几天看了几篇有关于Java Map的外国博文，写得非常不错，所以整理了Java map 应该掌握的8个问题，都是日常开发司空见惯的问题，希望对大家有帮助；如果有不正确的地方，欢迎提出，万分感谢哈~

> [本章节所有代码demo已上传github](https://github.com/whx123/MapTest)

### 1、如何把一个Map转化为List
日常开发中，我们经常遇到这种场景，把一个Map转化为List。map转List有以下三种转化方式：
- 把map的键key转化为list
- 把map的值value转化为list
- 把map的键值key-value转化为list

**伪代码如下：**
```
// key list
List keyList = new ArrayList(map.keySet());
// value list
List valueList = new ArrayList(map.values());
// key-value list
List entryList = new ArrayList(map.entrySet());
```
**示例代码：**
```
public class Test {
    public static void main(String[] args) {
        Map<Integer, String> map = new HashMap<>();
        map.put(2, "jay");
        map.put(1, "whx");
        map.put(3, "huaxiao");
        //把一个map的键转化为list
        List<Integer> keyList = new ArrayList<>(map.keySet());
        System.out.println(keyList);
        //把map的值转化为list
        List<String> valueList = new ArrayList<>(map.values());
        System.out.println(valueList);
        把map的键值转化为list
        List entryList = new ArrayList(map.entrySet());
        System.out.println(entryList);

    }
}
```
运行结果：

```
[1, 2, 3]
[whx, jay, huaxiao]
[1=whx, 2=jay, 3=huaxiao]
```

### 2、如何遍历一个Map
我们经常需要遍历一个map，可以有以下两种方式实现：

#### 通过entrySet+for实现遍历
```
for(Entry entry: map.entrySet()) {
  // get key
  K key = entry.getKey();
  // get value
  V value = entry.getValue();
}
```
**实例代码：**

```
public class EntryMapTest {
    public static void main(String[] args) {
        Map<Integer, String> map = new HashMap<>();
        map.put(2, "jay");
        map.put(1, "whx");
        map.put(3, "huaxiao");

        for(Map.Entry entry: map.entrySet()) {
            // get key
            Integer key = (Integer) entry.getKey();
            // get value
            String value = (String) entry.getValue();

            System.out.println("key:"+key+",value:"+value);
        }
    }
}
```
#### 通过Iterator+while实现遍历
```
Iterator itr = map.entrySet().iterator();
while(itr.hasNext()) {
  Entry entry = itr.next();
  // get key
  K key = entry.getKey();
  // get value
  V value = entry.getValue();
}
```
**实例代码：**

```
public class IteratorMapTest {
    public static void main(String[] args) {
        Map<Integer, String> map = new HashMap<>();
        map.put(2, "jay");
        map.put(1, "whx");
        map.put(3, "huaxiao");

        Iterator itr = map.entrySet().iterator();
        while(itr.hasNext()) {
            Map.Entry entry = (Map.Entry) itr.next();
            // get key
            Integer key = (Integer) entry.getKey();
            // get value
            String value = (String) entry.getValue();

            System.out.println("key:"+key+",value:"+value);
        }
    }
}
```
**运行结果：**

```
key:1,value:whx
key:2,value:jay
key:3,value:huaxiao
```
### 3、如何根据Map的keys进行排序
对Map的keys进行排序，在日常开发很常见，主要有以下两种方式实现。
#### 把Map.Entry放进list，再用Comparator对list进行排序
```
List list = new ArrayList(map.entrySet());
Collections.sort(list, (Entry e1, Entry e2)-> {
    return e1.getKey().compareTo(e2.getKey());
});
```
**实例代码：**
```
public class SortKeysMapTest {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("2010", "jay");
        map.put("1999", "whx");
        map.put("3010", "huaxiao");

        List<Map.Entry<String,String>> list = new ArrayList<>(map.entrySet());
        Collections.sort(list, (Map.Entry e1, Map.Entry e2)-> {
                return e1.getKey().toString().compareTo(e2.getKey().toString());
        });

        for (Map.Entry entry : list) {
            System.out.println("key:" + entry.getKey() + ",value:" + entry.getValue());
        }

    }
}
```

#### 使用SortedMap+TreeMap+Comparator实现
```
SortedMap sortedMap = new TreeMap(new Comparator() {
  @Override
  public int compare(K k1, K k2) {
    return k1.compareTo(k2);
  }
});
sortedMap.putAll(map);
```
**实例代码：**

```
public class SortKeys2MapTest {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("2010", "jay");
        map.put("1999", "whx");
        map.put("3010", "huaxiao");

        SortedMap sortedMap = new TreeMap(new Comparator<String>() {
            @Override
            public int compare(String k1, String k2) {
                return k1.compareTo(k2);
            }
        });
        sortedMap.putAll(map);

        Iterator itr = sortedMap.entrySet().iterator();
        while(itr.hasNext()) {
            Map.Entry entry = (Map.Entry) itr.next();
            // get key
            String key = (String) entry.getKey();
            // get value
            String value = (String) entry.getValue();

            System.out.println("key:"+key+",value:"+value);
        }
    }
}

```
**运行结果：**

```
key:1999,value:whx
key:2010,value:jay
key:3010,value:huaxiao
```

### 4、如何对Map的values进行排序

```
List list = new ArrayList(map.entrySet());
Collections.sort(list, (Entry e1, Entry e2) ->{
    return e1.getValue().compareTo(e2.getValue());
  });
```

**实例代码：**
```
public class SortValuesMapTest {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("2010", "jay");
        map.put("1999", "whx");
        map.put("3010", "huaxiao");

        List <Map.Entry<String,String>>list = new ArrayList<>(map.entrySet());
        Collections.sort(list, (Map.Entry e1, Map.Entry e2)-> {
                return e1.getValue().toString().compareTo(e2.getValue().toString());
            }
        );

        for (Map.Entry entry : list) {
            System.out.println("key:" + entry.getKey() + ",value:" + entry.getValue());
        }
    }
}
```
运行结果：

```
key:3010,value:huaxiao
key:2010,value:jay
key:1999,value:whx
```

### 5、如何初始化一个静态/不可变的Map
初始化一个静态不可变的map，单单static final+static代码还是不行的，如下：
```
public class Test1 {
    private static final Map <Integer,String>map;
    static {
        map = new HashMap<Integer, String>();
        map.put(1, "one");
        map.put(2, "two");
    }
    public static void main(String[] args) {
        map.put(3, "three");
        Iterator itr = map.entrySet().iterator();
        while(itr.hasNext()) {
            Map.Entry entry = (Map.Entry) itr.next();
            // get key
            Integer key = (Integer) entry.getKey();
            // get value
            String value = (String) entry.getValue();

            System.out.println("key:"+key+",value:"+value);
        }
    }
}
```
这里面，map继续添加元素(3,"three")，发现是OK的，运行结果如下：
```
key:1,value:one
key:2,value:two
key:3,value:three
```
真正实现一个静态不可变的map，需要Collections.unmodifiableMap，代码如下：

```
public class Test2 {
    private static final Map<Integer, String> map;
    static {
        Map<Integer,String> aMap = new HashMap<>();
        aMap.put(1, "one");
        aMap.put(2, "two");
        map = Collections.unmodifiableMap(aMap);
    }

    public static void main(String[] args) {
        map.put(3, "3");
        Iterator itr = map.entrySet().iterator();
        while(itr.hasNext()) {
            Map.Entry entry = (Map.Entry) itr.next();
            // get key
            Integer key = (Integer) entry.getKey();
            // get value
            String value = (String) entry.getValue();

            System.out.println("key:"+key+",value:"+value);
        }
    }

}
```
运行结果如下：

![](https://user-gold-cdn.xitu.io/2020/2/3/1700b88b92e849c2?w=901&h=255&f=png&s=30907)

可以发现，继续往map添加元素是会报错的，实现真正不可变的map。

### 6、HashMap, TreeMap, and Hashtable，ConcurrentHashMap的区别
|        | HashMap| TreeMap | Hashtable| ConcurrentHashMap |
| ------ | ------ | ------  |-------- |----------- |
| 有序性 | 否 | 是 | 否| 否 |
| null k-v | 是-是 | 否-是|否-否|否-否|
| 线性安全 | 否 | 否 |是 | 是 |
| 时间复杂度 | O（1） | O（log n） | O(1) | O（log n）|
| 底层结构 | 数组+链表+红黑树 |  红黑树 |数组+链表| 数组+链表+红黑树|


### 7、如何创建一个空map
如果map是不可变的，可以这样创建：

```
Map map=Collections.emptyMap()；
or
Map<String,String> map=Collections.<String, String>emptyMap();
//map1.put("1", "1"); 运行出错
```
如果你希望你的空map可以添加元素的，可以这样创建

```
Map map = new HashMap();
```

### 8、有关于map的复制
有关于hashmap的复制，在日常开发中，使用也比较多。主要有```=，clone，putAll```，但是他们都是浅复制，使用的时候注意啦，可以看一下以下例子：

**例子一，使用=复制一个map：**
```
public class CopyMapAssignTest {
    public static void main(String[] args) {

        Map<Integer, User> userMap = new HashMap<>();

        userMap.put(1, new User("jay", 26));
        userMap.put(2, new User("fany", 25));

        //Shallow clone
        Map<Integer, User> clonedMap = userMap;

        //Same as userMap
        System.out.println(clonedMap);

        System.out.println("\nChanges reflect in both maps \n");

        //Change a value is clonedMap
        clonedMap.get(1).setName("test");

        //Verify content of both maps
        System.out.println(userMap);
        System.out.println(clonedMap);
    }
}
```
运行结果：

```
{1=User{name='jay', age=26}, 2=User{name='fany', age=25}}

Changes reflect in both maps 

{1=User{name='test', age=26}, 2=User{name='fany', age=25}}
{1=User{name='test', age=26}, 2=User{name='fany', age=25}}
```
从运行结果看出，对cloneMap修改，两个map都改变了，所以=是浅复制。

**例子二，使用hashmap的clone复制：**

```
public class CopyCloneMapTest {
    public static void main(String[] args) {
        HashMap<Integer, User> userMap = new HashMap<>();

        userMap.put(1, new User("jay", 26));
        userMap.put(2, new User("fany", 25));

        //Shallow clone
        HashMap<Integer, User> clonedMap = (HashMap<Integer, User>) userMap.clone();

        //Same as userMap
        System.out.println(clonedMap);

        System.out.println("\nChanges reflect in both maps \n");

        //Change a value is clonedMap
        clonedMap.get(1).setName("test");

        //Verify content of both maps
        System.out.println(userMap);
        System.out.println(clonedMap);
    }
}

```
运行结果：

```
{1=User{name='jay', age=26}, 2=User{name='fany', age=25}}

Changes reflect in both maps 

{1=User{name='test', age=26}, 2=User{name='fany', age=25}}
{1=User{name='test', age=26}, 2=User{name='fany', age=25}}
```
从运行结果看出，对cloneMap修改，两个map都改变了，所以hashmap的clone也是浅复制。

**例子三，通过putAll操作**

```
public class CopyPutAllMapTest {
    public static void main(String[] args) {
        HashMap<Integer, User> userMap = new HashMap<>();

        userMap.put(1, new User("jay", 26));
        userMap.put(2, new User("fany", 25));

        //Shallow clone
        HashMap<Integer, User> clonedMap = new HashMap<>();
        clonedMap.putAll(userMap);

        //Same as userMap
        System.out.println(clonedMap);

        System.out.println("\nChanges reflect in both maps \n");

        //Change a value is clonedMap
        clonedMap.get(1).setName("test");

        //Verify content of both maps
        System.out.println(userMap);
        System.out.println(clonedMap);
    }
}

```

运行结果：

```
{1=User{name='jay', age=26}, 2=User{name='fany', age=25}}

Changes reflect in both maps 

{1=User{name='test', age=26}, 2=User{name='fany', age=25}}
{1=User{name='test', age=26}, 2=User{name='fany', age=25}}
```
从运行结果看出，对cloneMap修改，两个map都改变了，所以putAll还是浅复制。

**那么，如何实现深度复制呢？**

可以使用序列化实现，如下为谷歌Gson序列化HashMap，实现深度复制的例子：

```
public class CopyDeepMapTest {

    public static void main(String[] args) {
        HashMap<Integer, User> userMap = new HashMap<>();

        userMap.put(1, new User("jay", 26));
        userMap.put(2, new User("fany", 25));

        //Shallow clone
        Gson gson = new Gson();
        String jsonString = gson.toJson(userMap);

        Type type = new TypeToken<HashMap<Integer, User>>(){}.getType();
        HashMap<Integer, User> clonedMap = gson.fromJson(jsonString, type);

        //Same as userMap
        System.out.println(clonedMap);

        System.out.println("\nChanges DO NOT reflect in other map \n");

        //Change a value is clonedMap
        clonedMap.get(1).setName("test");

        //Verify content of both maps
        System.out.println(userMap);
        System.out.println(clonedMap);
    }
}

```
运行结果：

```
{1=User{name='jay', age=26}, 2=User{name='fany', age=25}}

Changes DO NOT reflect in other map 

{1=User{name='jay', age=26}, 2=User{name='fany', age=25}}
{1=User{name='test', age=26}, 2=User{name='fany', age=25}}
```
从运行结果看出，对cloneMap修改，userMap没有被改变，所以是深度复制。

### 参考与感谢
- [Top 9 questions about Java Maps](https://www.programcreek.com/2013/09/top-9-questions-for-java-map/)
- [Best way to create an empty map in Java](https://stackoverflow.com/questions/636126/best-way-to-create-an-empty-map-in-java)
- [How to clone HashMap – Shallow and Deep Copy](https://howtodoinjava.com/java/collections/hashmap/shallow-deep-copy-hashmap/)

### 个人公众号

![](https://user-gold-cdn.xitu.io/2019/7/28/16c381c89b127bbb?w=344&h=344&f=jpeg&s=8943)

- 如果你是个爱学习的好孩子，可以关注我公众号，一起学习讨论。
- 如果你觉得本文有哪些不正确的地方，可以评论，也可以关注我公众号，私聊我，大家一起学习进步哈。