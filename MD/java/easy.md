
# 大神总结面试题
https://github.com/yuanguangxin/LeetCode/blob/master/Rocket.md

https://github.com/yuanguangxin/LeetCode

https://zhuanlan.zhihu.com/p/106997736
# 计算机组成原理
https://blog.csdn.net/chen1083376511/article/details/81941318
# 网络编程
> TCP/IP 体系架构(网络通信协议)
1. 应用层  各种应用层协议 FTP HTTP  SMTP
2. 运输层 TCP  UDP
     - tcp: 面向连接的可靠的数据传输服务
     - udp: 提供无连接的，尽最大哦努力的数据传输服务（不保证数据传输的可靠性）
3. 网络层
   * ARP IPv4 IPv6...
4. 数据链路层/物理层
   * 以太网 无线网络
   
> 为什么使用RPC 而不是简单的Http
 RPC主要是基于TCP/IP 协议，然HTTP是基于http协议，我们都知道http是在传输层tcp之上的

> 网络通信
- 如何进行网络通信
   - 通信双方地址和端口号
   - 


## netty

# 数据结构与算法
## ArrayList
```
 /**
     * 默认初始化容量.
     */
    private static final int DEFAULT_CAPACITY = 10;


    //实际元素数据
    transient Object[] elementData; 

    //实际元素个数
    private int size;
```
> add 方法
```

 /**
     * Appends the specified element to the end of this list.
     * 将指定的元素追加到此列表的末尾
     * @param e element to be appended to this list 被添加到list的元素
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
		//确认list容量，如果不够，容量加1。注意：只加1，保证资源不被浪费
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        //将元素e放在size的位置上,并且size++
        elementData[size++] = e;
        return true;
    }
	//数组容量检查，不够时则进行扩容，只供类内部使用 
    private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
    private static int calculateCapacity(Object[] elementData, int minCapacity) {
		// 若elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA，则取minCapacity为DEFAULT_CAPACITY和参数minCapacity之间的最大值。DEFAULT_CAPACITY在此之前已经定义为默认的初始化容量是10。
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }
	//数组容量检查，不够时则进行扩容，只供类内部使用 
	// minCapacity 想要的最小容量
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;
        // overflow-conscious code
		//想要的最小容量>数组缓冲区当前长度
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);//扩容
    }

如果elementData元素是空的，就是第一次添加元素，minCapacity=size+1，其实就是等于1，空的数组没有长度就存放不了，所以就将minCapacity变成10


```

>grow() 扩容
```
 /**
     * Appends the specified element to the end of this list.
     * 将指定的元素追加到此列表的末尾
     * @param e element to be appended to this list 被添加到list的元素
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
		//确认list容量，如果不够，容量加1。注意：只加1，保证资源不被浪费
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        //将元素e放在size的位置上,并且size++
        elementData[size++] = e;
        return true;
    }
	//数组容量检查，不够时则进行扩容，只供类内部使用 
    private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
    private static int calculateCapacity(Object[] elementData, int minCapacity) {
		// 若elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA，则取minCapacity为DEFAULT_CAPACITY和参数minCapacity之间的最大值。DEFAULT_CAPACITY在此之前已经定义为默认的初始化容量是10。
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }
	//数组容量检查，不够时则进行扩容，只供类内部使用 
	// minCapacity 想要的最小容量
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;
        // overflow-conscious code
		//最小容量>数组缓冲区当前长度
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);//扩容
    }

```
```
add-->判断是否需要扩容-->进行扩容
```


> 什么时候扩容  
* 当数组数量大于当前容器数量的时候进行扩容 扩容1.5倍

