---
title: 系统环境准备
date: '2014-11-03'
description: 
categories: 'linux'
---

# Redhat

## ruby

```bash
wget 'http://curl.haxx.se/ca/cacert.pem'
curl --cacert cacert.pem -sSLk https://get.rvm.io | bash -s stable
source ~/.profile
source ~/.rvm/scripts/rvm
rvm install 1.9.2
rvm use 1.9.2 
```

参考：  

* ![http 使用curl发起https请求](http://www.cnblogs.com/ainiaa/archive/2011/11/08/2241385.html)
* ![如何快速正确的安装 Ruby, Rails 运行环境](https://ruby-china.org/wiki/install_ruby_guide)

# Mac


