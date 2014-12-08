---
title: mysql常用命令
date: '2014-11-26'
description:
categories: 'mysql'
---

* `mysqldump --set-gtid-purged=OFF -h host -u user_name -p  --databases database > ~/dropbox/work/db/database.sql` dump整个数据库到本地文件
* `mysql -h host -u user_name -p`登录数据库

## mysql dump
* `-d` 不dump数据
* `--tables` 指定哪几个表


* `show databases;`显示所有数据库
* `user database;`使用哪个数据库
* `show tables;`显示所有表
