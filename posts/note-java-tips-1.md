---
title: java小贴士-1
date: '2015-01-06'
description:
categories: 'java'
---

# java中的双括号反模式（Double Curly Braces Anti Pattern）

```java
Map map = new HashMap() {{
    put("", "")
}};
```

这种双括号反模式会创建一个HashMap的匿名子类，然后用双括号里的内容初始化这个子类。并且每次都是新的子类。有两个问题：   

* 如果有大量的这种用法，会产生大量的匿名内部类
* 匿名内部类对应对象，会持有对外部对象的引用，如果处理不好，加上外部对象比较中，容易造成内存泄露。  

结论：  

1. 内部类是一头野兽。它已经造成过许多的问题以及认知失衡。匿名内部类则更为严重
2. 不要自作聪明了，别使用双括号来进行初始化

参考:  

《切勿自作聪明，慎用双括号反模式》http://it.deepinmind.com/java/2014/12/12/dont-be-clever-the-double-curly-braces-anti-pattern.html  


# 


# 