>总结  
```
arrayList 最主要的还是在操作System.arraycopy(Object src, int srcPos, Object dest, int destPos, int length)

src - 源数组。 
srcPos - 源数组中的起始位置。 
dest - 目标数组。 
destPos - 目标数据中的起始位置。 
length - 要复制的数组元素的数量。 
该方法是用了native关键字，调用的为C++编写的底层函数，可见其为JDK中的底层函数。 
```
## LinkedList
```
Java中的LinkedList类实现了List接口和Deque接口，是一种链表类型的数据结构，支持高效的插入和删除操作，同时也实现了Deque接口，使得LinkedList类也具有队列的特性。LinkedList类的底层实现的数据结构是一个双端的链表。

LinkedList类中有一个内部私有类Node，这个类就代表双端链表的节点Node。这个类有三个属性，分别是前驱节点，本节点的值，后继结点。
源码中的实现是这样的。

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
    
注意这个节点的初始化方法，给定三个参数，分别前驱节点，本节点的值，后继结点。这个方法将在LinkedList的实现中多次调用。


//头节点    
transient Node<E> first;

//尾节点
transient Node<E> last;




```
> add(int index, E element) 
```
下面我们看add方法，这个方法就是最常用的，在指定下标插入一个节点。我们先来看下源码的实现，很简单
    public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }



  Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
    
我们看到node的实现并不是像我们想象的那样直接就线性从头查找，而是折半查找，有一个小优化，先判断index在前半段还是后半段，如果在前半段就从头开始找，如果在后半段就从后开始找，这样最坏情况也只要找一半就可以了。


```




