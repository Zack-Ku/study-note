

# CAS

内存值，旧值，新值。
内存值为 volidate标注，
旧值，在修改前获取。然后修改时对比内存值，如果不同，则证明被修改了，放弃修改。如果相同，则新值替换旧值。

# wait()、notify()、notifyAll()——Object类中

https://www.cnblogs.com/lirenzhujiu/p/5927241.html

调用wait()会使线程放弃锁，让出CPU时间，进入该对象的等待池。

在Object类中的原因是。wait需要配合synchronized，而synchronized是可以锁任意对象的。所以wait在对象里面

notify是唤醒单个，但不确定是哪个。notifyAll()唤醒全部

在循环上调用wait，因为不确定唤醒了是哪个线程，如果唤醒的不对，则继续wait()



# interrupt()、sleep()... ——Thread类中

sleep()**不会释放锁**，不需要sync块中。进入阻塞，退出CPU时间、不占用cpu时间。CPU时间后，进入就绪态、开始竞争CPU时间。
interrupt()，给线程发送中断信号。



# 线程的生命周期

新建 -> 就绪 -> 运行 -> 死亡
运行 yield() -> 就绪
运行 sleep()、suspend() -> 阻塞
阻塞 resume() -> 就绪

# synchronized原理

在1.6以前，是通过Monitor进行加锁的，是重量级锁。

1.6以后引入了锁的优化，轻量级锁、偏向锁



# 悲观/乐观锁、重量级/轻量级/偏向锁

https://zackku.com/java-thread-lock-base/

- **悲观锁**：认为写多读少，每次读写操作都上锁。

- **乐观锁**：认为读多些少，写时才上锁。

- **重量级锁**：悲观锁的一种，1.6以前synchronized 用monitorenter和monitorexit。多个线程同时进入临界区

- **轻量级锁**：判断是否有锁、无锁则判断记录。有锁通过自旋CAS尝试获取锁（对比Mark Word），然后锁膨胀为重量级锁，因为需要锁同时读写。多个线程交替进入临界区

- **偏向锁**：不存在竞争情况的锁，当偏向改变，则升级为轻量级锁。仅有一个线程进入临界区

  

# CountDownLatch、CycleBarrier、Semaphore————同步辅助

- **CountDownLatch**：计数地等待线程数结束
- **CycleBarrier**：栅栏。设置目的点，等待其他线程到底目的点
- **Semaphore**：信号量。控制资源被获取数，每次获取acquire()、释放release()。



# 线程池

**过程**：当execute提交一个任务后，会添加到队列中，然后线程会根据当前线程数判断是否拿出任务出来执行。

**参数**：

- **corePoolSize**，保持存活的线程，即使闲置。刚开始线程为0。
- **maximumPoolSize**，线程池允许同时存活最大线程数。
- **keepAliveTime**，大于corePoolSize的线程，会保持keepAliveTime时间内不销毁。
- **workQueue**，存放等待执行的任务（Runnable对象），execute()方法提交的任务。
- **rejectedExecutionHandler**，当队列满了和线程数饱和时拒绝任务。

**三种队列**：

- **SynchronousQueue**：直接递交任务，不保存。
- **LinkedBlockingQueue**：无上限队列，可以提交无线个队列。
- **ArrayBlockingQueue**：常规队列，避免资源耗尽。

BlockingQueue用于提供给其他操作，等待排空。



