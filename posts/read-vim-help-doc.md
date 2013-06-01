---
title: 不设限的Vim，不设限的人生
date: '2013-06-01'
description:
categories: 'vim'
tags: vim

type: draft
---


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
        `{mark} 跳转到标记处
        '{mark} 到标记出行首
        :markds 查找所有标记
        
        特殊标记
        ' 跳转前的光标位置
        " 最后编辑的光标位置
        [ 最后修改的开始位置
        ] 最后修改的结束位置

## usr_04 做小改动


## usr_07 编辑多个文件

## usr_08 分割窗口
