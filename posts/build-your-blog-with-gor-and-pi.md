---
title: 树莓派搭建个人静态博客
date: '2013-05-12'
description: 这里是描述内容
categories: "funny"
tags: pi

permalink: '/2013-05/build-your-blog-with-gor-and-pi.html'
---

## 我在做一件什么事？
作为一个coder，一个自己的博客是‘标配’丫  
云主机的控制力度实在有限，自己租服务器实在没必要，毕竟只有几篇静态的博文而已。  
用自己的笔记本或台式机搭，又略显大材小用，最近电费不便宜。  
树莓派的静态博客实在合适不过:  

    树莓派   400+
    域名一年 100-
    电费     忽略把，功率实在低
    实用性   Mem 256M CPU armv6l 跑静态的博客妥妥的



## 整体思路
1. 准备树莓派(+电源+SD卡)
2. 准备域名(net.cn)
3. 准备家里用的ADSL网络
4. 用[DNSPod](https://www.dnspod.cn/)的DDNS支持，将家里路由器对外ip动态绑定
到上面准好的域名
5. Pi的MAC地址在路由器里设置静态路由
6. 设置路由器80端口的所有tcp/udp包直接转发到树莓派的ip
7. 在树莓派上安装Nginx，并将[Gor](https://github.com/wendal/gor)或者
[Ruhoh](http://ruhoh.com/)编译好的静态博客内容放到Nginx能找到的位置
8. start DNSPod DDNS shell，你可到这里[https://support.dnspod.cn/Support/api](
https://support.dnspod.cn/Support/api)找合适的工具，我用[https://gist.github.com/chuangbo/833369](
https://gist.github.com/chuangbo/833369)
9. start nginx

## 操作步骤
已经有同好做了很不错的教程:  

* [利用GOR在Raspberry Pi上搭建博客](http://dqylyln.dyndns.org/2013/pi-gor.html)
* [在Pi和Github上搭建自己的个人博客](http://hugozhu.myalert.info/2013/02/27/%E5%9C%A8Pi%E5%92%8CGithub%E4%B8%8A%E6%90%AD%E5%BB%BA%E8%87%AA%E5%B7%B1%E7%9A%84%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2.html)
* [Installing Go on the Raspberry Pi](http://dave.cheney.net/2012/09/25/installing-go-on-the-raspberry-pi)

## 我遇到的坑

* 8G的SD卡装了个系统只剩200M的空间
  
        原因：系统预设只有2G的空间
        解决：sudo raspi-config => expand-rootf 

* 到google下载go源码太耗时
      
        原因：pi的配置比较低
        解决：另外的电脑下载完scp到pi上

* pi上编译的go源码test跑不通，运行go报错

        原因：google的开发人员提交的代码没有做好测试；
              Pi是ARM平台不能用主干的代码，需要用TIP分支
        解决：hg clone -u tip https://code.google.com/p/go
              hg revert -r 15749:e92503ce815b --all (我是使用15749:e92503ce815b 这个版本没问题)

* pi自带的vim不怎么好用

        原因：版本不行
        解决：sudo apt-get install vim

* 安装gor提示GOPATH没有设置

        .bashrc中加上:
          export GOPATH=$HOME/workspace/go
          export PATH=$GOPATH/bin:$PATH

* go编译好了，命令行还不能用go命令

        .bashrc中加上:
          export GOROOT=/path/to/your_go_root_dir
          export PATH=$GOROOT/bin:$PATH

ok ! enjoy it!
