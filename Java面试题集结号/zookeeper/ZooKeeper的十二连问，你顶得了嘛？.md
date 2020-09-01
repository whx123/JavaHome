## 前言
一线大厂ZooKeeper的十二连问，你顶得了嘛？

本文已经收录到github
> https://github.com/whx123/JavaHome

### 1. 面试官：工作中使用过Zookeeper嘛？你知道它是什么，有什么用途呢？

**小菜鸡的我：** 

- 有使用过的，使用ZooKeeper作为**dubbo的注册中心**，使用ZooKeeper实现**分布式锁**。
- ZooKeeper，它是一个开放源码的**分布式协调服务**，它是一个集群的管理者，它将简单易用的接口提供给用户。
- 可以基于Zookeeper 实现诸如数据发布/订阅、负载均衡、命名服务、分布式协调/通知、集群管理、Master 选举、分布式锁和分布式队列**等功能**。
- Zookeeper的**用途**：命名服务、配置管理、集群管理、分布式锁、队列管理

用途跟功能不是一个意思咩？给我一个眼神，让我自己体会
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fa22060efba341a391c1ef9ead212e95~tplv-k3u1fbpfcp-zoom-1.image)

### 2. 面试官：说下什么是命名服务，什么是配置管理，又什么是集群管理吧

**小菜鸡的我(幸好我刷过面试题)，无所畏惧** 
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/49f59ab796684aed93b0c6f1b6cade91~tplv-k3u1fbpfcp-zoom-1.image)
 - **命名服务就是**： 
 > 命名服务是指通过**指定的名字**来获取资源或者服务地址。Zookeeper可以创建一个**全局唯一的路径**，这个路径就可以作为一个名字。被命名的实体可以是**集群中的机器，服务的地址，或者是远程的对象**等。一些分布式服务框架（RPC、RMI）中的服务地址列表，通过使用命名服务，客户端应用能够根据特定的名字来获取资源的实体、服务地址和提供者信息等。
 
 - **配置管理：** ：
 > 实际项目开发中，我们经常使用.properties或者xml需要配置很多信息，如数据库连接信息、fps地址端口等等。因为你的程序一般是分布式部署在不同的机器上（如果你是单机应用当我没说），如果把程序的这些配置信息**保存在zk的znode节点**下，当你要修改配置，即znode会发生变化时，可以通过改变zk中某个目录节点的内容，利用**watcher通知给各个客户端**，从而更改配置。
 
-  **集群管理**
> 集群管理包括集群监控和集群控制，其实就是监控集群机器状态，剔除机器和加入机器。zookeeper可以方便集群机器的管理，它可以实时监控znode节点的变化，一旦发现有机器挂了，该机器就会与zk断开连接，对用的临时目录节点会被删除，其他所有机器都收到通知。新机器加入也是类似酱紫，所有机器收到通知：有新兄弟目录加入啦。


### 3. 面试官：你提到了znode节点，那你知道znode有几种类型呢？zookeeper的数据模型是怎样的呢？

**小菜鸡的我（我先想想）：** 

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fc8d387fe5764ecca82917190801bf66~tplv-k3u1fbpfcp-zoom-1.image)

#### zookeeper的数据模型
ZooKeeper的视图数据结构，很像Unix文件系统，也是树状的，这样可以确定每个路径都是唯一的。zookeeper的节点统一叫做**znode**，它是可以通过**路径来标识**，结构图如下：
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8d7058978e614f7f8ee4832cb3b9c7cb~tplv-k3u1fbpfcp-zoom-1.image)

#### znode的4种类型
根据节点的生命周期，znode可以分为4种类型，分别是持久节点（PERSISTENT）、持久顺序节点（PERSISTENT_SEQUENTIAL）、临时节点（EPHEMERAL）、临时顺序节点（EPHEMERAL_SEQUENTIAL）

- 持久节点（PERSISTENT）
> 这类节点被创建后，就会一直存在于Zk服务器上。直到手动删除。

