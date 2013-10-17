---
title: JVM——类加载器，初始化
date: '2013-09-15'
description: 
categories: 'java'
---

# 类加载器

## “双亲”委派

1. 类加载器使用“双亲委派”模型。
![classloaders](http://farm4.staticflickr.com/3705/9757201665_47321648aa.jpg)

2. 可以自己写一个类编辑打成Jar包放到`$JAVA_HOME/jre/lib/ext`目录，再写一个启动类，
打印出加载所写类的classloader，发现是extclassloader。但是放到`$JAVA_HOME/jre/lib`
目录并不会被加载
3. 被加载的类唯一标识是类路径+类加载器id.

## 线程类加载器

首先介绍一个概念：**当前类加载器**，指当前方法方法所在类的加载器。`Class.forName(String)`
`Class.getResource(String)``X.class`都是用的当前类加载器。  
这是原有的“双亲委派”模型就不能解决问题了。例如JNDI的核心类在JRE的rt.jar中，而这些核心类
需要加载第三方厂商的JNDI实现，加载时的类加载器默认是“当前类加载器”，即`Bootstrap class loader`，
显然是加载不到这些实现类的。这时，**线程类加载器**就起作用了。让JNDI核心类的加载使用线程
上下文类加载器，打破“双亲委托”限定的向上代理机制。  
虽然下“线程类加载器”解决了某些特殊问题，但它也存在不足。除非所有线程使用同一个线程上下文类
加载器，否则它们的数据共享成为问题。并且，缺省规则为使用当前类加载器，有些代码你又没法控制，
这个问题变得更加复杂。  

    J2SE中常见类加载场景：
    → JNDI使用线程上下文类加载器
    → Class.getResource()和Class.forName()使用当前类加载器
    → JAXP使用上下文类加载器
    → Java.unit.ResourceBundle使用调用者的当前类加载器
    → URL协议处理器使用java.protocol.handler.pkgs系统属性并只使用系统类加载器
    → Java序列化API缺省使用调用者当前的类加载器.

## JVM类加载体系初始化过程
![JVM classloader init](http://farm3.staticflickr.com/2812/9758702554_fdd089dd36.jpg)  
  
* 载入方式

        → pre-loading 预先载入，未避免以后多次IO操作，JRE预先载入一些基础类，主要是rt.jar里
        → load-on-demand，按需载入
        
可用`java –verbos:class Main`查看加载详细过程  

* Static 何时执行

        → forName(String)执行，而ClassLoader.loadClass并不会执行，forName(String,false,ClassLoader)也不会执行
        → 载入class时没有执行static块，则在第一次实例化时执行

* 隐式加载/显示加载

        → 显示加载：用forName方法显示指定要加载哪个类
        → 隐式加载：new方法实例化时，JVM隐式帮忙做加载，并执行静态代码快

# tomcat中类加载器

除了J2SE已经有的最基础的类加载器，tomcat类加载还有以下几种：  

1. Catalina类加载器：对Tomcat服务器内核可见,对Web应用程序不可见。
tomcat内核线程上下文加载器就是它。
加载目录`server/classes和server/lib`
2. Common类加载器：Tomcat内核和每个Web应用程序都可以看见。
加载未知`common/class``common/lib`，例如servlet.jar
3. Shared类加载器：只对所有Web应用程序有效,对Tomcat不可见。
加载位置：`share/classes``share/lib`
4. Webapp类加载器：只对当前Web应用程序有效。
每个web应用程序的上下文类加载器就是它。
加载位置：`WEB-INF/classes``WEB-INF/lib`

## tomcat类加载顺序

1. 查看缓存中之前有没加载过
2. 没有，委托给system classloader加载，走“双亲委派”那套
3. 2加载不到，某些类会尝试用”shared类加载器“，其他用“webapp类加载器”加载
4. 3搞不定，向上委派

整个过程如图：  
![tomcat class loader](http://farm4.staticflickr.com/3777/9758884752_bac5a4b3b7.jpg)

## tomcat最新版本
Tomcat 6之后classloader 稍微有些改动了，去掉了server 和 shared, 都由common classloader 来载入了

# 初始化
待补充

参考:  
[ 深入JVM系列（三）之类加载、类加载器、双亲委派机制与常见问题](http://blog.csdn.net/vernonzheng/article/details/8461380)  
[ClassLoader类加载器](http://ieroot.com/2013/05/01/932.html)  
[Tomcat ClassLoader机制介绍](http://www.goldendoc.org/2010/12/tomcat_classloader/)  
[深入探讨Java的类加载机制](http://www.blogjava.net/William/archive/2006/08/25/65804.html)  
