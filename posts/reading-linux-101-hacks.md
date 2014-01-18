---
title: 《linux 101 hacks》笔记
date: '2014-01-18'
description:
categories: 'reading notes'
---

## cd 

**CDPATH**  

默认`cd logs`进入当前目录的logs目录，设置了这个变量，优先进入这个变量
指向的目录的下对应的文件夹。可用冒号分隔指定多个。  

**返回多级上级目录**  

    ~/.bashrc
    
    alias ..='cd ..'
    alias ..2='cd ../..'
    alias ..3='cd ../../..'
    
    或者这种方式：
     
    alias ..='cd ..'
    alias ...='cd ...'

    或者

    alias cd...="cd ../.."

    等等方式

**创建目录并进入该目录**  

```
function mkdircd () { mkdir -p "$@" && eval cd "\"\$$#\""; }
```
`$@`所有参数，`$#`mkdircd函数传入的参数长度，例如mkdircd hello，
eval将$1这个字符串解释为变量值hello  

**cd -** 在最近访问的两个目录间切换  

**目录堆栈**  

    * dirs:显示目录栈
    * pushd:将目录压入目录栈 
    * popd:将目录弹出目录栈

**智能纠正cd命令错误目录名**  

`shopt –s cdspell`

## 其他

**grep**  

* `grep -ri str dir` 在目录dir的所有子目录超找包含字符str的内容的地方
* `grep -ril str dir` 只显示文件名

**find**  

* `find / -type f -size +100M` 找到/目录下大于100M的文件
* `find . -mtime +60` 最近60天没有被修改过的文件
* `find . –mtime -2`  最近2天被修改过的文件
* 删除 

         find / -type f -name *.tar.gz -size +100M -exec ls -l {} \;
         find / -type f -name *.tar.gz -size +100M -exec rm -f {} \;

* 打包

         find /home/jsmith -type f -mtime +60 | xargs tar -cvf /tmp/`date '+%d%m%Y'_archive.tar`

**join**  

合并两个文件，按相同列。


**xargs**  

将一个命令的输出传递给下一个命令的输入  

* `cat url-list.txt | xargs wget –c`
* `find /etc -name "*.conf" | xargs ls –l`

**uniq**  

* `sort namesd.txt | uniq –cd` -c显示出个数，-d只列出重复项

**ac**  

* `ac -d` 按天显示登陆主机时间
* `ac -p` 显示所有用户的时间
* `ac -d jiadongkai` 指定用户时间

**提示符**  

* `PS1=”\u@\h \w>"` \u用户 \h主机名 \w当前目录
* `PS2="continue-> "` 多行命令的默认提示符>修改为continue-> 
* `PS3="Select a day (1-4): "`select命令的默认提示符号

![PS3](http://farm8.staticflickr.com/7331/12007421406_6031688bca.jpg)  


* `PS4='$0.$LINENO+ '` 改变调试脚本(直接加上`set -xv`/`bash -xv script`)时的默认前缀，$0当前脚本的名字，$LINENO当前行号
* `PROMPT_COMMAND="date +%k:%m:%S"` Bash 在显示 PS1 之前先执行 PROMPT_COMMAND 定义的内容
* 可以在提示符中显示任何命令的输出

**提示符颜色**  

* `export PS1="\e[0;34m\u@\h \w> \e[m "` 改变提示符的前景颜色为亮蓝色
* `export PS1="\e[0;34m\e[47m\u@\h \w> \e[m "`定制前景色和背景色
* 可使用tput更加丰富颜色展示
* 已有的 PS1 变量可使用

**压缩**  

略过  

**history**  

* `export HISTTIMEFORMAT='%F %T '` history显示时间戳
* `ctrl + r`搜索历史命令
* `!n` 执行history中第几条命令
* `!ps`执行最近的已ps开头的历史命令
* 历史命令默认保存在.bash_history，用HISTFILE改变
* 自动去除重复历史命令

**fdisk** 管理分区  
**mke2fsk** 管理文件系统  
略  

**crontab**  

![crontab](http://farm3.staticflickr.com/2838/12008804724_a300fcfbe1.jpg)  

* `1,2,3,4``1-4` 每一项可以指定多个值，两种写法相同
* `*/5 * * * * ` 每隔5分钟
* `crontab file` 用file替代当前的crontab内容
* `echo "5 * * * * touch ni" | crontab -` 替换crontab内容

**.bash..**  

* 执行顺序:

![bash执行顺序](http://farm4.staticflickr.com/3803/12013970874_ff612e3f50.jpg)  

/etc/bashrc在/etc/profile中被调用，~/.bashrc在~/.bash_profile中被调用。

![非登录bash执行顺序](http://farm6.staticflickr.com/5498/12014506206_8f06f37a46.jpg)  

**echo**  

* `echo 1 \; 2` 输出`1 ; 2`
* 单引号按字符原意输出
* 双引号变量会被解释

        除了以下字符,双引号会去除所有字符的特殊含义
        $ 参数替换
        ` 后引号
        \$ 美元符号
        \" 双引号
        \\ 插入反斜杠

**从文件中读取指定域**  

![while读取文件指定域](http://farm6.staticflickr.com/5541/12014671226_006e3e3ca2.jpg)  

**top**  

显示进程的绝对路径按c  

**ps**  

* `ps axuf`/`ps –ejH`/`pstree` 显示进程树
* `ps U oracle`查看指定用户进程

**lsof**列出系统中所有打开的文件  

* `lsof -u user` 查看指定用户打开的文件
* `lsof file` 查看使用文件的所有用户

**nice**  

程的 nice 值决定进程需要多少处理器时间。nice 值的取值范围是是:-20 到 20。一
个具有-20 的 nice 值的进程有很高的优先级  
`renice`可以给正在运行的进程设置调度优先级  

**tee**  

* `ls | tee file` 同时将输出写入标准输出和文件，
* `crontab -l | tee crontab-backup.txt | sed 's/old/new/' | crontab –`
* `ls | tee -a file` 默认覆盖文件改成追加
* `ls | tee f1 f2 f3` 写入多个文件

## 系统监控命令系列

待续