- 持久顺序节点（PERSISTENT_SEQUENTIAL）
> 它的基本特性同持久节点，不同在于增加了顺序性。父节点会维护一个自增整性数字，用于子节点的创建的先后顺序。

- 临时节点（EPHEMERAL）
> 临时节点的生命周期与客户端的会话绑定，一旦客户端会话失效（非TCP连接断开），那么这个节点就会被自动清理掉。zk规定临时节点只能作为叶子节点。

- 临时顺序节点（EPHEMERAL_SEQUENTIAL）
> 基本特性同临时节点，添加了顺序的特性。

### 4、面试官：你知道znode节点里面存储的是什么吗？每个节点的数据最大不能超过多少呢？
**小菜鸡的我：**
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/92a4f38edb734cd195a2cae0e00a1d7a~tplv-k3u1fbpfcp-zoom-1.image)
#### znode节点里面存储的是什么？

Znode数据节点的代码如下
```
public class DataNode implements Record {
    byte data[];                    
    Long acl;                       
    public StatPersisted stat;       
    private Set<String> children = null; 
}
```
哈哈，Znode包含了**存储数据、访问权限、子节点引用、节点状态信息**，如图：
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/91b23e62a6424ae2848a050556ed34df~tplv-k3u1fbpfcp-zoom-1.image)

- **data:**  znode存储的业务数据信息
- **ACL:** 记录客户端对znode节点的访问权限，如IP等。
- **child:** 当前节点的子节点引用
- **stat:** 包含Znode节点的状态信息，比如**事务id、版本号、时间戳**等等。


#### 每个节点的数据最大不能超过多少呢

为了保证高吞吐和低延迟，以及数据的一致性，znode只适合存储非常小的数据，不能超过1M，最好都小于1K。


### 5、面试官：你知道znode节点上的监听机制嘛？讲下Zookeeper watch机制吧。
**小菜鸡的我：**

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fd8dc23431b24ec38fcd5b142d4ff453~tplv-k3u1fbpfcp-zoom-1.image)
- Watcher机制
- 监听机制的工作原理
- Watcher特性总结

#### Watcher监听机制
Zookeeper 允许客户端向服务端的某个Znode注册一个Watcher监听，当服务端的一些指定事件触发了这个Watcher，服务端会向指定客户端发送一个事件通知来实现分布式的通知功能，然后客户端根据 Watcher通知状态和事件类型做出业务上的改变。
> 可以把Watcher理解成客户端注册在某个Znode上的触发器，当这个Znode节点发生变化时（增删改查），就会触发Znode对应的注册事件，注册的客户端就会收到异步通知，然后做出业务的改变。

#### Watcher监听机制的工作原理
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9477e0dada834eee9a997313a14a963b~tplv-k3u1fbpfcp-zoom-1.image)

- ZooKeeper的Watcher机制主要包括客户端线程、客户端 WatcherManager、Zookeeper服务器三部分。
- 客户端向ZooKeeper服务器注册Watcher的同时，会将Watcher对象存储在客户端的WatchManager中。
- 当zookeeper服务器触发watcher事件后，会向客户端发送通知， 客户端线程从 WatcherManager 中取出对应的 Watcher 对象来执行回调逻辑。

#### Watcher特性总结
- **一次性：**一个Watch事件是一个一次性的触发器。一次性触发，客户端只会收到一次这样的信息。
- **异步的:  **  Zookeeper服务器发送watcher的通知事件到客户端是异步的，不能期望能够监控到节点每次的变化，Zookeeper只能保证最终的一致性，而无法保证强一致性。
- **轻量级：** Watcher 通知非常简单，它只是通知发生了事件，而不会传递事件对象内容。
- **客户端串行：** 执行客户端 Watcher 回调的过程是一个串行同步的过程。
- 注册 watcher用getData、exists、getChildren方法
- 触发 watcher用create、delete、setData方法


