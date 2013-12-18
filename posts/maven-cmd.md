---
title: 常用maven命令
date: '2013-09-12'
description: maven, 命令
categories: 'tool'
tags:

---

## 剪裁反应堆

* 构建指定几个模块`mvn clean install -pl account-email,account-persist` 
* 构建指定模块及其依赖`mvn clean install -pl account-email -am`
* 构建指定模块及依赖它的模块`mvn clean install -pl account-email -amd`
* 在完整的反应对顺序上指定从哪个模块开始构建`mvn clean install -rf account-email`
* 在`-pl -am / -pl -amd`基础上还能用`-rf`再次剪裁

## 常用命令
新建一个空白工程  

    mvn archetype:create -DgroupId=com.tiemei -DartifactId=p1 -DpackageName=com.tiemei.p1
    建web工程加-DarchetypeArtifactId=maven-archetype-webapp


下载依赖源码  

    mvn dependency:sources
    mvn eclipse:eclipse -DdownloadSources=true
查看依赖树  

    mvn dependency:tree

发布

    mvn clean source:jar javadoc:jar deploy 同时发布源码和doc文档
    mvn clean deploy 只发布calss jar包

## 常用插件
maven-compiler-plugin  
1. maven2.1 默认用jdk 1.3来编译，这个版本不支持注解，compiler插件可以制定jdk版本为1.6
2. windows平台默认GBK编码，如果工程编码为utf-8，需要再compiler插件中指定
