---
title: java锁笔记
date: '2013-08-30'
description:
categories: java
tags:

---
# 线程通信

通信遇到的问题包括：  

1. 共享对象：需要做同步处理
2. 忙等待：循环等待另一个线程发来信号，可sleep
3. wait(),notify()

        1. 遇到mobj.wait()，让线程进入非运行状态，直到另一个线程mobj.nitify
        2. 这几个方法必须在获得mobj锁的前提下调用，否则IllegalMonitorStateException
        3. 一个线程调用了wait()，即释放mobj上的锁
        4. 一旦一个线程被唤醒，不能立刻就退出wait()的方法调用，直到调用
        notify()的线程退出了它自己的同步块。因此，多个线程被notifyAll唤醒，只有一个
        线程可以最终退出wait()方法，因为必须获得mobj的锁

4. 丢失的信号（Missed Signal）

        线程A先调用了mobj.notify()，线程B后调用mobj.wait()，通知信号发送过了就被丢弃，
        线程B就可能永远不能醒来。
        这就需要一个额外的变量来保存‘是否被通知过’的信息。

5. 假唤醒（spurious wakeups）

        没有调用过notify()和notifyAll()的情况下醒来，原因未知
        需要加个自旋锁，即将wait()方法放到while循环中

6. 不要在字符串常量或全局对象中调用wait()。mobj与信号常量不是一一对应。

mobj: 管程 (英语：Monitors，也称为监视器)  


# 锁

锁由synchronized实现，有以下几个特性:  

1. 同一时刻只能有一个线程穿过临界区，进入同步块
2. 锁的可重入性：synchronized同步块可重入，相应也有可重入锁
3. 锁的公平性：多个线程不断竞争访问相同的synchronized同步块，可能存在某个
线程一直竞争不到。



**参考**:  
[Java中的锁](http://ifeve.com/locks/)  
[线程通信](http://ifeve.com/thread-signaling/)  