### 6、面试官：你对Zookeeper的数据结构都有一定了解，那你讲下Zookeeper的特性吧
**小菜鸡的我：（我背过书，啊哈哈）**
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/975294fd4807485e87adcb50f37fdc26~tplv-k3u1fbpfcp-zoom-1.image)
Zookeeper 保证了如下分布式一致性特性：
- **顺序一致性**：从同一客户端发起的事务请求，最终将会严格地按照顺序被应用到 ZooKeeper 中去。
- **原子性**：所有事务请求的处理结果在整个集群中所有机器上的应用情况是一致的，也就是说，要么整个集群中所有的机器都成功应用了某一个事务，要么都没有应用。
- **单一视图**：无论客户端连到哪一个 ZooKeeper 服务器上，其看到的服务端数据模型都是一致的。
- **可靠性：** 一旦服务端成功地应用了一个事务，并完成对客户端的响应，那么该事务所引起的服务端状态变更将会被一直保留下来。
- **实时性（最终一致性）：** Zookeeper 仅仅能保证在一定的时间段内，客户端最终一定能够从服务端上读取到最新的数据状态。

### 7、面试官：你刚提到顺序一致性，那zookeeper是如何保证事务的顺序一致性的呢？
**小菜鸡的我：（完蛋了这题不会）**
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cb1eac96fd474f8e8e2317b6bf8b0641~tplv-k3u1fbpfcp-zoom-1.image)

这道题可以看下这篇文章（本题答案来自该文章）：[聊一聊ZooKeeper的顺序一致性](https://time.geekbang.org/column/article/239261)
> 需要了解事务ID，即zxid。ZooKeeper的在选举时通过比较各结点的zxid和机器ID选出新的主结点的。zxid由Leader节点生成，有新写入事件时，Leader生成新zxid并随提案一起广播，每个结点本地都保存了当前最近一次事务的zxid，zxid是递增的，所以谁的zxid越大，就表示谁的数据是最新的。

ZXID的生成规则如下：
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ea46600cd60d462ca982c60fa854419b~tplv-k3u1fbpfcp-zoom-1.image)

ZXID有两部分组成：

- 任期：完成本次选举后，直到下次选举前，由同一Leader负责协调写入；
- 事务计数器：单调递增，每生效一次写入，计数器加一。

> ZXID的低32位是计数器，所以同一任期内，ZXID是连续的，每个结点又都保存着自身最新生效的ZXID，通过对比新提案的ZXID与自身最新ZXID是否相差“1”，来保证事务严格按照顺序生效的。

### 8、面试官：你提到了Leader，你知道Zookeeper的服务器有几种角色嘛？Zookeeper下Server工作状态又有几种呢？

**小菜鸡的我：**
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5b965992267c4fcbbaca532089548cad~tplv-k3u1fbpfcp-zoom-1.image)

#### Zookeeper 服务器角色
Zookeeper集群中，有Leader、Follower和Observer三种角色

**Leader**

> Leader服务器是整个ZooKeeper集群工作机制中的核心，其主要工作：
- 事务请求的唯一调度和处理者，保证集群事务处理的顺序性
- 集群内部各服务的调度者

**Follower**

> Follower服务器是ZooKeeper集群状态的跟随者，其主要工作：
- 处理客户端非事务请求，转发事务请求给Leader服务器
- 参与事务请求Proposal的投票
- 参与Leader选举投票

**Observer**
> Observer是3.3.0 版本开始引入的一个服务器角色，它充当一个观察者角色——观察ZooKeeper集群的最新状态变化并将这些状态变更同步过来。其工作：
- 处理客户端的非事务请求，转发事务请求给 Leader 服务器
- 不参与任何形式的投票


