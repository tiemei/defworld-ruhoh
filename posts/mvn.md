---
title: 我的maven相关积累
date: '2013-06-28'
description:
categories: 'tool'
tags: 

type: draft
---
## 常用命令
新建一个空白工程  

    mvn archetype:create -DgroupId=com.tiemei -DartifactId=p1 -DpackageName=com.tiemei.p1
下载依赖源码  

    mvn dependency:sources
    mvn eclipse:eclipse -DdownloadSources=true
查看依赖树  

    mvn dependency:tree

## 常用插件
maven-compiler-plugin  
1. maven2.1 默认用jdk 1.3来编译，这个版本不支持注解，compiler插件可以制定jdk版本为1.6
2. windows平台默认GBK编码，如果工程编码为utf-8，需要再compiler插件中指定
