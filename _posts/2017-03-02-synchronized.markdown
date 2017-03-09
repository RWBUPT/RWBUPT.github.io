---
layout: post
title:  "synchronized与Lock"
date:   2017-03-02 11:14:54
categories: Interview
---

两种线程同步机制的总结整理。

这里吐槽一下《Java核心技术》这本书里有关多线程的部分，顺序混乱又有冗余，看完一头雾水。

## Lock

> Java SE1.6里锁一共有四种状态：无锁状态，偏向锁状态，轻量级锁状态和重量级锁状态。可升级不可降级。

**锁优化**

- 自旋锁：在轻量级锁中使用，让后面请求锁的线程执行一个忙循环（自旋），看看持有锁的线程是否很快就会释放锁。若超过限定次数的自旋后仍未得到锁，进入挂起状态。JDK1.6引入了适应性：如果在同一个锁对象上，自旋等待刚刚成功获得过锁，并且持有锁的线程正在运行中，那么虚拟机就会认为这次自旋很有可能再次成功，进而允许自旋等待持续相对更长的时间。如果对于某个锁，自旋很少成功获得过，那在以后要获取这个锁时将可能省略掉自旋过程，以免浪费处理器资源。

- 偏向锁：锁会偏向于第一个获得它的线程。如果在接下来的执行过程中，该锁没有被其他的线程获取，则持有偏向锁的线程将永远不需要再进行同步。锁偏向消除数据在无竞争情况下的同步原语，提高性能。

- 轻量级锁：轻量级锁提升程序同步性能的依据是“对于绝大部分的锁，在整个同步周期内都是不存在竞争的”，这是一个经验数据。轻量级锁在当前线程的栈帧中建立一个名为锁记录的空间，用于存储锁对象目前的指向和状态。如果没有竞争，轻量级锁使用CAS操作去消除同步使用的互斥量的开销，但如果存在锁竞争，除了互斥量的开销外，还额外发生了CAS操作，因此在有竞争的情况下，轻量级锁会比传统的重量级锁更慢。

- 减少锁持有时间

- 减小锁粒度：将大对象（这个对象可能会被很多线程访问）拆成小对象，增加并行度，降低锁竞争。最典型的减小锁粒度的案例就是采用了分段锁的ConcurrentHashMap。源码分析[戳我](https://my.oschina.net/hosee/blog/675884)【吐槽一句，这玩意真难...】

- 锁分离：比如读写锁ReadWriteLock，根据功能进行分离成读锁和写锁，读读不互斥，读写互斥，写写互斥，即保证了线程安全，又提高了性能。（读写锁的降级：先得到一个写锁，在释放写锁之前再得到一个读锁。注意：这两个锁都需要被释放。）

- 锁消除：在某段要求同步的代码中，堆上的所有数据都不会逃逸出去（被其他线程访问），那就可以把他们当做栈上数据对待，认为他们是线程私有的，代码执行时就会忽略同步的要求。

- 锁粗化：连续对同一个对象反复加锁和解锁，频繁的互斥同步操作会影响性能。此时应扩展同步范围，如下：
```java
for (long i = 0;i < 100000000;i++){
	synchronized(object){   // 代码在循环中不断的需要获得对象锁，耗费资源
	}
}
//////////////
synchronized(object){ //粗化后效率提升
for (long i = 0;i < 100000000;i++){   
    }
}
```

**锁的相关概念**

- 可重入锁：一个线程在获取某个锁后，还可以继续获取该锁（通过关联一个请求计数），对于不同线程则相当于普通的互斥锁。它可以避免死锁。ReentrantLock和Synchronized都是可重入锁。案例[戳我](http://blog.csdn.net/ns_code/article/details/17014135) 

- 公平锁（new ReentrantLock(true)）：线程按照申请锁的先后顺序来获得锁。保障线程不饿死，但效率低。

- 悲观锁：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作.Synchronized互斥锁就是悲观的。

  乐观锁：假定不会发生并发冲突，只在提交操作时检测是否违反数据完整性。乐观锁避免了悲观锁独占对象的现象，同时也提高了并发性能。案例[戳我](http://www.kancloud.cn/seaboat/java-concurrent/117870)

- 共享锁：如果事务T对数据A加上共享锁后，则其他事务只能对A再加共享锁，不能加排它锁。获准共享锁的事务只能读数据，不能修改数据。

  排它锁：如果事务T对数据A加上排它锁后，则其他事务不能再对A加任何类型的锁。获得排它锁的事务即能读数据又能修改数据。

- 闭锁：在闭锁状态结束之前，没有任何线程能通过。用来确保某些活动直到其他活动都完成后才继续执行。

**ReentrantLock**
```java
Lock lock = new ReentrantLock();  
lock.lock();  
//code
lock.unlock();  
```
ReentrantLock的await()、signal()、signalAll()对应Object中的wait()、notify()、notifyAll()，后三者用于synchronized

源码解析[戳我](http://www.cnblogs.com/zhimingyang/p/5702752.html)

## synchronized关键字

> 注意：synchronized不具有继承性。

JVM中的每个对象和类都与一个内部锁相关联，程序通过这个锁来协调对象的多线程访问。

被synchronized修饰的代码，同一时刻只能被一个线程访问。

synchronized有两种使用方法：

- 修饰方法：
```java
synchronized public void xxx()    //对象锁
synchronized static public void xxx()   //类锁，对类的所有实例起作用
```
- 修饰代码块：
```java
synchronized (object) {   //对象锁，this或非this对象
    //code block
}
synchronized (类.Class) {   //类锁
    //code block
}
```

> 注意：同一个类的对象锁和类锁互不干涉。


## synchronized与ReentrantLock的区别

- ReentrantLock可以**中断**一个正在试图获得锁的线程（lockInterruptibly() ）
- ReentrantLock可以**非阻塞**地获取锁（tryLock()，立刻返回“拿到”或“没拿到”，可设定等待时间，实现定时锁OR轮询锁）
- ReentrantLock对象可以通过调用newCondition()方法同时绑定**多个Condition**对象，实现多个控制条件，而synchronized依靠wait(), notify(), notifyAll()只能实现**一个**隐含条件。
- ReentrantLock可以实现**公平**锁。
- 资源但竞争激烈时synchronized**性能大幅下降**，ReentrantLock几乎不变。
- synchronized块必须被完整地包含在单个方法里，而Lock对象可以把它的lock()和unlock()方法的调用放在不同的方法里。
- synchronized是在JVM层面上实现的，出现异常会**自动释放锁**；Lock是基于JDK实现的，需要手动释放，所以**必须在finally中实现unlock()**














