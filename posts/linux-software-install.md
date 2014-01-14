---
title: 谈谈Unix-like上软件安装那些事
date: '2013-11-10'
description: linux soft
categories: "linux"
---

## Linux和Mac OS X
在大学刚听说linux时候，被一堆名词唬住了：CentOS,Ubuntu,RedHat等。我一开始用
Fedora13一直用到Fedora15，使用过程中遇到多次诡异问题，最后都定位到是Fedora发行
版的bug，变忍痛弃之。相对，使用率更高的Ubuntu成为我的第二选择，因为使用率高，出现
问题也能从网上获得更多帮助。从今年3月份开始，换成了现在的os x系统。  
如此多的选择，常常让入门者无所适从。  
linux发行版分为主要的三个分支：RedHat、Slackware和Debian，每一个分支都拥有一个
最具代表性的商业服务器级的发行版，它们是：RedHat Enterpries Linux，简称RHEL、
SUSE LinuxEnterprise，简称SUSE，和Ubuntu Server，简称Ubuntu。有分支的区分，是因为
后来的各种发行版是在这几个最初版本上修改而来的，虽然某些发行版实际上已经跟最初的
版本相差甚远了。很多大型互联网公司会选择REHL，因为它对kvm的支持。但REHL收费，除非
你自己下它的源码编译，不过CentOS就是REHL源码编译过来的，所以，直接上CentOS就好。  
那现在苹果机器搭载的os x系统处在什么位置呢？`Mac OS`基于Mach内核，这个内核的出现也是
为了弥补Unix的某些局限和不足，对Unix系统下的程序的直接移植也做到了较好的兼容性。`mac os x`
是一次内核层面的改造，核心名为`Darwin`，以`FreeBSD`源码和`Mach`微内核为基础。于是可知现在
我们使用的`Mac os x`衍生自`BSD`分支，而linux于1991被`Linus Benedict Torvalds`编写时，`386BSD`
还没有发布。如果真的要追溯linux和`mac os x`的血缘关系，恐怕要更远了。所以，如果你觉得
用惯了Ubuntu，换成Mac机有一些不习惯，那是不可避免的了。  

## Unix安装软件
### 源码安装

这里最原始的软件安装方式就是自己编译源码了，通常各源码提供方（例如nginx.org）会将自己源码
放到主页供世界各地使用者下载，这时下下来的源码都是经过打包压缩的，以`tar.gz``tar.bz2`等结尾。
解压后你就看到一堆纯文本的源码。  
接下来，我们需要编译器将源码编译成机器可识别的二进制格式，常用的C语言编译器有`gcc``cc`等。我们
通常会将编译、安装、卸载一个软件的所有步骤放到Makefile文件中。如果源码较大，且需要检测系统环境
动态决定具体的操作步骤时，我们可以借助`automake``autoconfig`这样的自动化工具（nginx不依赖，自己编码
实现这个过程）。所以，从源码安装软件通常分为三步：  

1. ./configure

        检查系统环境，比如检查你的系统是否支持epoll。生成Makefile文件

2. make 

        编译

3. make install 

        安装编译好的二进制文件到指定目录

### rpm和yum，dkpg和apt

以rpm为例，它是编译好的二进制文件，所以不同的发行版之间通常无法互通，设置同一发行版的不同版本也不兼容。
这时，通常与RPM包同时释放出的SRPM，自定编译安装。  
我们通常会在安装rpm包时，包名里见到这样的字符`i386,i586,i686,noarch,x86_64`。由于CPU的向下兼容性，实际上
i386基于能运行于所有的x86平台上，只是后面版本的rpm包进行过编译优化。  
rpm包安装前会自动检查系统环境的兼容性，以及与其他软件的依赖关系。并且会自己管理RPM文件的信息，便于升级、移除、查询等。  
yum相比rpm，解决了软件之间依赖问题。它集中收集rpm包内部记录的软件依赖数据，在你安装某个软件时，自动完成所依赖软件的安装。  

### SRPM的使用

SRPM在编译时会用到一些目录，以CentOS为例：  

![SRPM DIR](http://farm4.staticflickr.com/3757/10773016346_1220899b61.jpg)  
  
我么可以从SRPM开始安装软件（`--rebuid/--recompile`），可以从从源码开始安装软件。  
我们将准备好的源码放到SOURCES目录，将spec文件放到SPECS目录，然后运行rpmbuild命令即可将源码打包成rpm。  

## 常用命令

参考:  
[软件安装： RPM, SRPM 与 YUM 功能](http://vbird.dic.ksu.edu.tw/linux_basic/0520rpm_and_srpm_2.php)  

### rpm

**安装**  
  
* `rpm -ivh  rpm -ivh rp-pppoe-3.5-32.1.i386.rpm/http://website.name/path/pkgname.rpm`从本地或远程安装rpm包，
相关信息会写入`/var/lib/rpm/`目录
* `--replacefiles`强制覆盖本地已存在文件
* `--replacepkgs`强制替换已经安装过的rpm包
* `--force`上面两个的综合
* `--test` 不真的安装，检测是否有依赖问题
* `--justdb` 由於 RPM 数据库破损或者是某些缘故产生错误时，可使用这个选项来升级软件在数据库内的相关资讯。
* `--nosignature`  想要略过数码签章的检查时
* `--prefix 新路径`

**升级**  
  
* `rpm -Uvh rpm-pkg` 已有这个软件，升级它，没有就装上。`rpm -Fvh rpm-pkg`已有这个软件升级它，没有不装

**查询**  
  
* `rpm -q t-p4p-lego_qa` 查看某软件是否已安装
* `rpm -qa` 列出所有已安装软件
* `rpm -qi t-p4p-lego_qa` 列出该软件的详细资讯 (information)，包含开发商、版本与说明等
* `rpm -ql t-p4p-lego_qa` 列出该软件所有的文件与目录所在完整档名 (list)
* `-qc` 列出该软件的所有配置档 (找出在 /etc/ 底下的档名而已) 
* `-qd` 列出该软件的所有说明档 (找出与 man 有关的文件而已)
* `-qR` 列出与该软件有关的相依软件所含的文件 (Required 的意思)
* `-qf` 后面接的文件名称，找出该文件属於哪一个已安装的软件
* `-qp[icdlR]` 不是到已安装文件里找，而是指定一个本地rpm包查找(p 是 package 的意思)

**其他**  
  
* `rpm -e t-p4p-lego_qa` 需要按依赖从上往下移除
* `rpm --rebuilddb` 如果`/var/lib/rpm/`受损坏，用它恢复


参考:  
[wiki-mac_os](http://zh.wikipedia.org/wiki/Mac_OS)  
[wiki-mac_os_x](http://zh.wikipedia.org/wiki/OS_X)  
[wiki-mach](http://zh.wikipedia.org/wiki/Mach)  
[软件安装：原始码与 Tarball](http://vbird.dic.ksu.edu.tw/linux_basic/0520source_code_and_tarball.php)  
[软件安装： RPM, SRPM 与 YUM 功能](http://vbird.dic.ksu.edu.tw/linux_basic/0520rpm_and_srpm.php)  


