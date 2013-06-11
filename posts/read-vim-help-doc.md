---
title: 不设限的Vim，不设限的人生
date: '2013-06-01'
description:
categories: 'tool'
tags: vim

---
这篇是自己阅读vim help doc的一些笔记  
我对编辑器之争的态度是:  
  
>eclipse这样强大方便的编辑器固然需要，但vim是很好的补充。个人
>不准备用sublime text 2这样的神器，毕竟精力有限，专心吃透vim已经
>够，并且可以做的很好。  

## usr_02 Vim初步

* normal mode

        x  删除单个字符
        dd 删除一行
        J  删除当前行末的换行符

        o/O  进入插入模式，下一行/上一行起
        i/a  进入插入模式，当前光标前/后起
        
        : 进入cmd mode

        u  撤销一次编辑
        uu 撤销到原样(vim被配置在vi兼容模式下，可调整)  
        CTRL-R redo
        U  行撤销，可用u撤销U动作

        9k 上移9行
        3a! 执行三遍a!，即追加三个!

        ZZ 自动保存并退出

* insert mode

        
* cmd mode   

        :e! 放弃未保存的修改，重新编辑


### 其他

* 如何使用help doc

## usr_03 移动

        
1. 移动一个单词
       
    
        w / b   向后/前移动一个单词词首
        e / ge  向后/前移动一个单词末尾
        'iskeyword' 选项，设置vim认为的单词的组成部分
        W / B / E /gE 按空白字符分隔的字符串
        可使用数字前缀

2. 行移动

        ^ $
        0 移动到一行的第一个字符
        2$ 移动到下一行行尾

        nG 移动到n行
        50% 移动到50%的地方
        H / M / L 可见范围内移动

3. 移动到指定字符

        fa / Fa 向后/前移动到字符a
        ta / Ta 向后/前移动到字符a前一个字符
        ;  / ,  正/反向重复上面的命令

4. 括号匹配

        % 匹配(),第一次正向查找括号，第二次匹配
        'matchpairs' 设置可匹配的符号

5. 确定当前位置

        :set nu
        :set ruler 

6. 滚屏

        CTRL-U / CTRL-D 上/下半屏
        CTRL-E / CTRL-Y 上/下一行
        CTRL-B / CTRL-F 上/下一屏
        zt / zz /zb 光标移动到屏幕首/中/末
        'scrolloff' 光标上下总留有几行，按了z*命令才有效

7. 查找

        / / ? 正/反向查找
        :set noignorecase 忽略大小写
        * / # 光标所在位置单词查找
        g* / g# 部分匹配
        /the/>  / /\<the 匹配词尾/词首

        :set hlsearch 高亮匹配
        :nohlsearch 关闭本次高亮，下次查找将恢复
        :set incsearch 输入时就开始高亮
        :set nowrapscan 文首/尾即停止查找

8. 标记

        `` / '' 移动的两个位置间跳转
        CTRL-O / CTRL-I 较老/新的位置
        :jumps 列出你可以跳转的位置

        有名字的标记
        m[a-z] 标记当前位置为a-z
        m[A-Z] 标记是全局的，跨文件的(H头文件,M makefile,C代码文件)
        `{mark} 跳转到标记处
        '{mark} 到标记出行首
        :markds 查找所有标记
        
        特殊标记
        ' 跳转前的光标位置
        " 离开文件时的位置
        . 最后修改的位置
        [ 最后修改的开始位置
        ] 最后修改的结束位置

## usr_04 做小改动

## usr_07 编辑多个文件

1. 编辑多个文件

````vim
:edit foo.txt 关闭当前文件，打开另一个文件
:hide edit foo.txt 隐藏仓前该文件，编辑另一个文件

vim one.c two.c three.c vim会假定你想编辑全部
:next :wnext :2next 下一个文件
:previous :wprevious
:last :first

:args 正在编辑文件列表中哪个
:args five.c six.c seven.c 编辑另一个文件列表

:set autowrite 不是所有命令执行完都自动写

CTRL-^ 两个文件轮换
````

2. 保留原始文件/备份文件

````vim
:set backup  保留本次编辑前文件的备份
:set backupext=.bak 
:set backupdir 
:set writebackup 编辑退出后备份自动删除

:set patchmode=.orig 只保留一份最原始文件，不覆盖
````

3. 拷贝

````vim
v 可视模式，拷贝选中的区域
V 选中整行
CTRL-V 列模式，选中矩形块
Y / yy 一行
yaw 一个单词

"fyas 拷贝一个句子到寄存器f
"l3Y  拷贝3行到寄存器l
CTRL-Vjjww"by 宝贝一个区块到寄存器b

"wdaw 删除一个单词到寄存器w中

"fp 粘贴寄存器f中内容
:write >> logfile 直接写入
````

4. view file

````bash
vim -R file  :write 会被拒绝，不顾:write!还是会起作用
view file  同上

vim -M file 禁止修改,:write!也无效
:set modifiable 还是可以去掉禁止
:set write 同上
````

5. 修改文件名

````vim
:saveas move.c 重命名保存文件，并切换到move.c
:file move.c 效果差不多
````

## usr_08 分割窗口

````vim
:sp :vs
:only
:new :vnew 新文件

:3sp alpha.c 
n CTRL-W + / CTRL-W - 
{n}CTRL-W _ 指定高度
CTRL-W _ 扩大到尽可能大

CTRL-W h/j/k/l
CTRL-W t/b 最顶/最低

CTRL-W H/J/K/L 移动窗口,CTRL-W K 垂直分隔时，将窗口移动到最上面并扩展到整屏宽度

vim -o/-O one.txt two.txt three.txt 
:all :vertical all 为:args中每个文件打开一个窗口
````

2. 差异比较  

````vim
vimdiff one.txt two.txt 显示文件差异
zo zc 打开关闭折叠

:edit main.c
:vertical diffsplit main.c~ 另一种diff方式，从vim内部打开比较文件

补丁方式?

查找差异
]c 跳转到下一个不同

消除差异
:diffupdate 修改后更新高亮
:dp         diff put，将另一侧的差异推过去
:do         diff obtain,将另一侧差异拉过来
````

3. 打开的新窗口位置

````vim
命令行很多命令都可以前面加上s表示在新窗口dakia
普通模式加上CTRL-W 例如 CTRL-W CTRL-^ 新窗口轮换
'splitbelow' 让新窗口出现下面(默认上面)
'splitright'

:leftabove {cmd}        当前窗口的左上方
:aboveleft {cmd}        同上
:rightbelow {cmd}       当前窗口的右下方
:belowright {cmd}       同上
:topleft {cmd}          整个 Vim 窗口的最上面或者最左边
:botright {cmd}         整个 Vim 窗口的最下面或者最右边
````
4. 标签页

````vim
:tabedit thatfile 标签页打开新文件
:tab split 同一缓冲区的新的标签页
:tab help gt 在新的标签页打开help
{n}gt {n}gT :tabfir :tabl 标签页跳转
:tabonly
````

## usr_40 创建新的命令

## usr_43 使用文件类型
