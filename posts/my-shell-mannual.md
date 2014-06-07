---
title: 近一年来shell命令积累(随时更新)
date: '2013-06-12'
description: shell
categories: "linux"
tags: shell

---
参考:  
[linux命令五分钟系列博文，写的简洁易懂](http://roclinux.cn/)  


- [awk](#awk)
- [echo](#echo)
- [expect](#expect) 对命令行提示输入自动输入，如密码验证
  

- [cut](#cut)      字节/字符/域（特殊字符分隔）的按行切割
- [paste](#paste)  多文件按行连接/一个文件多行连接
  

- [split](#split) 按行/大小拆分文件
- [sed](#sed) stream editor,面向行处理，输出到标准输出
- [awk](#awk)
- [xargs](#xargs)
  

- [cat](#cat)
- [tac](#tac) 逆向输出文件
- [date](#date)
- [截取子串](#截取子串)
- [其他](#其他)
- [find](#find)
- [service](#service)
- [du](#du) disk usage，目录及文件大小
- [chkconfig](#chkconfig) 设置各个系统运行级别启动的服务
- [unmae](#uname)
- [tr](#tr) 标准输入到标准输出的字符串转译
- [压缩和解压](#压缩和解
- [shutdown](#shudown)
- [chsh](#chsh) choose shell
- [man](#man) 
- [who am i](#who am i)- [wc](#wc) 字符/字节/最长行字符数/行数 统计
- [vim](#vim) 字符替换
- [sort](#sort)
- [ln](#ln)
  

- [sar](#sar)  收集、汇报与存储CPU/IO..信息
  

- [netstat](#netstat) 和netstat说再见！官方都不维护了！
- [ss](#ss) Socket Statistics
- [iproute2](#iproute2) 于TCP/IP协议的流量控制工具套装,取代原来包含netstat的套装net-tools
- [实时观察文件变化](#实时观察文件变化)
- [ps](#ps)
- [shuf](#shuf) 打乱文本的行
- [rpm](#rpm) 
- [grep](#grep)
- [screen](#screen)
  

- [script/scriptrelay](#script/scriptrelay) 终端演示
- [pushd/popd]#(#pushd/popd) 目录压栈出栈
- [touch](#touch)

#### http相关

- [wget](#wget)
- [curl](#curl)

### <a id="awk"><font color="green">awk</font></a>

[入门一](http://124.16.151.186/docs/linux/script/Awk-1index.shtml#12)_

- 
- awk '{ print }' file # 对每一行都执行{}内的命令
- $0表示整行，$1...表示第几几个字段，分割符可制定-F ':'
- '{ print $1 " " $3 }' 这样才会在两个字段间插入空格
- 外部脚本 awk -f myscript.awk file

        # myscript.awk
        BEGIN {
          FS=":" # 不同的是，这里没有用-F参数设置分隔符
        } 
        { print $1 }

- BEFIN END 在处理所有行之前和之后做一次的事，前者通常设置FS，打印页眉，或初始化全局变量，后者执行最终计算或输出结尾摘要
- 规则表达式和快 

        /foo/ { print }，指数出有foo的行，/[0-9]+\.[0-9]*/ { print },浮点数 

- 表达式和块

        $1 == "fred" { print $3 } 只有第一字段是fred时才输出
        可用： == < > <= >= !=  ~ !~
        $5 ~ /root/ { print $3 } 匹配时才

        () &&/|| () {}

- 条件语句

        上个例子等价于：
        {
            if ( $5 ~ /root/ ) {
               print $3
            }
        }
        复杂例子：
        {
            if ( $1 == "foo" ) {
                if ( $2 == "foo" ) {
                    print ""
                } else {
                    print ""
                }
            } else if ( $1 == "bar" ) {
                print ""
            } else { 
                print ""
            }
         }
        
         ! /match/ {}
         等价 
         {
             if ( $0 !~ /match/ ) {
             }
         }


- 数值运算
 
         BEFIN { x=0 }
         /^$/  { x=x+1 }
         END   { print "Blank line num: " x }

- 字符串到数字的转换自动发生

         x="1.01"
         x=x+1
         print x # 2.01
         
         bash需要放到$()中，而python需要显示转换
         awk { print ($1^2)+1 } ，如果不能成功转换默认0

- 众多运算符

         + - * / 
         ++ --（前后都支持）
         += -= *= /=
         ^ % ^= %=

- 字段分隔符

         FS 可指定任意长度
         FS="\t+" 一个或多个
         FS="[[:space:]+]" 其实默认就是一个或多个空格tab
         FS="foo[0-9][0-9][0-9]"

- 其他特殊变量

         NF 字段数量，当前记录中的字段数量
         NR 记录号，比如当前行号

   
###### 四行合一行
    [code:1:bde308dcc7]cat filename
    111111111
    222222222
    333333333
    444444444
    555555555
    666666666
    777777777
    888888888
    999999999
    awk '{if (NR%4==0){print $0} else {printf"%s ",$0}}' filename
    111111111 222222222 333333333 444444444
    555555555 666666666 777777777 888888888
    999999999

###### 按特殊符号分隔 $NF表示最后一个匹配到的
        cat file1
    > 111.222
    > 3333.44
    cat file1 | awk -F '.' '{printf "%s %s",$1,$2}'
    > 111 222
    > 3333 44

###### split内建命令

    awk 'BEGIN{begno="1,2,3";split(begno,str,",");print str[1],str[2],str[3]}'
    > 1 2 3

###### awk其他教程
    [csdn博文](http://suo.iteye.com/blog/1319525)  
[awk实例](http://124.16.151.186/docs/linux/script/Awk-1index.shtml)  



### <a id="echo"><font color="green">echo</font></a>
###### 输出换行
    echo "line1\nline2"
    > line1\nline2
    echo -e line1\n\line2
    > line1
    > line2


### <a id="expect"><font color="green">expect</font></a> 
[wiki](http://en.wikipedia.org/wiki/Expect#Examples)  

### <a id="cut"><font color="green">cut</font></a> 
[网友博文：《cut命令》-linux命令五分钟系列之十九](http://roclinux.cn/?p=1328)  
    
    # 定位格式
    3 /3-5 / 4- / -2

    # 按字节切割
    [rocrocket@rocrocket programming]$ who
    rocrocket :0           2009-01-08 11:07
    rocrocket pts/0        2009-01-08 11:23 (:0.0)
    rocrocket pts/1        2009-01-08 14:15 (:0.0)

    [rocrocket@rocrocket programming]$ who|cut -b 3-5,8
    croe
    croe
    croe

    # 按字符切割
    [rocrocket@rocrocket programming]$ cat cut_ch.txt
    星期一
    星期二
    星期三
    星期四
    [rocrocket@rocrocket programming]$ cut -b 3 cut_ch.txt
    �
    �
    �
    �
    [rocrocket@rocrocket programming]$ cut -c 3 cut_ch.txt
    一
    二
    三
    四

    # -n选项
    [rocrocket@rocrocket programming]$ cat cut_ch.txt |cut -nb 2
     
    [rocrocket@rocrocket programming]$ cat cut_ch.txt |cut -nb 1,2,3
    星
    星
    星
    星

    # 按域（特殊字符分隔）切割
    # cut的-d选项的默认间隔符就是制表符，如果分隔符为空格，用' '(只允许一个空格，因为cut只允许间隔符为一个字符)
    [rocrocket@rocrocket programming]$ cat /etc/passwd|head -n 5
    root:x:0:0:root:/root:/bin/bash
    bin:x:1:1:bin:/bin:/sbin/nologin
    daemon:x:2:2:daemon:/sbin:/sbin/nologin
    adm:x:3:4:adm:/var/adm:/sbin/nologin
    lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
    [rocrocket@rocrocket programming]$ cat /etc/passwd|head -n 5|cut -d : -f 1
    root
    bin
    daemon
    adm
    lp

    # 制表符和空格区分，制表符显示为"\t"，空格原样显示
    sed -n l tab_space.txt

    # 不足
    -d 选项多空格分隔符不支持


### <a id="cat"><font color="green">cat</font></a>
###### 用cat合并文件技巧
[IBM Dev博文](http://www.ibm.com/developerworks/cn/linux/l-tip-prompt/l-tiptex2/)  

### <a id="date"><font color="green">date</font></a>
 
[参考](http://xingfujie.blog.51cto.com/2791569/637223)
`date –I –d '-n day'`   (可以得到N天前的日期，格式为YYYY-MM-DD)
`date –d '-n day' "+Y%m%d"`       (可以得到你天前的日期，格式为YYYYMMDD)

`date –I –d '+n day'`   (可以得到N天后的日期，格式为YYYY-MM-DD)
`date –d '+n day' "+%Y%m%d"`       (可以得到你天后的日期，格式为YYYYMMDD)  
`date +%s.%N` (%s是秒，%N是纳秒，需要ms可以对输出做一个计算，基本可作为唯一字符戳)  

#### 时间戳格式化日期互转
- `date -d @1287331200  "+%Y-%m-%d %M:%S"`
- `date -d "2010-10-18 00:00:00" +%s 输出时间戳
- 指定时间前后几天

       s=`date -d "2010-10-10 00:00:00" +%s` 
       s_new=`expr $s + 86400` # 加上一条的秒数
       date_new=`date -d @$s_new "+%Y-%m-%d"`




### <a id="截取子串"><font color="green">截取子串</font></a>


### <a id="其他"><font color="green">其他</font></a>
[linux下grep分析APACHE 服务器日志 命令集合](http://blog.sina.com.cn/s/blog_69516b7f01015j34.html)  

### <a id="find"><font color="green">find</font></a>
分几个维度查找：  
`find . -name file_name ` # 文件名  
`fine . -type d -name file_name` # 文件类型,f(普通文件),l(链接文件)  
`find . -user tiemei` # 用户/用户组  
`find . -perm 755` # 权限  
`find . -regex '.*b.*3'` # 正则过滤  
`find . -type f -name “*.abc” -exec cat {} \`  
                 # 输出找到项的内容， -exec 表示找到的项作为后面命令的参数， -ok 表示每个匹配项用户确认是否输出，{}表示find查找的结果。  
`find . -amin -5`  
                 # 访问过用amin，修改过用mmin，文件状态改变过用cmin  
                 # 精确到分钟的用amin,mmin,cmin，精确到天的用atime,mtime,ctime  
                 #  在5分钟之内的用-5，在5分钟以上的用＋5  
`find . -size +10000000c`  
                 # -size：表示文件大小，＋表示大于某个数，－表示小于某个数。c表示单位是字节，你可以将c换成k,M,G  

`find . -maxdepth 1 -name “*.c”`  # 不想深入子目录，只想在当前目录  
  
#### 递归修改目录、文件权限

`find -type d -exec chmod 755 {} \;`  
`find -type d|xargs chmod 755`  
`find -not -type d -exec chmod 644 {} \;`  
`find -not -type d|xargs chmod 644`  
  

### <a id="xargs"><font color="green">xargs</font></a>

[关于xargs，你可能不知道的](http://heikezhi.com/yuanyi/things-you-didnt-know-about-xargs)  
[Xargs by example](http://sidvind.com/wiki/Xargs_by_example)  
[一个例子，找到所有txt结尾文件并在文件中间插入特殊字符](http://stackoverflow.com/questions/10803296/modifying-replace-string-in-xargs)  

* 默认从管道传递过来的值是放在命令最后的
* If utility is omitted, echo(1) is used
* `-I``find . -name "*" | xargs -I {} cp {} /home/ads` -I将管道传递过来的输出作为参数，用符号{}代替
* `-J`用法和`-I`有些区别
* `-n`如果要执行的命令只接收两个参数，`echo {0..9} | xargs -n 2`
* `-L`将多个非空白行合并成后接命令的一次参数输出，与`-n`冲突，同时出现以最后一个为准
* `-t`展示将要被执行的命令
* `-s`最终执行的命令的字符最大长度
* `-R`最大用于替换的-I指定的replstr数据，例如`echo {1..10} | xargs -I {} -n 1 -R 1 echo {} {}`
只替换echo后前一个{}
* find + xargs 常用来批量处理文件，不过文件名或者目录有空格会出现问题，因为xargs会按照空格来
划分输入，给find加上`-print0`选项告诉find使用NUL(\0)来分割结果，同时给xargs加上`-0`
* `-P`并行调用例如`time echo {1..5} | xargs -n 1 -P 5 sleep`
* `-p`交互确认当前某个命令是否执行
* `ls / |xargs -I% sh -c 'n=%;echo "[$n]"'` 使用sh -c执行复杂的处理


### <a id="service"><font color="green">[service](http://roclinux.cn/?p=47)</font></a>
`service httpd start/stop/restart/reload`(重新载入配置)  
位于/sbin目录下，一脚本命令，动作是去/etc/init.d目录下寻找相应服务。  
不是所有发行版都提供  

### <a id="du"><font color="green">du</font></a>
`du -h .` # 当年前目录下所有目录以及子目录的大小
`du -ch abc | tail -n 1` # 只想看abc目录大小，-c 表示最后计算出所有目录大小之和  
`du -sh abc`  
`du -h -max-depth=0 abc`  
`du -ah abc` # 列出abc及其子目录下所有目录和文件大小  
`du -h -exclude='*xyz*'` # 不包含xyz字符串的目录  
`du -0h abc` # -0不换行，直接输出下一个  

### <a id="chkconfig"><font color="green">chkconfig</font></a> 
 # 增加一个服务步骤  
- 服务脚本放在/etc/ini.d/下  
- `chkconfig -add servicename` 增加，此时服务会被在/etc/rc.d/rcN.d中赋予K/S入口了  
- 设置服务默认启动等级  

`chkconfig -list` # 系统所有服务启动情况  
`chkconfig -list mysqld` # mysqld服务设置情况  
`chkconfig -level 35 mysqld on` # 只在等级3 5 执行，off表示关闭  
`chkconfig mysqld on`   
                    # 各等级(2 3 4 5)为on  
                    # 0 关机; 1 单用户模式;2 无网络连接的多用户命令行模式；  
                    # 3 有网络连接的多用户命令行模式；4 不可用；5 带图形界面的多用户模式；6 重新启动  
`chkconfig -del sevicename` # 删除  

### <a id="uname"><font color="green">uname</font></a> 
`uname -a` 打印所有系统信息  
`uname -s` 打印内核名称  
`uname -n` 打印网络节点主机名  
`uname -r` 答应内核发信版本号  
查看系统版本号方法还有：  

- `cat /proc/version`  
- `cat /etc/redhat-release`
- `cat /etc/issue`
    
### <a id="tr"><font color="green">tr</font></a> 
`cat filename | tr u n`  将u替换为n  
`cat filename | tr -d abc` 将所有a/b/c字符删去  
`cat filename | tr [:lower:] [:upper:]`  小写变大写  
`[:alnum:]`  所有字符数字  
`[:alpha:]` 表示所有的字母  
`[:blank:]` 表示所有空格  
`[:digit:]` 表示所有数字  
`[:graph:]` 表示所有可打印字符，但不包括空格  
`[:print:]` 表示所有可打印字符，包括空格  

### <a id="压缩和解压"><font color="green">压缩和解压</font></a>
**RAR**  
去下面的网站下载一个RAR命令  
http://www.rarlab.com/download.htm  
解压后，在rar文件夹make  
 
`rar x解压 rar文件`  

**ZIP**  
适用Linux， Windows以及Mac OS。压缩率不是很高，而tar.gz和tar.gz2在压缩率方面做得非常好。  
压缩一个目录：  
 `zip -r archive_name.zip directory_to_compress` 压缩目录  
 `zip -r a.zip abc.txt dir1` 压缩目录和文件到一起
解压一个zip文档：  
`unzip archive_name.zip `   
`unzip abc\?.zip` 解压缩多个文件（支持正则）abc1.zip，abc2.zip和abc3.zip...  
`unzip -j music.zip ` 内部子目录内容全部解压到一级目录  
查看：  
`unzip -v large.zip`  内部内容  
`unzip -t large.zip`  下载的文件是否完整  



**tar**  
打包一个目录：  
 `tar -cvf archive_name.tar directory_to_compress[ dir2]`单个/多个目录打包    
 `tar -cvf my.tar file1[ file2]`单个/多个文件打包
如何解包：  
`tar -xvf archive_name.tar.gz`  
上面这个解包命令将会将文档解开在当前目录下面。当然，你也可以用这个命令来捏住解包的路径  
`tar -xvf archive_name.tar -C /tmp/extract_here/`  


**tar.gz**  
它在压缩时不会占用太多CPU的，而且可以得到一个非常理想的压缩率。  
`tar -xzvf abc.tar.gz/abc.tgz`  解压缩  
上面这个解包命令将会将文档解开在当前目录下面。当然，你也可以用这个命令来捏住解包的路径：  
`tar -zxvf archive_name.tar.gz -C /tmp/extract_here/`
`tar -xzvpf abc.tar.gz` 解压保留原被压缩文件权限  
`tar -czvf dirabc.tar.gz dirabc` 压缩文件夹  

`tar -ztvf abc.tar.gz` 仅仅是查看  
`tar -xzvf abc.tar.gz def/xyz.txt` 只提取一个文件  


`tar -xvf abc.tar` 解开包（并没有解压缩动作）  


**gizp : gz**  
`gzip -d xyz.gz`     
`gzip -1 abc.tar` 压缩的最快，或用--fast,压缩比有1~9,9也可为--best  
 

**TAR.BZ2**  
bunzip2/bzip2  
所有方式中压缩率最好的。就意味着，占用更多的CPU与时间。  
压缩:  
`tar -jcvf archive_name.tar.bz2 directory_to_compress`  
用这个命令来捏住解包的路径：  
`tar -jxvf archive_name.tar.bz2 -C /tmp/extract_here/`  

**bzip2**  
`bzip2 abc.txt` 压缩后得到abc.txt.bz2,原文件被删除  
`bzip five.bz2 abc1.txt abc2.txt abc3.txt dir1 dir2` 压缩多个目录/文件到一个文件    
`bzip2 abc1.txt abc2.txt` 分别压缩多个文件
`bunzip2 abc.txt.bz2 / bzip2 -d abc.txt.bz2` 解压缩，原文件自动删除  
`bunzip2 abc1.txt.bz2 abc2.txt.bz2` 解压缩多个  



tar它只是一个打包的命令。tar.gz与tar.bz2都是在tar命令中调用了别的压缩命令实现了一个压缩的功能，实际是实行一个先打包后压缩的过程，而且，tar.gz与tar.bz2只是一个公认的一个通行后缀而已，并不是一种压缩格式（真正实现压缩的是gzip与bzip2，这是后话。），tar.gz在很多时候，也会被简写成tgz。  
如果你在fedora下工作，你会发现bunzip2是bzip2的一个符 号连接。但bunzip2和bzip2的功能却正好相反。bzip2是用来压缩文件的（当然如果使用特殊函数也是可以用来解压，用选项-d可以实现），而 bunzip2是用来解压文件的（相当于bzip2 -d）。类比，还有zip和unzip、gzip和gunzip、compress和uncompress。  

### <a id="shutdown"><font color="green">shutdown</font></a>
`shutdown -h 23:00`  
`shutdown -h +15`  
`shutdown -k now "Server would shutdown in 15mins."` 吓唬一下  

### <a id="chsh"><font color="green">chsh</font></a>
`chsh -l` 当前机器安装了那些shell  
`cat /etc/shells` 同上  
`echo $SHELL` 正在使用的shell  
`chsh -s /bin/zsh` 修改当前shell，重启shell窗口生效，修改的效果是改了文本文本文件,`cat /etc/passwd | grep ^tiemei`  

### <a id="man"><font color="green">man</font></a>
用man查看帮助信息，同名的情况，需要引入分类：  

- 1 用户命令
- 2 系统调用
- 3 C语言库函数
- 4 设备或特殊文件
- 5 文件格式和规则
- 6 游戏及其他
- 7 宏、包及其他杂项
- 8 系统管理员相关的命令

[一个查看man信息的网站](http://www.linuxmanpages.com/)  
`manpath`命令可查看man搜索帮助信息的路径顺序  
`makewhatis` 重新建立man数据库  
`man -k roc` 等效于 `apropos`(一个脚本文件) ,像搜索引擎一样，给一个关键字列出所有相关信息  

### <a id="who am i"><font color="green">who am i</font></a>
whoami 当前操作用户,即有效用户=euid(effective user id)  
who am i 等效 who -m 登录用户，即linux术语里的实际用户=uid(user id)   
who 当前登录的所有用户  
深入：  
[用标准IO实现linux的who命令](http://hi.baidu.com/xinbao125/item/0efe5d9a728cdb88581461eb)
[实现linux的whoami命令](http://hi.baidu.com/xinbao125/item/a521ac313f3779159cc65eeb)  

### <a id="wc"><font color="green">wc</font></a>

    [rocrocket@rocrocket programming]$ cat wc1.txt
    1 2
    34 5
    你好
    [rocrocket@rocrocket programming]$ wc -c wc1.txt # 字节数，空格换行都算，UTF-    8编码，汉字3字节    
    16 wc1.txt    
    [rocrocket    @rocrocket programming]$ wc -m wc1.txt # 字符数，一个汉字一个字符    
    12 wc1.txt    
    [rocrocket    @rocrocket programming]$ wc -l wc1.txt # 行数    
    3 wc1.txt    
    [rocrocke    t@rocrocket programming]$ wc -L wc1.txt # 最长行长度，换行不在内    
    4 wc1.txt    
    [rocrocket@rocrocket programming]$ wc -w wc1.txt # 字数（word），34 是一个字
    5 wc1.txt


    [rocrocket@rocrocket programming]$ wc wc1.txt
    3  5 16 wc1.txt
    输出信息依次是：行数 字数 字节数 文件名称。

tab制表符，这个符号比较特殊，当使用-L时，制表符算7个字符（这要依据一个制表符的长度而定，在我的系统中一个制表符算7个空格长度）。而当使用-w时，制表符和空格没有两样，都作为字的间隔来看待。当用-c时，一个制表符也就算一个字符  

### <a id="vim"><font color="green">vim</font></a>
`sed -n '/\sat\s/!p'` !p打印取反  
`s/abc/xyz/` 光标所在行第一项替换  
`s/abc/xyz/g` 光标所在行所有项替换  
`1,$s/abc/xyz/g` 第一行到最后一样替换，`.`表示当前行  
`:s#http://roclinux\.cn/index\.php#http://www\.sohu\.com#` 间隔符号用#，!也行  
`:1,$s/\([0-9]\{4\}-[0-9]\{2\}-[0-9]\{2\}\)/'\1'/g`  日期格式（XXXX-XX-XX）的字符串两侧加上一对单引号  
`:r !sed -n '1,1p' file` 读入另一个文件第一行   

`num ctrl+w +/-`增减宽度  
`num ctrl+w >/<`减小高度

`n>>` `n<<` 多行缩进，或进入v模式按>/<  

### <a id="sed"><font color="green">sed</font></a>
按行处理，读一行到零食缓冲区（模式空间，pattern space）,处理完输出到屏幕。  
`sed cmd file` 命令格式  

- `sed -i ".bak" 's/abc/ac/g' file` 直接在文件中替换字符串。.bak为备份文件名，为""时不会备份。mac
下必须加上""，linux下可省略。
- `/2/d` 删除含有字符'2'的行

        [rocrocket@rocrocket programming]$ cat roc.txt
        test 1
        test2
        testtest
        XtestX
        BBtest
        [rocrocket@rocrocket programming]$ sed ‘/2/d’ roc.txt
        test 1
        testtest
        XtestX
        BBtest
        
- `s/:.*$//` 将第一个':'只有的内容替换为空白

        [rocrocket@rocrocket programming]$ head -n 5 /etc/passwd
        root:x:0:0:root:/root:/bin/bash
        bin:x:1:1:bin:/bin:/sbin/nologin
        daemon:x:2:2:daemon:/sbin:/sbin/nologin
        adm:x:3:4:adm:/var/adm:/sbin/nologin
        lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
        
        [rocrocket@rocrocket programming]$ head -n 5 /etc/passwd|sed ‘s/:.*$//’
        root
        bin
        daemon
        adm
        lp

- `/4/p` 输出含有'4'的行，sed默认先输出原始行，加上 `-n` 只输出想要的行

        [rocrocket@rocrocket programming]$ cat roc.txt
        1
        2
        3
        4
        5
        [rocrocket@rocrocket programming]$ sed ‘/4/p’ roc.txt
        1
        2
        3
        4
        4
        5
        [rocrocket@rocrocket programming]$ sed -n ‘/4/p’ roc.txt
        4

- command部分包括，
    - 确定范围部分 
        - 指定行数 `3,5` `5,$` `2,+4p` 第2行后面再加4行
        - 用模式匹配进行指定，`/^[dD]/`匹配行首不是以d/D开头的行
    - 处理方式部分
        - d 删除行
        - p 打印该行
        - r 读取指定文件的内容
        - w 写入指定文件
        - a\: 在下面插入新行内容
    - 举例:
        - `sed -n '10,20p' test` 显示test文件的10到20行
        - `sed '/^[dD]/s/x/X/g' test` 将所有以d/D开头的行小写x替换成大写X输出
        - `sed 's/..$//' test`删除每行最后两个字符
        - `sede 's/..//' test` 删除每行前两个字符

- `s/B.*/&2008/`在匹配到'B.*'后面插入2008,`&`表示被匹配的部分

        [rocrocket@rocrocket programming]$ cat mysed.txt
        Beijing
        London[rocrocket@rocrocket programming]$ sed ‘s/B.*/&2008/’ mysed.txt
        Beijing2008
        London

- `s/\(Beijing\)\(.*\)\(Beijing\)/\12008\2\32008/` 在行的第一个Beijing后加上2008，最后一个Beijing后加上2008，注意'()'匹配的部分存储到\1 \2 \3中

        [rocrocket@rocrocket programming]$ cat mysed.txt
        Beijing Beijing Beijing Beijing
        London London London London
        [rocrocket@rocrocket programming]$ sed ‘s/\(Beijing\)\(.*\)\(Beijing\)/\12008\2\32008/’ mysed.txt
        Beijing2008 Beijing Beijing Beijing2008
        London London London London

- `sed -n '1~2p' file` 打印偶数行 `sed -n '0~2p' file` 打印奇数行

- [sed不支持非贪婪算法](http://roclinux.cn/?p=1362)
- `‘/2005/,/2007/p’` 匹配有2005的行到有2007的行结束

        [rocrocket@rocrocket programming]$ cat mysed.txt
        Beijing 2003
        Beijing 2004
        Beijing 2005
        Beijing 2006
        Beijing 2007
        Beijing 2008
        Beijing 2007
        [rocrocket@rocrocket programming]$ sed -n ‘/2005/,/2007/p’ mysed.txt
        Beijing 2005
        Beijing 2006
        Beijing 2007

- `-e`接多个cmd，从前到后执行

        [rocrocket@rocrocket programming]$ sed -n -e ’1,2p’ -e ’4p’ mysed.txt
        Beijing 2003
        Beijing 2004
        Beijing 2006

- `-f`将cmd部分写到file中


        [rocrocket@rocrocket programming]$ cat callsed
        /2004/,/2006/p
        [rocrocket@rocrocket programming]$ sed -n -f callsed mysed.txt
        Beijing 2004
        Beijing 2005
        Beijing 2006

- cmd中 `r  w`用法

        [rocrocket@rocrocket programming]$ cat ins.txt
        ====China====
        [rocrocket@rocrocket programming]$ sed ‘/2005/r ins.txt’ mysed.txt
        Beijing 2003
        Beijing 2004
        Beijing 2005
        ====China====
        Beijing 2006
        Beijing 2007
        Beijing 2008
        [rocrocket@rocrocket programming]$ sed ‘/200[4-6]/w new.txt’ mysed.txt; cat new.txt
        Beijing 2004
        Beijing 2005
        Beijing 2006

- cmd中 `i`用法  在特定行上面插入特定内容

        [rocrocket@rocrocket programming]$ sed ‘/2004/i\China’ mysed.txt
        Beijing 2003
        China
        Beijing 2004
        Beijing 2005
        Beijing 2006
        Beijing 2007
        Beijing 2008

- cmd中`y`用法，替换特定字符
        
        # sed -n 's/eijing/EIJIGN/g' mysed.txt一样的效果
        [rocrocket@rocrocket programming]$ sed ‘y/eijng/EIJNG/’ mysed.txt
        BEIJING 2003
        BEIJING 2004
        BEIJING 2005
        BEIJING 2006
        BEIJING 2007
        BEIJING 2008

- `/2004/{n;y/eijng/EIJNG/;}`对匹配行的下一行进行处理，n使移到下一行

        [rocrocket@rocrocket programming]$ sed ‘/2004/{n;y/eijng/EIJNG/;}’ mysed.txt
        Beijing 2003
        Beijing 2004
        BEIJING 2005
        Beijing 2006
        Beijing 2007
        Beijing 2008
        [rocrocket@rocrocket programming]$ sed ‘/200/{n;y/eijng/EIJNG/;}’ mysed.txt
        Beijing 2003
        BEIJING 2004
        Beijing 2005
        BEIJING 2006
        Beijing 2007
        BEIJING 2008

- [强势扩展](http://roclinux.cn/?p=1585) 复杂命令/h x G/...

        # 同时写多个命令，用三种方式：分号，-e，-f
        sed -n -e ‘=;p’ myfile.txt  # =表示行号，打印每行行号，然后答应每行内容
        sed -n -e ‘=’ -e ‘p’ myfile.txt
        sed -n -f mycommands.sed myfile.txt
        
        # h x G 
        h表示将模式空间中的内容拷贝到”保持空间(hold space)”中。“保持空间”是sed中除了模式空间外的另一个暂存数据的地方
        x表示交换模式空间和保持空间中的内容
        G表示将保持空间的内容追加到模式空间中内容的尾部

        # s///g和s///
        个都是sed里的替换命令，有g，如果一行里出现两次匹配串，那么所有的匹配串都会被替换；否则值只替换第一个匹配串

        # sed -e ’s/72;/72,next_val=0×11111111;/;/@/{h;s/test/next/g;x;G}’ fmt_vuln.c
        将文件中每行出现‘72;’替换为‘72,next_val=0×11111111;’；接着，如果有@符号，则将本行宝贝到hold空间，做一个全局的替换，交换保持空间和模式空间内容，并将保持空间内容追加到模式空间结尾（增加了一行）


[SED单行脚本快速参考](http://sed.sourceforge.net/sed1line_zh-CN.html)  

- `sed G`在每一行后面增加一空行
- `sed 'G;G'` 增加两个空行
- `sed '/^$/d;G'`删除空行并在每行后面加上一空行
- 


### <a id="awk"><font color="green">awk</font></a>
`awk 'NR%2' file` 提取奇数行
`awk 'NR%2==0' file ` 提取偶数行  

### <a id="sort"><font color="green">sort</font></a>
- `-u` 去除重复行
- `-r` 默认升序，改成降序
- `sort file > otherfile`不生效，用`sort file -o otherfile`
- `-n`默认按字符排序，改成按数值排序
- `-t -k选项`

        [rocrocket@rocrocket programming]$ cat facebook.txt
        banana:30:5.5
        apple:10:2.5
        pear:90:2.3
        orange:20:3.4
        
        分隔符:，按第二列排序
        [rocrocket@rocrocket programming]$ sort -n -k 2 -t : facebook.txt
        apple:10:2.5
        orange:20:3.4
        banana:30:5.5
        pear:90:2.3

-  其他选项

        -f 忽略大小写
        -c 检查文件是否乱序，如果乱序输出第一个乱序行信息，返回1
        -C 如果乱序，不输出，返回1
        -M 以月份来排序,比如JAN小于FEB等等
        -b 忽略每一行前面的所有空白部分，从第一个可见字符开始排序

#### `-k`选项大作战

    $ cat facebook.txt
    google 110 5000
    baidu 100 5000
    guge 50 3000
    sohu 100 4500

    分隔符' ',先按第三列数字方式降序排，再按第二列数字方式排序
    $ sort -t ' ' -k 3nr -k 2n facebook.txt
    baidu 100 5000
    google 110 5000
    sohu 100 4500
    guge 50 3000

###### 完整语法： `[ FStart [ .CStart ] ] [ Modifier ] [ , [ FEnd [ .CEnd ] ][ Modifier ] ]`   
- 由`,`分成start/end两部分，end部分可省略
- Modifier放置类似n r的选项
- CStart从一个域的哪个字符开始，省略表示从第一个字符开始
- FStart从哪个域开始
- CEnd省略表示到域尾

    按第一个域排序，从第二字符开始
    $ sort -t ' ' -k 1.2 facebook.txt
    baidu 100 5000
    sohu 100 4500
    google 110 5000
    guge 50 3000

    按第一个域第二个字符排序，如果相同按第三个域数字方式降序
    $ sort -t ' ' -k 1.2,1.2 -k 3,3nr facebook.txt
    baidu 100 5000
    google 110 5000
    sohu 100 4500
    guge 50 3000

###### Modifier还可以包括：  
- n
- r
- b 忽略本域的签到空白符号
- d 对本域按字典顺序排序（只考虑空白和字母）
- f 忽略带小些
- i 忽略不可打印字符
- u
- h 会识别单位，4G 大约699M
- s 阻止同一个域相同，不进行进一步比较

        cat
        00 2
        00 1
        sort -t ' ' -k 1,1 demo -s
        00 2
        00 1
        sort -t ' ' -k 1,1 demo
        00 1
        00 2


###### -u -k 联合使用 
<font color="red">可以推测，某个域没法比较相等，从第一个域开始升序排序！！！</font>

    $ cat facebook.txt
    google 110 5000
    baidu 100 5000
    guge 50 3000
    sohu 100 4500

    取出重复行，guge也没有留下
    $ sort  -k 1.1,1.1 -u facebook.txt
    baidu 100 5000
    google 110 5000
    sohu 100 4500

    -u只会删除所以-k选项都相同情况，如果存在sina 100 4500就会被去掉
    $ sort -n -k 2 -k 3 -u facebook.txt
    guge 50 3000
    sohu 100 4500
    baidu 100 5000
    google 110 5000

    第二个域的第二个字符开始到第三个域的第一个字符结束
    原来“跨域的设定是个假象”，sort只会比较第二个域的第二个字符到第二个域的最后一个字符的部分，
    而不会把第三个域的开头字符纳入比较范围。当发现00和00相同时，sort就会自动比较第一个域去了。
    可以推测，某个域没法比较相等，从第一个域开始升序排序！！！
    $ sort -n -k 2.2,3.1 facebook.txt
    guge 50 3000
    baidu 100 5000
    sohu 100 4500
    google 110 5000

[参考原文](http://roclinux.cn/?p=1472)  
[sort参数详解](http://www.linuxso.com/command/sort.html)  

### <a id="split"><font color="green">split</font></a>

`split -l 3 input_file prefix.` 3行拆分input_file,目标文件前缀prefix  
`-b -k -m..`按大小拆分文件  

### <a id="ln"><font color="green">ln</font></a>
[Linux ext2文件系统 / 硬链接软链接](http://vbird.dic.ksu.edu.tw/linux_basic/0230filesystem.php)

- `ln srcfile hardlink`硬链接,只能同一个文件系统，只能指向文件（）。实际是在这个目录的block增加了一条记录，指向srcfile的innode位置。由于删除文件，就是删除其目录block中的内容（需要改目录你有w权限啦~），故你删除原来的srcfile，hardlink照样存在。
- `ln -s srcfile/srcdir softlink` 软链接，实际是创建了新的文件softlink,该文件的block内容指向srcfile/srcdir的inode号码。如果你删除srcfile/srcdir，该softlink失效啦。

### <a id="paste"><font color="green">paste</font></a>

    
    [rocrocket@rocrocket programming]$ cat p3.txt
    I
    II
    III

    # 默认用制表符连接
    [rocrocket@rocrocket programming]$ paste p1.txt p2.txt p3.txt
    1    a    I
    2    b    II
    3    c    III
    [rocrocket@rocrocket programming]$ paste p3.txt p2.txt p1.txt
    I    a    1
    II   b    2
    III  c    3

    # 指定连接符号
    [rocrocket@rocrocket programming]$ paste -d '*' p3.txt p2.txt p1.txt
    I*a*1
    II*b*2
    III*c*3

    # 一个文件多行连接
    [rocrocket@rocrocket programming]$ paste -s -d "*" p3.txt p2.txt p1.txt
    I*II*III
    a*b*c
    1*2*3

    # 
    $ cat fa.txt
    20
    60
    90
    12
    $ cat fb.txt
    60
    90
    12
    14
    $ cat fc.txt
    70
    90
    80
    12
    $ paste -d"+-" fa.txt fb.txt fc.txt
    20+60-70
    60+90-90
    90+12-80
    12+14-12

    # 计算结果
    $ paste -d"+-" fa.txt fb.txt fc.txt| while read line
    do
    echo -n "($line)=" # -n不换行
    echo $line | bc
    done
    
    Output:
    (20+60-70)=10
    (60+90-90)=60
    (90+12-80)=22
    (12+14-12)=14
    $ paste fa.txt fb.txt fc.txt | awk '{print "("$1"+"$2"-"$3")="$1+$2-$3}'
    
    Output:
    (20+60-70)=10
    (60+90-90)=60
    (90+12-80)=22
    (12+14-12)=14

    # Only list the elements which have rpm > 3000
    $ cat element.txt
    Element E1:
    rpm=2300
    Element E5:
    rpm=8900
    Element E3:
    rpm=5000
    Element E4:
    rpm=1200
    $ paste - - < element.txt | awk '$NF>=3000 {print $1}' FS=[:,=]
    Element E5
    Element E3
    
[待续](http://unstableme.blogspot.com/2009/01/linux-paste-command-good-examples-uses.html)


### <a id="sar"><font color="green">sar</font></a>
##### CPU信息
- `sar 时间间隔 输出次数` 单位s,如果时间间隔0，输出从开机到现在为止的平均值；输出次数为0，永远输出下去。
- `sar 5 6 -o sys_info`输出到那文件，不过格式没法cat，用`sar -f sys_info`看吧。-o没有filename，默认到var/log/saDD文件里，其中DD表示当天的日期数字
- `sar -P ALL 1 2`试试多核，`sar -P 5 1 1`只看第六个核
##### 内存/网络/IO..信息呢？
   
       -b：报告I/O使用情况以及传输速率。（只适用于2.5及之前的内核，所以新内核有可能不支持这个选项）
   
       -B：报告“页”使用情况
   
       -c：报告进程创建情况
   
       -d：报告每一个块设备的使用情况
       （当你使用时，你会发现在DEV列有类似dev1-7格式的字符串，
       其中1代表设备的主序号，n代表设备的从序号，
       而且rd_sec/s列和wr_sec/s列的单位都是512bytes，也就是512B，也就是0.5KB）
   
       -I：汇报中断情况
   
       -n：汇报网络情况
   
       -P：设定CPU
   
       -q：汇报队列长度和负载信息
   
       -r：汇报内存和交换区使用情况
   
       -R：汇报内存情况
   
       -u：汇报CPU使用情况
   
       -v：汇报i节点、文件和其他内核表信息
   
       -w：汇报系统上下文切换情况
   
       -x：可以针对某个特定PID给出统计信息，
       可以直接指定进程ID号；
       也可以指定为SELF，这样就是检测sar进程本身；
       如果设定为ALL，则表示汇报所有系统进程信息。
   
       -X：汇报特定PID的子进程的信息
   
       -y：设定TTY设备的信息。

       -A 就相当于-bBcdqrRuvwWy -I SUM -n FULL -P ALL这么一堆选项了

##### 网络相关

    # sar命令使用-n选项可以汇报网络相关信息，可用的参数包括：DEV、EDEV、SOCK和FULL
    # 如果你使用DEV关键字，那么sar将汇报和网络设备相关的信息，如lo，eth0或eth1等，例如：
    $ sar -n DEV 1 2
    Linux 2.6.9      10/17/2009
    
    12:10:49 AM     IFACE   rxpck/s   txpck/s   rxbyt/s   txbyt/s   rxcmp/s   txcmp/s  rxmcst/s
    12:10:50 AM      eth0     63.64      0.00   4072.73      0.00      0.00      0.00      0.00
    12:10:50 AM      eth1     30.30     13.13   2907.07   1234.34      0.00      0.00      0.00
    12:10:50 AM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00
    IFACE：就是网络设备的名称；
    rxpck/s：每秒钟接收到的包数目
    txpck/s：每秒钟发送出去的包数目
    rxbyt/s：每秒钟接收到的字节数
    txbyt/s：每秒钟发送出去的字节数
    rxcmp/s：每秒钟接收到的压缩包数目
    txcmp/s：每秒钟发送出去的压缩包数目
    txmcst/s：每秒钟接收到的多播包的包数目

    # 如果你使用EDEV关键字，那么会针对网络设备汇报其失败情况
    $ sar -n EDEV 1 3
    Linux 2.6.9     10/17/2009
    
    12:15:06 AM     IFACE   rxerr/s   txerr/s    coll/s  rxdrop/s  txdrop/s  txcarr/s  rxfram/s  rxfifo/s  txfifo/s
    12:15:07 AM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
    12:15:07 AM      eth0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
    12:15:07 AM      eth1      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
    rxerr/s：每秒钟接收到的损坏的包的数目
    txerr/s：当发送包时，每秒钟发生的错误数
    coll/s：当发送包时，每秒钟发生的冲撞(collisions)数（这个是在半双工模式下才有）
    rxdrop/s：由于缓冲区满，网络设备接收端，每秒钟丢掉的网络包的数目
    txdrop/s：由于缓冲区满，网络设备发送端，每秒钟丢掉的网络包的数目
    txcarr/s：当发送数据包时，每秒钟载波错误发生的次数
    rxfram/s：在接收数据包时，每秒钟发生的帧对齐错误的次数
    rxfifo/s：在接收数据包时，每秒钟缓冲区溢出错误发生的次数
    txfifo/s：在发送数据包时，每秒钟缓冲区溢出错误发生的次数

    # 如果你使用SOCK关键字，则会针对socket连接进行汇报
    $ sar -n SOCK 1 3
    Linux 2.6.9       10/17/2009
    
    12:27:29 AM    totsck    tcpsck    udpsck    rawsck   ip-frag
    12:27:30 AM        90        41         4         0         0
    12:27:31 AM        90        41         4         0         0
    12:27:32 AM        90        41         4         0         0
    Average:           90        41         4         0         0
    totsck：被使用的socket的总数目
    tcpsck：当前正在被使用于TCP的socket数目
    udpsck：当前正在被使用于UDP的socket数目
    rawsck：当前正在被使用于RAW的socket数目
    ip-frag：当前的IP分片的数目

    # 如果你使用FULL关键字，相当于上述DEV、EDEV和SOCK三者的综合




##### 其他好玩的
- sar实时汇报机器性能情况 `sar -o monitor.res 间隔时间 运行次数 >/dev/null 2>&1 &`
- `-e hh:mm:ss选项`某个特定时间结束，只能用在读取(-f)或写入(-o)信息文件时才可用

**或许你平时还在使用free,ifconfig或者top，从今以后，试试sar吧**

### <a id="wget"><font color="green">wget</font></a>
##### wget是什么？

- 支持HTTP、HTTPS和FTP协议的下载，其中也包括通过HTTP代理的下载  
- wget能够跟踪HTML文件和XHTML文件,傻傻的去下载外链站点的内容
- wget支持慢速网络下载和不稳定网络的下载。当因为网络不稳定而导致下载失败时，wget会重试直到把目标文件下载下来为止
- wget支持断点续传功能
- wget是使用getopt函数来处理选项和参数,支持长格式的选项和短的
- 选项有参数时，你甚至可以在短选项和参数之间不加空格。比如-o log可以写成-olog
- 连续使用了多个短选项，且这些短选项不需要加参数，那么可以将他们组合在一起。比如-d -r -c可以写成-drc
- [还有好多呀](http://roclinux.cn/?p=1542)

#### demo
`wget -c "www.baidu.com" -O reNameFile -o wget.log` 重命名，并输出运行日志  

##### [wget突破robots.txt限制](http://roclinux.cn/?p=1546)

##### [wget目录相关选项](http://roclinux.cn/?p=2505)

##### [大文件](http://roclinux.cn/?p=1561)

##### [其他选项](http://roclinux.cn/?p=2107)

### <a id="curl"><font color="green">curl</font></a>

* `curl http://defworld.com` GET获得一个页面内容
* `curl -I http://defworld.com` 获得http head
* `curl -v http://defworld.com` 打印http交互详细过程
* 支持POST方式
* `curl -F upload=@localfilename -F press=OK [URL]` 支持文件上传
* `curl -u name:password www.secrets.com` 认证
* `curl -A “Mozilla/4.0 (compatible; MSIE 5.01; Windows NT 5.0)” [URL]` 模拟指定浏览器
* `curl -D head_cookies http://bbs.it580.com` `curl -b head_cookies http://bbs.it580.com`保存并使用cookie
* `curl -x proxy.test.org:80 http://bbs.it580.com` 使用代理访问


### <a id="netstat"><font color="green">netstat</font></a>
![替代netstat的命令](/Dropbox/notes/a/netstat_saygb.png)

### <a id="ss"><font color="green">ss</font></a>
- ss快不是一点点，尤其在sock连接很多，它利用到了TCP协议栈中tcp_diag
- [如果系统没有预装ss，如何装](http://roclinux.cn/?p=2420)  


**【场景一：我想查看当前服务器的网络连接统计】**  
    
    $ ss -s
    Total: 295 (kernel 312)
    TCP:   48 (estab 1, closed 31, orphaned 0, synrecv 0, timewait 0/0), ports 13
    
    Transport Total     IP        IPv6
    *         312       -         -
    RAW       0         0         0
    UDP       2         2         0
    TCP       17        12        5
    INET      19        14        5
    FRAG      0         0         0

**【场景二：我想查看所有打开的网络端口】**  
    
    $ ss -l
    Recv-Q Send-Q           Local Address:Port               Peer Address:Port
    0      128                         :::webcache                      :::*
    0      128                         :::http                         :::*
    0      128                         :::snapenetio                      :::*
    0      128                          *:snapenetio                       *:*
    0      50                           *:8531                          *:*
    0      9                           :::ftp                          :::*
    0      9                            *:ftp                           *:*
    0      128                          *:ddi-tcp-1                       *:*
    0      100                        ::1:smtp                         :::*
    0      100                  127.0.0.1:smtp                          *:*
    0      128                          *:8541                          *:*
    0      128                  127.0.0.1:entextxid                       *:*
    0      50                           *:12421                         *:*
    0      10                           *:amqp                          *:*
    0      128                          *:12521                         *:*
    0      50                           *:mysql                         *:*

    如果使用-pl参数的话，则会列出具体的程序名称。你会在输出中看到类似于这样的内容：
    ("nginx",15786,6)
    从中可以知道，某个socket连接是属于nginx程序的，nginx程序的PID是15786

**【场景三：我想查看这台服务器上所有的socket连接】**  

    很简单，直接使用-a选项即可列出所有网络连接。
    ss -a
    如果只想查看TCP sockets，那么使用-ta选项；
    如果只想查看UDP sockets，那么使用-ua选项；
    如果只想查看RAW sockets，那么使用-wa选项；
    如果只想查看UNIX sockets，那么使用-xa选项

### <a id="iproute2"><font color="green">iproute2</font></a>
![iproute工具集替代掉net-tools工具集](/Dropbox/notes/a/net-tool_ip.png)
![iproute2工具集](/Dropbox/notes/a/iproute2.png)

### <a id="实时观察文件变化"><font color="green">实时观察文件变化</font></a>
[一个帖子](http://bbs.chinaunix.net/thread-1257647-1-1.html)
- `watch -n 1 cat file.log` 实时观察cat file.log的最后几行结果
- `tail -f file.log` 末尾几行变动
- 
### <a id="ps"><font color="green">ps</font></a>
`ps -eLf | grep java | wc`

### <a id="shuf"><font color="green">shuf</font></a>
自己下载源码编译安装：http://mirror.yongbok.net/nongnu//shuffle/shuffle-0.9b.tar.gz  

### <a id="rpm"><font color="green">rpm</font></a>

### <a id="grep"><font color="green">grep</font></a>
- `grep -n root /etc/passwd` 输出行号
- `grep -v bash /etc/passwd` 反向输出
- `grep -c false /etc/passwd` 统计匹配行数
#### 正则
- `grep ^root /etc/passwd` root开头的行
- `grep :$ /etc/passwd` :结尾的行
- `grep '\<PATH' file` 有某个单词是以PATH开头 
- `grep 'PATH\> file'` 有某个单词是以PATH结尾
- `grep -w 'PATH' file` 有某个独立的单词，当然前后都是空格啦
- `grep [yf] file` 包行y或f
- `grep '\<c...h\>'` 某个单词，c开头h结尾，中间还有三个字符
- `grep -E 'AAA|BBB' urfile` -E用egrep的方式解释正则pattern

### <a id="script/scriptrelay"><font color="green">script/scriptrelay</font></a>
1. open terminal 1 : $ `mkfifo fd1`
2. open terminal 2 : $ `cat fd1`
3. back terminal 1 : $ `script -f fd1`
4. write in terminal 1, terminal 2 will see same
5. exit in terminal 1 : $ `exit` 

### <a id="pushd/popd"><font color="green">pushd/popd</font></a>
- `dirs` 可现实当前目录，金丹目录堆栈只有一个目录时
- `pushd /path/to/dir1` 将目录压入目录堆栈，此时运行`dirs`输出目录堆栈，从左到右编号0...n
- `pushd +3` 迅速切换到#3目录,此时目录堆栈的顺序会变化
- `popd +3` 弹出并跳转到该目录

### <a id="touch"><font color="green">touch</font></a>
`touch -t [[CC]YY]MMDDhhmm[.SS] file`

### <a id="screen"><font color="green">screen</font></a>
[参考1](http://www.ibm.com/developerworks/cn/linux/l-cn-screen/)  
[参考2](http://serverfault.com/questions/155851/run-gnu-screen-from-script)  

* `screen -S name` 创建一个screen session，并命名name
* `screen -r name` 重新进入screen session
* `screen -ls` 列出所有screen session
* `screen -wipe`清扫无法连接的session
* `screen -d -m -S name` 创建一个开始就处于断开模式的session
* `screen -S name -X zombie qr` 设置cmd结束时，screen窗口不直接退出
* `screen -S name -X exec cmd` 在id为name的screen session的编号为0的窗口执行命令cmd
* `screen -S name -X screen cmd` 在session中新建一个窗口并执行cmd
* `C-a d` 退出screen session
* `C-a c` 创建一个新的shell窗口并切换到该窗口
* `C-a w` 显示所有窗口列表
* `C-a k` 杀掉当前窗口
* `C-a p``C-a n` 前一个窗口，后一个窗口 



