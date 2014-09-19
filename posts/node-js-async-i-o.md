---
title: 聊聊Node.js的异步I/O
date: '2014-09-19'
description: 
categories: "Node.js"
---

# 阻塞vs非阻塞，同步vs异步

阻塞是你做一件事不能立即得到返回，否则是非阻塞。同步是做完一件事，做下一件事，反之异步。  
阻塞非阻塞都是同步，异步就无所谓阻塞非阻塞了。  

# 各种I/O模型

一个输入主要包括两个阶段：  

1. 等待数据到达
2. 将数据从内核空间拷贝到用户空间


Blocking I/O model:  

![Blocking I/O model](https://farm6.staticflickr.com/5574/15261520936_c092d571bd.jpg)  

这里以UDP为例，因为UDP的datagram ready只要一个datagram到了就行了，TCP相对复杂很多。这里我们发现系统层面一个系统调用需要等待1，2都完成后才能返回。  

Nonblocking I/O model:  

![Nonblocking I/O model](https://farm4.staticflickr.com/3902/15284612605_bc8f5d4653.jpg)  

当我们把一个socket设置为NonBlocking时，recvfrom系统调用就不会使得调用程序sleep，而是立即返回一个EWOULDBLOCK的错误。应用程序需要重复调用recvfrom，直到某次调用datagram ready，然后等待data copy完成再返回。  

I/O multiplexing model:  

![I/O multiplexing model](https://farm6.staticflickr.com/5594/15261591366_8e44175e2e.jpg)  

跟Blocking I/O不同的是，I/O multiplexing model调用select等待多个socket句柄上，知道其中一个到达datagram ready的状态，程序拿到socket句柄再去调用一次recvfrom做一次copy然后返回。  
多线程加上Blocking I/O也能模拟这种模式，只是一个这种方式是多个线程分别阻塞调用recvfrom，分别等待在一个socket句柄上。  

Signal-Driven I/O model：  

![Signal-Driven I/O model](https://farm4.staticflickr.com/3894/15284215892_56e035732c.jpg)  

先将socket设置成signal-driven I/O，然后使用系统调用sigaction绑定一个signal handler。然后main loop可以干别的事，一直到datagram ready状态，kernel发出一个信号告知signal handler,可以由signal handler调用recvfrom copy完毕后再通知main loop处理，也可以让main loop调用recvfrom。  

![Asynchronous I/O model](https://farm3.staticflickr.com/2941/15098054478_83da95610f.jpg)  

等到copy做完了signal才会生成传递给signal hanlder，这是不同于Signal-Driven I/O model的地方。  

最后也可以看出，前四种I/O模式都是阻塞的，因为recvfrom系统调用都被阻塞了，有的两个阶段都被阻塞了，有的只有copy阶段被阻塞了。最后，来看下总的对比图：  

![Comparison of the five I/O models.](https://farm3.staticflickr.com/2941/15098149637_2a1664950a.jpg)  

# Node.js里如何实现Async I/O

操作系统层面有几种多路复用的api:  

* read ： 最原始、性能最低的一种，重复调用来检查I/O的状态
* select：在read基础上的改进方案，通过对文件描述符桑冕的事件状态进行判断。但它此阿勇1024长度的数组来存储状态，最多而已同时检查1024个文件描述符。
* poll：较select有所改进，采用链表方式避免长度限制，避免不需要的检查，但是当文件描述符较多时，性能还是十分低下。
* epoll：Linux下效率最高的I/O事件通知机制，进入轮询时如果么有检查到I/O事件，将会休眠，直到时间发生将它唤醒，它是真实利用了时间通知、执行回调的方式，而不是遍历查询，所以不会兰妃CPU，执行效率高。
* kqueu，与epoll类似，不错仅存在于FreeBSD系统下。

当以上几种方式，应用程序仍需要等待I/O完全返回，等待期间CPU要么遍历文件描述符，要不休眠。  

## 理想的异步I/O

CPU无需轮询何休眠，应用程序可以直接处理下一个任务，直到I/O完成后通过信号或者回调将数据传递给应用程序。  
Linux下的AIO就是这样的模式，通过信号或者回调来传递数据，但仅支持内核I/O中的O_DIRECT方式读取，导致无法利用系统缓存。  

## 现实的异步I/O

通过将逻辑处理和I/O放在不同线程中，可以模拟异步I/O。glibc的AIO是典型的线程池模拟Async I/O，然而有些bug。libev作者实现了一个异步I/O库libeio，也是用线程池与阻塞I/O模拟Async I/O，最初Node在*nix平台下采用了libeio配合libev实现Async I/O，Node v0.0.3中，自行实现了线程池来实现Async I/O。  
Windows下的IOCP也是一个Async I/O，不过内部也是线程池实现的，不同之处在于这些线程池由系统内核接受管理。Node在windos平台下用IOCP实现。  
Node.js -> libuv（抽象层） -> (*nix自定义线程池，Windows IOCP）。   

## 简单说说Node架构的一些概念

我们写的js代码实际上是在注册一些事件，Node的单线程实际上就指的是事件循环线程，它向各类事件观察者询问是否有待处理事件，而处理I/O的线程池就是制造事件源。这些观察者被询问的先后顺序是idel观察者，I/O观察者，check观察者。  
Node提供的几个异步调用都是绑定在这些观察者身上的：  

* `setTimeout()`绑定在定时观察者，不过因为一个Tick(一个事件循环)会占用一定事件，所以不一定准确，加上红黑树去存储定时器对象，资源和性能上不是最好。
* `setInterval()`也是绑定在定时观察者，跟setTimeout类似，只是会多次调用
* `process.nextTick()`绑定在idel观察者身上，比setTimeout轻量级，存在数组里。每个Tick执行数组里所有的。
* `setImmediate()`绑定在check观察者身上，存在链表里，每个Tick只执行第一个。



参考：

《Unix Network Programming Vol1 1002003, 3Ed 6.2 I/O Models》http://english.tebyan.net/newindex.aspx?pid=31159&BookID=23760&PageIndex=92&Language=3  
《怎样理解阻塞非阻塞与同步异步的区别？》http://www.zhihu.com/question/19732473  
《深入浅出Node.js》作者：朴灵
