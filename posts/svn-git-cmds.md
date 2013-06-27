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

![git提交状态](http://farm4.staticflickr.com/3701/9147517685_85e9e01925.jpg)  

* `git add file/dir``git commit -m 'commit log'`上图很形象的说明了git命令和文件所处状态的变化关系。
* `git commit -a -m "commit log"` 将add和commit动作合并，不过只作用域变化的文件，对新增文件无效
* `git log`查看commit历史 `git log --pretty=oneline --abbrev-commit`查看commit历史，简明格式

  
![git回退](http://farm6.staticflickr.com/5331/9149824940_25f19bf055.jpg)  

* `git checkout file/dir` 用index中文件覆盖working tree中文件
* `git reset HEAD file`   用commit中文件覆盖index中文件,HEAD为commit log前几位字符(唯一就行)
* `git revert HEAD`       undo某次commit动作，你也可以undo更老些的commit
* `git show HEAD`         show diff from one commmit compared to its parent
* `git diff HEAD1..HEAD2` 两个commit之间的变化

  
![git tag](http://farm8.staticflickr.com/7443/9149898002_3c3513af25.jpg)  

* `git tag wokring 3720b35``git tag broken` 给某次commit加tag
* `git diff working..broken` tag可以被用在HEAD出现的任何地方

  
branches merget.. 待补充

### 配置

* `git config --global color.ui auto`
* `git config user.name "Bob"`
* `git config user.email "bob@example.com"`
* `git config --global user.name "Bob"`
* `git config --global user.email "bob@example.com"`
* 直接编辑.git/config

