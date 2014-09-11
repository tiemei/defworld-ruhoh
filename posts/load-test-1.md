---
title: 压力测试小结 - 工具
date: '2014-08-08'
description:
categories:

type: draft
---

# 工具

## top

可以显示的以下信息：  

* cpu
    - us 用户占用
    - sy 内核占用
    - ni 改变过优先级的进程占用百分比
    - id 空闲
    - wa i/o等待占用
    - hi 硬中断（Hardware IRQ）占用
    - si 软中断（Software Interrupts）占用
* 各进程的详细状态
    - PID 
    - USER
    - PR 进程优先级
    - NI nice值，优先级，值越小优先级越高
    - VIRT 进程使用的虚拟内存总量，单位KB，VIRT=SWAP+RES
    - RES 进程使用的、未被换出的物理内存大小，单位KB，RES=CODE+DATA
    - SHR 共享内存大小，单位KB
    - S 进程状态。D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程
    - %CPU 上次更新到现在的CPU时间占用百分比
    - %MEM — 进程使用的物理内存百分比
    - TIME+ — 进程使用的CPU时间总计，单位1/100秒
    - COMMAND

可以控制：  

* 显示完整的命名
* 指定用户名
* 指定进程
* 默认按照CPU占用排序，可以改变排序项
* top命令执行过程中有些交互命令可以使用
* 可将当前的配置写入~/.toprc文件中

## vmstat

定时统计系统指标。  

* r 表示运行队列(就是说多少个进程真的分配到CPU)
* b 阻塞进程数
* swpd 虚拟内存使用
* free 空闲物理内存大小
* buff Linux/Unix系统是用来存储，目录里面有什么内容，权限等的缓存
* cache 直接用来记忆我们打开的文件,给文件做缓冲
* si 每秒从磁盘读入虚拟内存的大小
* so 每秒虚拟内存写入磁盘的大小

## lsof

查看文件系统打开文件状况。  

```
COMMAND：进程的名称
PID：进程标识符
USER：进程所有者
FD：文件描述符，应用程序通过文件描述符识别该文件。如cwd、txt等
TYPE：文件类型，如DIR、REG等
DEVICE：指定磁盘的名称
SIZE：文件的大小
NODE：索引节点（文件在磁盘上的标识）
NAME：打开文件的确切名称
```

```
lsof  filename 显示打开指定文件的所有进程
lsof -c string   显示COMMAND列中包含指定字符的进程所有打开的文件
lsof -u username  显示所属user进程打开的文件
lsof +d /DIR/ 显示目录下被进程打开的文件
lsof +D /DIR/ 同上，但是会搜索目录下的所有目录，时间相对较长
lsof -d FD 显示指定文件描述符的进程
lsof -i 用以显示符合条件的进程情况
lsof -i[46] [protocol][@hostname|hostaddr][:service|port]
            46 --> IPv4 or IPv6
            protocol --> TCP or UDP
            hostname --> Internet host name
            hostaddr --> IPv4地址
            service --> /etc/service中的 service name (可以不只一个)
            port --> 端口号 (可以不只一个)
```

如果一个文件还处于被一个进程打开的状态，即使被删除也可以被回复，原理是/proc/pid/..目录实际上对应一个进程在内存中对应的空间，我们可以从这里看到缓存在内存中的数据。  

## iostat

《iostat来对linux硬盘IO性能进行了解》http://www.php-oa.com/2009/02/03/iostat.html  
《通过IOStat命令监控IO性能》http://huoding.com/2011/07/13/91  
《用iostat分析fio性能问题》http://www.atatech.org/articles/14188  

## strace

用来跟踪所有的系统调用和时间。可以做以下几件事：  

* 看系统打开了那些文件
* 统计一个时间段内正在运行的程序的system call状态
* 跟踪一个服务器连接不上的原因

##  systemtamp

一开始需要监控内核程序运行状态，需要开发人员在内核源代码中加入调试代码，然后重新编译安装内核，整个过程繁琐困难。之后Kprobe机制，不需要重新编译内核，即可加入调试信息。systemtamp封装了Kprobe，提供了更易用的api。  
不只是可以知道所有内核调用的状态，还可以知道用户空间的一些动作。  


