---
title: 我的svn git常用命令及配置
date: '2013-06-27'
description: svn,git,常用命令
categories: 'tool'
tags:
  - svn
  - git

---
参考:  
[SVN常用命令](http://blog.csdn.net/sunboy_2050/article/details/6187464)  

## svn 
### 常用命令
* `svn co remote-svn-path local-dir --username tiemei`签出
* `svn cp base-svn-path new-svn-path -m "commit log"`新建分支
* `svn diff -r m:n path` 比较两个版本文件差异
* `svn diff -r m:n path |grep "Index:"` 比较两个版本间变化的文件
* `svn info path` 列出某个path svn信息
* `svn log path` 查看path提交log
* `svn log -l4`  查看前四个提交log
* `svn ls path`  列出某个路径下内容(远程)
* `svn cat /path/to/file` 打印某个文件内容
* `svn st path` 只输出状态有变化的文件
* `svn st -v path` 列出所有文件详细状态信息
* `svn add file/dir``svn ci m "comment log"` 添加文件，提交变动

### 配置
#### 排除
**全局排除**  
~/.subversion/conf文件中global-ignores  
**局部排除**  
命令行执行:`[TargetFolder]>svn propset svn:ignore *.suo .`  
或者直接写到文件.svnignore中，然后执行`[TargetFolder]>svn propset svn:ignore -F ignore.txt`  

## git
待补充
