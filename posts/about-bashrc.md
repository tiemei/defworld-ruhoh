---
title: .bashrc .bash_profile等文件区别
date: '2013-06-28'
description: bashrc
categories: 'linux'
tags: 

---
**/etc/profile**  
全局，用户第一次登录执行一次  
会从/etc/profile.d目录的配置文件中搜集shell的设置.
  
**/etc/bashrc**  
全局，bash shell配置，一般由/etc/profile调用  
 
**~/.bash_profile**  
似有，用户登录(ssh远程登陆/本地新开一个terminal)时执行一次
一般会调用~/.bashrc  
  
**~/.bashrc**  
似有，每次执行bash命令时自动调用，或者非交互方式进入bash时运行  
  
**~/.bash_logout**  
当每次退出系统(退出bash shell)时,执行该文件.  