## 火焰图 

## tsar




# 实战例子

## 磁盘写入/读取速度上限是多少？

### 用dd命令来测试读写速度

dd命令：  

* if=FILE read from FILE instead of stdin
* of=FILE writ to FILE instead of stdout
* bs=BYTES force ibs=BYTES and obs=BYTES
* ibs=BYTES read BYTES bytes at a time，即一次读入缓冲区的字节数
* obs=BYTES write BYTES bytes at a time，即一次写入缓冲区的字节数

* 测试/目录所在磁盘的纯写速度`sudo time dd if=/dev/zero bs=1024 count=1000000 of=/1Gb.file conv=fdatasync`。

        没有加conv=fdatasync实际上只是将数据读入读缓冲中，并不是最终全部写到磁盘的速度。
        加上oflag=dsync每次读入bs=1024byte大小的数据，都会写到磁盘，也就是说写缓冲区没起作用。

* 测/目录所在磁盘的纯读速度：`dd if=/kvm/ftp/other/1Gb.file bs=64k |dd of=/dev/null`
* 测读写速度：`sudo dd -if=/1Gb.file of/1Gb.file.bak bs=64K`

### 用hdparm测磁盘读写速度

* `hdparm -T /dev/sda`测试磁盘缓存读写速度，不走device本身
* `hdparm -t /dev/sda`测试磁盘的本身读写速度

### 根据接口和硬件指标判断读写速度

从以下一点看读写速度：  

1. 接口：一开始是ATA 133MB/s，升级为SATA 1.0 150MB/s ，SATA 2.0 300MB/s，SATA 3.0 600MB/s。
2.磁盘转速：普通5400转的hdd读写速度一般在70-80MBs，普通7200转的hdd读写速度大概在100-120M/s，而普通ssd的读速度有400-500MB/s，写入速度也有200MB/s。加上正常情况下的读写是随即读写，如果需要读取的内容或者写入的地方相隔较远，磁头花在寻道（锁定磁道约4ms）、平均延时（找到磁道上某个位置约2ms）的时间会很夸张。
3. SSD读写速度不均

## linux如何知道文件被哪个进程写？



## 



参考：  
《每天一个linux命令（44）：top命令》http://www.cnblogs.com/peida/archive/2012/12/24/2831353.html  
《 计算机中断/硬中断/软中断之理解(1)》http://blog.csdn.net/pxz_002/article/details/7327668  
《Linux vmstat命令实战详解》http://www.cnblogs.com/ggjucheng/archive/2012/01/05/2312625.html  
《正确用DD测试磁盘读写速度》http://blog.csdn.net/xrb66/article/details/7180919  
《Linux利用dd测试读取/写入磁盘速度》http://www.tsingpost.com/articles/201401/344.html  
《mac下的改装人生——关于ssd》http://www.cnblogs.com/wisejoker/p/3399828.html  
《SATA百科》http://zh.wikipedia.org/wiki/SATA  
《（总结）Linux的hdparm工具参数详解：硬盘检查、测速、设定、优化 》 http://blog.chinaunix.net/uid-22823163-id-3294012.html  
《硬盘原理与IO吞吐量计算》http://www.cnblogs.com/netflu/archive/2010/01/19/1651519.html  
《linux lsof详解》http://blog.csdn.net/guoguo1980/article/details/2324454  
《5 simple ways to troubleshoot using Strace》http://www.hokstad.com/5-simple-ways-to-troubleshoot-using-strace  
《内核探测工具systemtap简介》http://www.cnblogs.com/hazir/p/systemtap_introduction.html
《systemtap官网》http://sourceware.org/systemtap/  
《如何统计lua每个函数的执行时间占比》https://groups.google.com/forum/#!topic/openresty/bOwgPymXQzg  
《春哥的nginx systemtap工具集》https://github.com/openresty/nginx-systemtap-toolkit#sample-bt  
《ata systemtap资源》http://www.atatech.org/search?q=systemtap  



