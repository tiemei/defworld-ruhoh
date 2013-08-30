---
title: tomcat源码阅读-1-准备源码
date: '2013-05-20'
description:
categories: java
tags: 'tomcat'

type: draft
---

1. 下载编译
  1. svn co http://svn.apache.org/repos/asf/tomcat/tc6.0.x/tags/TOMCAT_6_0_29/
  2. cp build.properties.default build.properties，重新指定base.path
  3. `ant download`下载依赖到base.path指定的目录
  4. `ant`编译
  5. 编译后的tomcat在**output/build**目录
2. 导入IDE-IDEA
  1. file → import module → java module
  2. add module dpendencies "jars or derectory"
     1. ant.jar
     2. jaxrpc.jar
     3. org.eclipse.jdt.core_3.3.1.v_780_R33x.jar
     4. wsdl4j-1.5.1.jar
3. 运行


参考:  
[Eclipse导入Tomcat源码](http://www.cnblogs.com/huangfox/archive/2011/10/20/2218970.html)  