## 并发编程
 线程池
 [线程池实现](https://blog.csdn.net/u013332124/article/details/79587436)
 
 
 ## Avro
   Avro是Hadoop的一个数据序列化系统，由Hadoop的创始人Doug Cutting（也是Lucene，Nutch等项目的创始人）开发，设计用于支持大批量数据交换的应用。
   

# ElasticSearch
## 短语匹配(Phrase Matching)
就像用于全文搜索的的match查询一样，当你希望寻找邻近的单词时，match_phrase查询可以帮你达到目的。
```
GET /my_index/my_type/_search
{
    "query": {
        "match_phrase": {
            "title": "quick brown fox"
        }
    }
}
```
精确短语(Exact-phrase)匹配也许太过于严格了。也许我们希望含有"quick brown fox"的文档也能够匹配"quick fox"查询，即使位置并不是完全相等的。

我们可以在短语匹配使用slop参数来引入一些灵活性：
```
GET /my_index/my_type/_search
{
    "query": {
        "match_phrase": {
            "title": {
                "query": "quick fox",
                "slop":  1
            }
        }
    }
}
````
slop参数告诉match_phrase查询词条能够相隔多远时仍然将文档视为匹配。相隔多远的意思是，你需要移动一个词条多少次来让查询和文档匹配？

我们以一个简单的例子来阐述这个概念。为了让查询quick fox能够匹配含有quick brown fox的文档，我们需要slop的值为1


## match
最简单的一个match例子：

查询和"我的宝马多少马力"这个查询语句匹配的文档。
```
{
  "query": {
    "match": {
        "content" : {
            "query" : "我的宝马多少马力"
        }
    }
  }
}
```
上面的查询匹配就会进行分词，比如"宝马多少马力"会被分词为"宝马 多少 马力", 所有有关"宝马 多少 马力", 那么所有包含这三个词中的一个或多个的文档就会被搜索出来。
并且根据lucene的评分机制(TF/IDF)来进行评分。

## multi_match
如果我们希望两个字段进行匹配，其中一个字段有这个文档就满足的话，使用multi_match
```
{
  "query": {
    "multi_match": {
        "query" : "我的宝马多少马力",
        "fields" : ["title", "content"]
    }
  }
}
```
但是multi_match就涉及到匹配评分的问题了。

我们希望完全匹配的文档占的评分比较高，则需要使用best_fields
```
{
  "query": {
    "multi_match": {
      "query": "我的宝马发动机多少",
      "type": "best_fields",
      "fields": [
        "tag",
        "content"
      ],
      "tie_breaker": 0.3
    }
  }
}
```
意思就是完全匹配"宝马 发动机"的文档评分会比较靠前，如果只匹配宝马的文档评分乘以0.3的系数

most_fields  
越多字段匹配的文档评分越高

cross_fields  
 希望这个词条的分词词汇是分配到不同字段中的，那么就使用cross_fields
## term
 term是代表完全匹配，即不进行分词器分析，文档中必须包含整个搜索的词汇
 ```
 {
  "query": {
    "term": {
      "content": "汽车保养"
    }
  }
}
 ```
 
## bool联合查询: must,should,must_not
如果我们想要请求"content中带宝马，但是tag中不带宝马"这样类似的需求，就需要用到bool联合查询。
联合查询就会使用到must,should,must_not三种关键词。

这三个可以这么理解

must: 文档必须完全匹配条件
should: should下面会带一个以上的条件，至少满足一个条件，这个文档就符合should
must_not: 文档必须不匹配条件
比如上面那个需求：
```
{
  "query": {
    "bool": {
      "must": {
        "term": {
          "content": "宝马"
        }
      },
      "must_not": {
        "term": {
          "tags": "宝马"
        }
      }
    }
  }
}
```

## query和filter的本质区别？
query  
关注点：此文档与此查询子句的匹配程度如何？  
filter  
此文档与此查询子句匹配吗?  
使用场景
*
*


# hashmap-JAVA8
https://www.infoq.cn/article/DBIPV1XRorIf7SdMW0A5
>  数据结构
- 数组
- 链表
- 红黑树

> 成员变量
```
static final int DEFAULT_INITIAL_CAPACITY = 16; //  默认的初始容量16
static final int MAXIMUM_CAPACITY = 1073741824; // 最大容量
static final float DEFAULT_LOAD_FACTOR = 0.75F; // 默认加载因子
static final int TREEIFY_THRESHOLD = 8;         // 链表转换红黑树的阈值
static final int UNTREEIFY_THRESHOLD = 6;       // 红黑树转换为链表的阈值
static final int MIN_TREEIFY_CAPACITY = 64;     // 红黑树结构的最小容量
transient HashMap.Node<K, V>[] table;
transient Set<Entry<K, V>> entrySet;
transient int size;
transient int modCount;//修改次数
int threshold;// HashMap的阈值，用于判断是否需要调整HashMap的容量（threshold = 容量*加载因子） 
```
> 算法
>>PUT 方法解析
* hash 算法  
* 获取下标位置

>问题
* resize 如何实现的   https://blog.csdn.net/qq32933432/article/details/86668385  
为什么经过rehash之后，元素的位置要么是在原位置，要么是在原位置加原数组长度的位置？  

HashMap的数组长度恒定为2的n次方，也就是说只会为16，32，64，128这种数。源码中有限制，也就是说即使你创建HashMap的时候是写的

index=(n-1) & hash  
那么就是对于初始长度为16的数组，扩容之后为32，对应的leng-1就是15，31，他们所对应的二进制为
>15：0000 0000 0000 0000 0000 0000 0000 1111  
31：0000 0000 0000 0000 0000 0000 0001 1111  

现在我们开始做假设，假设某个元素的hashcode为52：  
>52：0000 0000 0000 0000 0000 0000 0011 0100  

这个52与15运算做按位与运算的的结果是4，用二进制表示如下  

>4：0000 0000 0000 0000 0000 0000 0000 0100

这个52与31做按位与运算的的结果是20，用二进制表示如下  
>20：0000 0000 0000 0000 0000 0000 0001 0100  

二十不就是等于4+16吗，刚好是原数组的下标+原数组的长度

# MYSQL
## MVCC 
 在mysql中的MVCC是在INNODB中得到支持，innodb隐藏三个字段
 * 事务id DB_TRX_ID
 * 回滚指针 DB_ROLL_PTR
 * 隐藏的ID

  更新前建立undo log，根据各种策略读取时非阻塞就是MVCC，undo log中的行就是MVCC中的多版本，这个可能与我们所理解的MVCC有较大的出入，一般我们认为MVCC有下面几个特点：
每行数据都存在一个版本，每次数据更新时都更新该版本
修改时Copy出当前版本随意修改，个事务之间无干扰
保存时比较版本号，如果成功（commit），则覆盖原记录；失败则放弃copy（rollback）
就是每行都有版本号，保存时根据版本号决定是否成功，听起来含有乐观锁的味道。。。，而Innodb的实现方式是：
事务以排他锁的形式修改原始数据
把修改前的数据存放于undo log，通过回滚指针与主数据关联
修改成功（commit）啥都不做，失败则恢复undo log中的数据（rollback）

> 当前读、快照读  

1.MySQL的InnoDB存储引擎默认事务隔离级别是RR(可重复读), 是通过 "行排他锁+MVCC" 一起实现的, 不仅可以保证可重复读, 还可以部分防止幻读, 而非完全防止;

2.为什么是部分防止幻读, 而不是完全防止?

效果: 在如果事务B在事务A执行中, insert了一条数据并提交, 事务A再次查询, 虽然读取的是undo中的旧版本数据(防止了部分幻读), 但是事务A中执行update或者delete都是可以成功的!!
因为在innodb中的操作可以分为当前读(current read)和快照读(snapshot read):
3.快照读(snapshot read)

简单的select操作(当然不包括 select ... lock in share mode, select ... for update)
4.当前读(current read) 官网文档 Locking Reads

select ... lock in share mode
select ... for update
insert
update
delete
在RR级别下，快照读是通过MVVC(多版本控制)和undo log来实现的，当前读是通过加record lock(记录锁)和gap lock(间隙锁)来实现的。
innodb在快照读的情况下并没有真正的避免幻读, 但是在当前读的情况下避免了不可重复读和幻读!!!
>小节  

一般我们认为MVCC有下面几个特点：

每行数据都存在一个版本，每次数据更新时都更新该版本
修改时Copy出当前版本, 然后随意修改，各个事务之间无干扰
保存时比较版本号，如果成功(commit)，则覆盖原记录, 失败则放弃copy(rollback)
就是每行都有版本号，保存时根据版本号决定是否成功，听起来含有乐观锁的味道, 因为这看起来正是，在提交的时候才能知道到底能否提交成功
而InnoDB实现MVCC的方式是:

事务以排他锁的形式修改原始数据
把修改前的数据存放于undo log，通过回滚指针与主数据关联
修改成功（commit）啥都不做，失败则恢复undo log中的数据（rollback）
二者最本质的区别是: 当修改数据时是否要排他锁定，如果锁定了还算不算是MVCC？
Innodb的实现真算不上MVCC, 因为并没有实现核心的多版本共存, undo log 中的内容只是串行化的结果, 记录了多个事务的过程, 不属于多版本共存。但理想的MVCC是难以实现的, 当事务仅修改一行记录使用理想的MVCC模式是没有问题的, 可以通过比较版本号进行回滚, 但当事务影响到多行数据时, 理想的MVCC就无能为力了。
比如, 如果事务A执行理想的MVCC, 修改Row1成功, 而修改Row2失败, 此时需要回滚Row1, 但因为Row1没有被锁定, 其数据可能又被事务B所修改, 如果此时回滚Row1的内容，则会破坏事务B的修改结果，导致事务B违反ACID。 这也正是所谓的 第一类更新丢失 的情况。
也正是因为InnoDB使用的MVCC中结合了排他锁, 不是纯的MVCC, 所以第一类更新丢失是不会出现了, 一般说更新丢失都是指第二类丢失更新。

# 面试题
https://blog.csdn.net/jingyangV587/article/details/102531764


# 高并发系统限流




