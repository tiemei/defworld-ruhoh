---
title: jdk自带工具
date: '2013-08-30'
description:
categories: java
---

# javah
## JNI示例
参考:[JNI example](https://blogs.oracle.com/moonocean/entry/a_simple_example_of_jni)  

1. 写一个java类，生命navtive方法
2. 调用javah生成头文件
3. 写c代码，生成库文件
4. `java -Djava.library.path=<path_of_native_lib> JNIFoo`会默认先载入头文件，
然后按指定的路径查找库文件

# jmap(Java Memory Map)
参考:[jmap](http://blog.csdn.net/fenglibing/article/details/6411953)  
## jmap -histo pid
![jmap output](http://farm8.staticflickr.com/7346/9627329472_b28eba137c.jpg)


    B代表byte
    C代表char
    D代表double
    F代表float
    I代表int
    J代表long
    Z代表boolean
    前边有[代表数组，[I 就相当于int[]


## jmap -heap pid
查看JVM初始化内存分配，以及各代详细的内存使用

## 产生heap dump文件

* `jmap -heap:format=b pid` JDK1.5
* `jmap -dump:live,format=b,file=xxx pid` JDK1.6

## jmap -permstat pid
打印一些持久代上的内存使用状态

# jstack

* `pstree -p pid` 查看某个进程的进程数
* `top -H` 线程维度查看
* `ps -eLf | grep java | wc` 查看java线程数
* `ps -Lf 24551`查看某个进程所有线程
* `ps mp pid -o THREAD,tid` 查看某个进程下各线程的运行状态
* `netstat -anp |grep port` 查看占用某个端口的进程

## 结果分析
[cankao](http://iamzhongyong.iteye.com/blog/1441285)  
可以分析到：  

1. 死锁：看到线程已获得哪个锁，在等待哪个锁
2. 程序运行慢：查看waiting状态的几个线程在干嘛
3. cpu占用特别高的线程在干什么
  1. 某个进程占用cpu/mem较高，用ps查看改进程各线程运行状态
  2. 用jstack查看改线程的调用堆栈，定位问题

一个例子:  

    "main" prio=10 tid=0x0000000040c75800 nid=0x5fe8 runnable [0x0000000040209000]
    "xxx" - 线程名 
    prio  - 优先级 
    tid   - 线程id(jmm中内存规范中地址) 
    nid   - 是linux下的线程id  
    runnable - 线程状态
    "main" 主线程，后面接了daemon未守护线程
    "http-bio-8080-exec-xxx" http 8080 端口bloked io 线程

## JVM pid文件
jvm运行时会生成一个目录hsperfdata_$USER(linux默认在/tmp/hsperfdata_$USER)，存放Pid文件(/etc/cron.daily/tmpwatch会240h移除这个目录)。jstack等工具读取这个文件获取pid。（[参考](http://windows9834.blog.163.com/blog/static/273450042012919101435608/)）从Java 6 update 21 ~ 24，会优先`-Djava.io.tmpdir`，这样jstack等文件可可能找不到pid文件。
