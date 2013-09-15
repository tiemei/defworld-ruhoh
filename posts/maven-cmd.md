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