#### Zookeeper下Server工作状态
> 服务器具有四种状态，分别是 LOOKING、FOLLOWING、LEADING、OBSERVING。
- 1.LOOKING：寻找Leader状态。当服务器处于该状态时，它会认为当前集群中没有 Leader，因此需要进入 Leader 选举状态。
- 2.FOLLOWING：跟随者状态。表明当前服务器角色是Follower。
- 3.LEADING：领导者状态。表明当前服务器角色是Leader。
- 4.OBSERVING：观察者状态。表明当前服务器角色是Observer。


### 9、面试官：你说到服务器角色是基于ZooKeeper集群的，那你画一下ZooKeeper集群部署图吧？ZooKeeper是如何保证主从节点数据一致性的呢？
**小菜鸡的我：**
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2037f26385ef479281e004084f1cd8da~tplv-k3u1fbpfcp-zoom-1.image)
#### ZooKeeper集群部署图
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6cf565bd494b4262baee09cfadfee24d~tplv-k3u1fbpfcp-zoom-1.image)
ZooKeeper集群是一主多从的结构：
- 如果是写入数据，先写入主服务器（主节点），再通知从服务器。
- 如果是读取数据，既读主服务器的，也可以读从服务器的。

#### ZooKeeper如何保证主从节点数据一致性
我们知道集群是主从部署结构，要保证主从节点一致性问题，无非就是两个主要问题：
- **主服务器挂了，或者重启了**
- **主从服务器之间同步数据**~ 

Zookeeper是采用ZAB协议（Zookeeper Atomic Broadcast，Zookeeper原子广播协议）来保证主从节点数据一致性的，ZAB协议支持**崩溃恢复和消息广播**两种模式，很好解决了这两个问题：
- 崩溃恢复：Leader挂了，进入该模式，选一个新的leader出来
- 消息广播： 把更新的数据，从Leader同步到所有Follower

> Leader服务器挂了，所有集群中的服务器进入LOOKING状态，首先，它们会选举产生新的Leader服务器；接着，新的Leader服务器与集群中Follower服务进行数据同步，当集群中超过半数机器与该 Leader服务器完成数据同步之后，退出恢复模式进入消息广播模式。Leader 服务器开始接收客户端的事务请求生成事务Proposal进行事务请求处理。

### 10、面试官：Leader挂了，进入崩溃恢复，是如何选举Leader的呢？你讲一下ZooKeeper选举机制吧
**小菜鸡的我：**
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/de58c6ae02c04f008b6abad08178fcb9~tplv-k3u1fbpfcp-zoom-1.image)

服务器启动或者服务器运行期间（Leader挂了），都会进入Leader选举，我们来看一下~假设现在ZooKeeper集群有五台服务器，它们myid分别是服务器1、2、3、4、5，如图：
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1b578b765d4b4500a83d7ad63bb35e2f~tplv-k3u1fbpfcp-zoom-1.image)
#### 服务器启动的Leader选举

