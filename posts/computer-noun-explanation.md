---
title: 计算机、编程术语解释
date: '2014-12-19'
description:
categories: 'other'

type: draft
---

# SOA

> “取乎其上，得乎其中；取乎其中，得乎其下；取乎其下，则无所得矣” —— 孔子

参考：  

SOA wiki : http://zh.wikipedia.org/wiki/%E9%9D%A2%E5%90%91%E6%9C%8D%E5%8A%A1%E7%9A%84%E6%9E%B6%E6%9E%84  
微观SOA：服务设计原则及其实践方式（上篇）: http://www.infoq.com/cn/articles/micro-soa-1  
微观SOA：服务设计原则及其实践方式（下篇）：http://www.infoq.com/cn/articles/micro-soa-2?utm_source=infoq&utm_medium=related_content_link&utm_campaign=relatedContent_articles_clk  

面向服务的体系结构（Service-oriented architecture）是构造分布式计算的应用程序的方法。它将应用程序功能作为服务发送给最终用户或者其他服务。  
当当网架构师沈理把SOA分为“宏观SOA”、“微观SOA”，宏观SOA考虑的东西比较大，而微观的比较容易实施落地。至于微观SOA的原则可能需要在实践中总结，沈理给出了他的最佳实践原则：  

* 服务设计原则1：优化远程调用
* 服务设计原则2：消除冗余数据
* 服务设计原则3：粗粒度契约
* 服务设计原则4：通用契约
* 服务设计原则5：隔离变化
* 服务设计原则6：契约先行
* 服务设计原则7：稳定和兼容的契约
* 服务设计原则8：契约包装  