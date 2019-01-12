# HashMap
https://mp.weixin.qq.com/s/__ZnkPAF6ucUqN8CVSVQeA
无锁，高效，非线程安全。可以接受null值，无序。
由数组和链表组成，通过hashcode获得bucket（桶位置），然后通过key.equal遍历链表获取结果。

为了减少碰撞，最好用String，Integer，是final的，hashCode不会变。
开放寻址，找到空位存值，可避免过多碰撞。

当链表长度少于8，为链表，大于等8，转换成红黑树。

容器超过75%的时候，会扩容两倍，重排。

# 红黑树
1. 每个节点非红即黑
2. 根节点总是黑色的
3. 如果节点是红色的，则它的子节点必须是黑色的（反之不一定）
4. 每个叶子节点都是黑色的空节点（NIL节点）
5. 从根节点到叶节点或空子节点的每条路径，必须包含相同数目的黑色节点（即相同的黑色高度）

# HashTable
sychronized加锁，线程安全，低效。

# ConcurrentHashMap
JDK1.7时用ReentranLock，分段式加锁。Segment是一个分段。

JDK1.8抛弃分段锁，用CAS自旋替换值

# CAS
内存值，旧值，新值。
内存值为 volidate标注，
旧值，在修改前获取。然后修改时对比内存值，如果不同，则证明被修改了，放弃修改。如果相同，则新值替换旧值。

# wait()、notify()、notifyAll()——Object类中
https://www.cnblogs.com/lirenzhujiu/p/5927241.html
在Object类中的原因是。wait需要配合synchronized，而synchronized是可以锁任意对象的。所以wait在对象里面

notify是唤醒单个，但不确定是哪个。notifyAll()唤醒全部

在循环上调用wait，因为不确定唤醒了是哪个线程，如果唤醒的不对，则继续wait()

# interrupt()、sleep()... ——Thread类中
sleep()不会释放锁，不需要sync块中。进入阻塞，但不占用cpu时间。

