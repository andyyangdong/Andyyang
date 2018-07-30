# 基础知识

### 一、基础

*   基本语法

    基本数据类型；运算符；表达式；选择与循环语句；类与对象（普通类、抽象类、接口、枚举、Annotation、内部类）；继承与实现；异常；package与jar包；序列化与反序列化；正则表达式；重载与覆盖；

## java修饰词

### 1.volatile介绍
1.禁止指令重排序优化  2.保证变量可见性，但无法保证原子性
### 2.synchronized介绍
对象 syncObject 锁，可以针对任意代码块，且可任意指定上锁的对象
#### 3.final介绍
如果修饰变量标识为常量，运行过程中会将值直接替换到变量这个占位符中（避免根据内存地址再次查找这层消耗）；如果修改方法，方法不允许被覆盖；修饰类，类不允许被继承。

基础类型，如String，不允许修改。

集合，如Map、List，引用地址不允许改，但可以put、get等操作
#### 4.static
*   声明属性

    为全局属性，放在全局数据区，只分配一
*   声明方法

    类方法，可以由类名称直接调用
*   声明类

### 5.transient
一个对象中的某个属性不希望被序列化



*   数组

    一维数组；二维数组。。。

*   集合

    Collection接口；Set相关；List相关；Map相关  
Collection是集合类的上级接口，继承与他有关的接口主要有List和Set
Set:不会存储重复的元素
HashSet 线程不安全，存取速度快。底层是以哈希表实现的
TreeSet:当Comparable比较方式，及Comparator比较方式同时存在，以Comparator

比较方式为主

1、是否允许空
2、是否允许重复数据
3、是否有序，有序的意思是读取数据的顺序和存放数据的顺序是否一致
4、是否线程安全

  
 #    List相关
 ## ArrayList 详解   
  
（1）的ArrayList以数组形式实现，顺序插入，查找快，插入，删除较慢

（2）链表以链表形式实现，顺序插入，查找较慢，插入，删除方便

>* 默认的底层数组大小是10
>*  扩容的时候把元素组大小先乘以3，再除以2，最后加1 Arrays的copyOf方法，将元素组里面的内容复制到新的数组
>* 所有元素利用System,arraycopy方法做一个整体的复制  （remove，add）
>*  Vector是线程安全的，ArrayList是线程非安全的   ;Vector如果不指定增长因子，那么就给原数组大小*2

 ## LinkedList 详解
>* LinkedList既然是一种双向链表：链表的尾节点的后一个节点是链表的头结点，链表的头结点的前一个节点是链表的尾节点
>* 每次顺序插入的时候LinkedList将new一个对象出来，所以顺序插入LinkedList必然慢于ArrayList，那么LinkedList将比ArrayList更耗费一些内存
>* ArrayList的遍历效率会比LinkedList的遍历效率高一些
>* 插入、删除的元素是在前半段，那么就使用LinkedList
>* ArrayList使用最普通的for循环遍历，LinkedList使用foreach循环比较快    for (Integer i : list)
 
 ## CopyOnWriteArrayList 详解
>* 任何可变的操作（add、set、remove等等）都是伴随复制这个动作的
>* 加锁的ArrayList的扩容
>* Vector  能够保证增、删、改、查的单个操作一定是原子的  绝对的线程安全
>* 读和写操作的不是同一个对象，这就是读写分离
>* 随着CopyOnWriteArrayList中元素的增加，CopyOnWriteArrayList的修改代价将越来越昂贵，因此，CopyOnWriteArrayList适用于读操作远多于修改操作的并发场景中

 # Map 详解

 ##   HashMap 详解
HashMap是一种非常常见、方便和有用的集合，是一种键值对（K-V）形式的存储结构
1.HashMap在new的时候构造出了一个大小为16的Entry数组，Entry内所有数据都取默认值 4.每次放置完Entry之后都会判断是否需要扩容
>* 对比Key是否相同，是先比HashCode是否相同，HashCode相同再判断equals是否为true
>* 它的顺序是基于HashCode，HashCode是一个随机性很强的数字  ,HashCode因为是int值，比较速度非常快
>* 在readObject的时候重构HashMap数据结构 保证跨平台性
>* Hashtable是线程安全的，Hashtable所有对外提供的方法都使用了synchronized，也就是同步，而HashMap则是线程非安全的
>* Hashtable不允许空的value，空的value将导致空指针异常，而HashMap则无所谓，没有这方面的限制
>* 为什么线程不安全？插入的元素超过了容量（由负载因子决定）的范围就会触发扩容操作，就是rehash，这个会重新将原数组的内容重新hash到新的扩容数组中，在多线程的环境下，存在同时其他的元素也在进行put操作，如果hash值相同，可能出现同时在同一数组下用链表表示，造成闭环，导致在get时会出现死循环

 ##   **LinkedHashMap** 详解
可以认为是HashMap+LinkedList即它既使用HashMap操作数据结构，又使用LinkedList维护插入元素的先后顺序

>* 增加了时间和空间上的开销，但是通过维护一个运行于所有条目的双向链表
>* 每次访问一个元素（get或put），被访问的元素都被提到最后面去了

 ##   **ConcurrentHashMap** 详解

ReentrantLock +时间段+ HashEntry，到JDK1.8版本中同步+ CAS + HashEntry +红黑树

>* 经过一次hash定位到Segment的位置，然后再hash定位到指定的HashEntry,HashEntry下的链表进行对比
>* Node是ConcurrentHashMap存储结构的基本单元，继承于HashMap中的Entry
>* 如果没有初始化就先调用initTable 2. hash冲突就直接CAS插入 3.进行扩容操作就先进行扩容 4.存在hash冲突，就加锁来保证线程安全，这里有两种情况，一种是链表形式就直接遍历到尾端插入，一种是红黑树就按照红黑树结构插入 5.添加成功就调用addCount（）方法统计size，并且检查是否需要扩容
>* 1.计算散列值，定位到该表索引位置，如果是首节点符合就返回 2,如果遇到扩容的时候，会调用标志正在扩容节点ForwardingNode的找方法，查找该节点，匹配就返回 3.都不符合的话,就往下遍历节点，匹配就返回，否则最后就返回空

 

*   线程

    Thread；Runnable；Callable；线程状态；优先级；

*   IO

    File类；字节流（InputStream、OutputStream）；字符流（Reader、Writer）；转换流（OutputStreamWriter、InputStreamReader）；压缩流；

     Java IO和NIO之间第一个最大的区别是，IO是面向流的，NIO是面向缓冲区的

*   网络

    TCP编程；UDP编程

*   泛型

*   反射

    提供api方法取得类的结构；调用类的方法；动态代理
*   Integer和int之间==的比较问题
Integer是int的封装对象，两个对象==比较的是栈的值
默认是-128-127之间，如果参数中的i在这个范围之内，则返回一个数组中的内容，如果不在这个范围，则new一个新的Integer对象并返回。查看Integer类的源码可以发现，这个数组里面缓存了基本类型-128-127之间的Integer对象
因为int在堆中是不开辟内存的，他在栈中的值则为他本身的值
