---
title: JMM
date: '2013-05-21'
description:
categories: "java"
tags: JMM

type: draft
---

[JMM基础](http://ifeve.com/java-memory-model-1/)  
[JMM重排序](http://ifeve.com/java-memory-model-2/)  
[JMM顺序一致性](http://ifeve.com/java-memory-model-3/)  
[JMM volatile](http://ifeve.com/java-memory-model-4/)  
[JMM锁](http://ifeve.com/java-memory-model-5/)  
[JMM final](http://ifeve.com/java-memory-model/)  
[JMM总结](http://ifeve.com/java-memory-model-7/)  


JMM的happen before保证 -> “真正”同步处理的程序 -> 满足JMM意义上的“顺序一致性模型”

Q:  

* 线程A在释放锁之前所有可见的共享变量，在线程B获取同一个锁之后，将立刻变得对B线程可见
线程释放锁


