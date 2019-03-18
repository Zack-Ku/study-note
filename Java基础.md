# HashMap
https://mp.weixin.qq.com/s/__ZnkPAF6ucUqN8CVSVQeA

- 无锁，高效，非线程安全。可以接受null值，无序。
- 由数组和链表组成，通过hashcode获得bucket（桶位置），然后通过key.equal遍历链表获取结果。
- 为了减少碰撞，最好用String，Integer，是final的，hashCode不会变。开放寻址，找到空位存值，可避免过多碰撞。
- 当链表长度少于8，为链表，大于等8，转换成红黑树。
- 容器超过75%的时候，会扩容两倍，重排。
- 初始化的时候，能预估最好预设初始化大小，避免频繁扩容

# TreeMap、LinkedHashMap——有序
https://www.cnblogs.com/xiaoxi/p/6170590.html

TreeMap：红黑树的实现，按key值排序
LinkedHashMap：HashMap+LinkedList。链表部分维护多一个双向链表，保持插入有序



# HashTable

sychronized加锁，线程安全，低效。



# ConcurrentHashMap
JDK1.7时用ReentranLock，分段式加锁。Segment是一个分段。

JDK1.8抛弃分段锁，用CAS自旋替换值



# CopyOnWriteArrayList/Set
COW机制，所有人共享一个内容，当有人尝试修改的时候，会复制一份新的出来修改，然后替换旧的。这样对于旧的数据不会有读并发问题。
读不加锁，写加锁。
缺点：内存消耗多；数据不会实时一致，只会最终一致。



# transient关键词 
标注成员变量，避免序列化



# 动态代理

https://zackku.com/java-dynamic-proxy/

通过 Proxy.newProxyInstance()方法，新增一个代理对象，当代理对象的方法被触发的时候，会拦截触发InvocationHandler，在其中拦截插入对应的代码。



#值传递、引用传递

java中，基本类型都是值传递，否则都是引用传递。



# ==与equals

- **==**，对比地址
- **equals()**，没Override，则与==相同，否则一般是对比内容

一般基本类型可以用==，否则最好用equals()



# hashCode与equals

- **hashcode**，一般用于判断重复，在hashset中，优化哈希过程
- **equals**，用于判断对象内容



# String和StringBuffer、StringBuilder

- **String**，final标记，不可变，常量。
- **StringBuffer**，线程安全
- **StringBuilder**，非线程安全，性能好







