---
title: ssh密钥自动登陆+ssh-agent使用+单机多个ssh账号
date: '2013-06-30'
description: ssh
categories: 'linux'
---
参考：  
[使用SSH CONFIG](http://www.lainme.com/doku.php/blog/2011/01/%E4%BD%BF%E7%94%A8ssh_config#生成ssh证书)  
[使用ssh公钥密钥自动登陆linux服务器](http://7056824.blog.51cto.com/69854/403669/)  
  
## 生成ssh key
`ssh -t rsa`  
`ssh -t rsa -C "jiadongkai@gmail.com"` 注释用邮箱地址  
将公钥追加到需要登录机器的~/.ssh/authorized_keys，如果无法登陆将~/.ssh/known_hosts
对应行删掉  
注意需要保证authorized_keys文件只有当前用户有写权限  

## 用ssh-agent省掉passphrase输入
1. 准备ssh-agent需要的环境变量，选择下面的一种方法即可  

        eval `ssh-agent` 只在当前登陆窗口生效
        echo 'eval `ssh-agent`' >> /etc/profile 每次登陆生效
2. 将ssh key对应的passphrase添加到ssh agent高速缓存中  

        ssh-add ~/.ssh/private_key_name
        你可已查看缓存中内容

3. ssh-agent默认打开，你可以关掉`ssh-agent -k`

## 你有这些特殊需求嘛？ 多个ssh帐号、使用非标准端口
用ssh config解决，详见`man ssh_config`  
编辑`~/.ssh/config`：  

    Host pi
      HostName defworld.com
      User pi
      IdentityFile ~/.ssh/id_rsa

    Host github
      HostName github.com
      User git
      IdentityFile ~/.ssh/gmail

任何使用`pi@defworld.com`的地方用`pi`代替，例如`ssh pi@defworld.com`用`ssh pi`代替，
会自动查找指定的密钥`id_rsa`进行权限验证。于是，你可以单机保存多个密钥。  