zookeeper集群初始化阶段，服务器（myid=1-5）**依次**启动，开始zookeeper选举Leader~
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5791e745136d4da18aa4254e7c116f23~tplv-k3u1fbpfcp-zoom-1.image)
- 服务器1（myid=1）启动，当前只有一台服务器，无法完成Leader选举
- 服务器2（myid=2）启动，此时两台服务器能够相互通讯，开始进入Leader选举阶段
> 1. 每个服务器发出一个投票
> > 服务器1 和 服务器2都将自己作为Leader服务器进行投票，投票的基本元素包括：服务器的myid和ZXID，我们以（myid，ZXID）形式表示。初始阶段，服务器1和服务器2都会投给自己，即服务器1的投票为（1,0），服务器2的投票为（2,0），然后各自将这个投票发给集群中的其他所有机器。
> 2. 接受来自各个服务器的投票
> > 每个服务器都会接受来自其他服务器的投票。同时，服务器会校验投票的有效性，是否本轮投票、是否来自LOOKING状态的服务器。
> 3. 处理投票
>> 收到其他服务器的投票，会将被人的投票跟自己的投票PK，PK规则如下：
>>> - 优先检查ZXID。ZXID比较大的服务器优先作为leader。
>>> - 如果ZXID相同的话，就比较myid，myid比较大的服务器作为leader。
>> 服务器1的投票是（1,0），它收到投票是（2,0），两者zxid都是0，因为收到的myid=2，大于自己的myid=1，所以它更新自己的投票为（2,0），然后重新将投票发出去。对于服务器2呢，即不再需要更新自己的投票，把上一次的投票信息发出即可。
> 4. 统计投票
>> 每次投票后，服务器会统计所有投票，判断是否有过半的机器接受到相同的投票信息。服务器2收到两票，少于3（n/2+1,n为总服务器），所以继续保持LOOKING状态
- 服务器3（myid=3）启动，继续进入Leader选举阶段
> 跟前面流程一致，服务器1和2先投自己一票，因为服务器3的myid最大，所以大家把票改投给它。此时，服务器为3票（大于等于n/2+1）,所以服务器3当选为Leader。 服务器1，2更改状态为FOLLOWING，服务器3更改状态为LEADING；
- 服务器4启动，发起一次选举。
> 此时服务器1，2，3已经不是LOOKING状态，不会更改选票信息。选票信息结果：服务器3为4票，服务器4为1票。服务器4并更改状态为FOLLOWING；
- 服务器5启动，发起一次选举。
> 同理，服务器也是把票投给服务器3，服务器5并更改状态为FOLLOWING；
- 投票结束，服务器3当选为Leader


#### 服务器运行期间的Leader选举

zookeeper集群的五台服务器（myid=1-5）正在运行中，突然某个瞬间，Leader服务器3挂了，这时候便开始Leader选举~
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c974c58e0d4842cf8c7ef59d9fb28af2~tplv-k3u1fbpfcp-zoom-1.image)

- 1.变更状态
> Leader 服务器挂了之后，余下的非Observer服务器都会把自己的服务器状态更改为LOOKING，然后开始进入Leader选举流程。
- 2.每个服务器发起投票
> 每个服务器都把票投给自己，因为是运行期间，所以每台服务器的ZXID可能不相同。假设服务1,2,4,5的zxid分别为333,666,999,888，则分别产生投票（1,333），（2，666），（4,999）和（5,888），然后各自将这个投票发给集群中的其他所有机器。
- 3.接受来自各个服务器的投票
- 4.处理投票
> 投票规则是跟Zookeeper集群启动期间一致的，优先检查ZXID，大的优先作为Leader，所以显然服务器zxid=999具有优先权。
- 5.统计投票
- 6.改变服务器状态

### 11、面试官： 你前面提到在项目中使用过Zookeeper的分布式锁，讲一下zk分布式锁的实现原理吧？
**小菜鸡的我：**
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84050ae342c040b69f657cabf2359a07~tplv-k3u1fbpfcp-zoom-1.image)
Zookeeper就是使用临时顺序节点特性实现分布式锁的。
- 获取锁过程 （创建临时节点，检查序号最小）
- 释放锁 （删除临时节点，监听通知）

#### 获取锁过程

