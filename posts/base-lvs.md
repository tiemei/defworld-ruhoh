---
title: 基础 - lvs
date: '2014-11-18'
description:
categories: 'Base'
---

基础系列一，说说对lvs的理解。   

## 首先理解为什么要有lvs

![典型的应用结构]()  


### lvs分类


### 一个简单的lvs配置

```bash
ipvsadm -A -t 10.101.86.153:80 -s rr
ipvsadm -a -t 10.101.86.153:80 -r 10.101.86.153:8080 -m
# 临时打开网卡间转发
echo 1 >/proc/sys/net/ipv4/ip_forward
```