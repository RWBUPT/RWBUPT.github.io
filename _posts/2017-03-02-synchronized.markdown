---
layout: post
title:  "synchronized与Lock"
date:   2017-03-02 11:14:54
categories: Java
---

两种线程同步机制的总结整理。

这里吐槽一下《Java核心技术》这本书里有关多线程的部分，顺序混乱又有冗余，看完一头雾水。

## Lock



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
> 同一个类的对象锁和类锁互不干涉。

## synchronized与ReentrantLock的区别

- ReentrantLock可以中断一个正在试图获得锁的线程（lockInterruptibly() ）
- ReentrantLock可以非阻塞地获取锁（tryLock()，立刻返回“拿到”或“没拿到”，可设定等待时间，实现定时锁OR轮询锁）
- ReentrantLock对象可以通过调用newCondition()方法同时绑定多个Condition对象，实现多个控制条件，而synchronized依靠wait(), notify(), notifyAll()只能实现一个隐含条件。
- ReentrantLock可以实现公平锁（线程按照请求锁的顺序依次获得锁）。
- 资源但竞争激烈时synchronized性能大幅下降，ReentrantLock几乎不变。
- synchronized块必须被完整地包含在单个方法里，而Lock对象可以把它的lock()和unlock()方法的调用放在不同的方法里。
- **synchronized是在JVM层面上实现的，出现异常会自动释放锁；Lock则需要手动释放，所以必须在finally中实现unlock()。**