- 当第一个客户端请求过来时，Zookeeper客户端会创建一个持久节点/locks。如果它（Client1）想获得锁，需要在locks节点下创建一个顺序节点lock1.如图
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/39e5ee84f901453fb894600c331693d6~tplv-k3u1fbpfcp-zoom-1.image)
- 接着，客户端Client1会查找locks下面的所有临时顺序子节点，判断自己的节点lock1是不是排序最小的那一个，如果是，则成功获得锁。
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e958178a196742b480a20dff85dfef4f~tplv-k3u1fbpfcp-zoom-1.image)
- 这时候如果又来一个客户端client2前来尝试获得锁，它会在locks下再创建一个临时节点lock2
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/34a3a0f7221b4de79e1ed5595d7c177c~tplv-k3u1fbpfcp-zoom-1.image)
- 客户端client2一样也会查找locks下面的所有临时顺序子节点，判断自己的节点lock2是不是最小的，此时，发现lock1才是最小的，于是获取锁失败。获取锁失败，它是不会甘心的，client2向它排序靠前的节点lock1注册Watcher事件，用来监听lock1是否存在，也就是说client2抢锁失败进入等待状态。
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a17880856e1546bba4f2571dbf8b3b38~tplv-k3u1fbpfcp-zoom-1.image)
- 此时，如果再来一个客户端Client3来尝试获取锁，它会在locks下再创建一个临时节点lock3
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/abda17df527b4dbe8faa7e3a3a9e859e~tplv-k3u1fbpfcp-zoom-1.image)
- 同样的，client3一样也会查找locks下面的所有临时顺序子节点，判断自己的节点lock3是不是最小的，发现自己不是最小的，就获取锁失败。它也是不会甘心的，它会向在它前面的节点lock2注册Watcher事件，以监听lock2节点是否存在。
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5a8725119723434daddca761b0d7be83~tplv-k3u1fbpfcp-zoom-1.image)

#### 释放锁

我们再来看看释放锁的流程，zookeeper的**客户端业务完成或者故障**，都会删除临时节点，释放锁。如果是任务完成，Client1会显式调用删除lock1的指令
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ecf2bec179f64ef79b180f66a327e634~tplv-k3u1fbpfcp-zoom-1.image)
如果是客户端故障了，根据临时节点得特性，lock1是会自动删除的
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e87703be60a3407e9e570c86bd4c180d~tplv-k3u1fbpfcp-zoom-1.image)
lock1节点被删除后，Client2可开心了，因为它一直监听着lock1。lock1节点删除，Client2立刻收到通知，也会查找locks下面的所有临时顺序子节点，发下lock2是最小，就获得锁。
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/be3c7146f2934dd69a598cdf974ab390~tplv-k3u1fbpfcp-zoom-1.image)

同理，Client2获得锁之后，Client3也对它虎视眈眈，啊哈哈~

### 12. 面试官：好的，最后一道题，你说说dubbo和Zookeeper的关系吧，为什么选择Zookeeper作为注册中心？
小菜鸡的我（答了这么多道题，不会还不给我过吧？）：
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fd81e6dd9b0a40f9811c98e3c8e3a424~tplv-k3u1fbpfcp-zoom-1.image)

dubbo的注册中心可以选Zookeeper，memcached，redis等。为什么选择Zookeeper，因为它的功能特性咯~
- 命名服务，服务提供者向Zookeeper指定节点写入url，完成服务发布。
- 负载均衡，注册中心的承载能力有限，而Zookeeper集群配合web应用很容易达到负载均衡。
- zk支持监听事件，特别适合发布/订阅的场景，dubbo的生产者和消费者就类似这场景。
- 数据模型简单，数据存在内存，可谓高性能
- Zookeeper其他特点都可以搬出来讲一下~


### 个人公众号
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e6b23c704fc94ca09207b779f953cce6~tplv-k3u1fbpfcp-zoom-1.image)

### 参考与感谢
- [zookeeper面试题](https://segmentfault.com/a/1190000014479433)
- [28道进阶必备ZooKeeper面试真题（建议收藏！）](https://zhuanlan.zhihu.com/p/102168788)
- [漫画：什么是ZooKeeper？](https://juejin.im/post/6844903608685707271)
- [聊一聊ZooKeeper的顺序一致性](https://time.geekbang.org/column/article/239261)
- [Zookeeper——一致性协议:Zab协议](https://www.jianshu.com/p/2bceacd60b8a)
- [Zookeeper的选举机制原理（图文深度讲解）](https://blog.csdn.net/wx1528159409/article/details/84622762)
- [漫画：如何用Zookeeper实现分布式锁？](https://mp.weixin.qq.com/s/u8QDlrDj3Rl1YjY4TyKMCA)
