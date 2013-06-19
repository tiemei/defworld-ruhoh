---
title: 从eclipse迁移到intellijIDEA(OS x 10.5)
date: '2013-06-07'
description: eclipse,IDEA,intellij,快捷键,OSX
categories: 'tool'
tags: intellij
---
![intellijIDEA](http://farm8.staticflickr.com/7287/8977180999_271d543e62.jpg)  
本文帮助希望从eclipse迁移到IntelliJ IDEA的coder.  
我已经使用eclipse开发java程序快3年，虽然也能满足基本需求，但是我还是希望尝试新的东西，IDEA也确实有独到之处。  

参考：  
[IntelliJ IDEA 的 20 个代码自动完成的特性](http://www.oschina.net/question/12_70799)  
[从Eclipse转移到IntelliJ IDEA一点心得](http://www.ituring.com.cn/article/37792)  
[常用IDEA快捷键](http://www.colorfuldays.org/program/idea_keymap/)
[Intellij-IDEA-F2E](https://github.com/damao/Intellij-IDEA-F2E)  
[@IntelliJ IDEA第二部分视频讲解：项目创建/打开/导入+主题修改+布局/界面介绍+工具栏/菜单栏介绍](http://www.youmeek.com/intellij-idea-part-ii-ui/)
(http://wiki.jetbrains.net/intellij/Creating_and_importing_Maven_projects)  
[Intellij配置文件同步](http://willerce.com/post/intellij-idea-config-sync)  
[Intellij IDEA常用设置](http://www.myexception.cn/program/1032108.html)

本文快捷键仅适用于：  
> Mac OS X 10.5+ keymap  
> Intellij IDEA 12  

其他环境请参照[Intellij IEDA Getting Started](http://www.jetbrains.com/idea/documentation/index.jsp)  
或者自己到`settings → keymap`查看设置  

## 自动完成功能
相比于eclipse停留在类方法和变量的简单自动提示，IDEA实在有强太多:  


* `⌃ Space/自动`(Basic code completion) 基本的类、方法、成员变量、局部变量等查找
* `⌃ ⌥  Space`(Smart code completion)更加智能的根据上下文过滤后的提示
* chain completion，多层调用提示，在eclipse里要通过pluging: Code Recommenders完成
* 父类构造函数super()所用参数自动完成
* `⌘ ⇧ ↩ `(statements complemention)语句自动完成
* `⌘ p`(Parameter info)方法参数信息提示
* `⌥ Space`(quick definition)显示建议列表中方法定义
* `⌥ ↩ `(Exclude from completion) 排除几乎总是无用的提示
* 输入一个字符的中间部分也能匹配
* 自动完成不止在代码编辑器，debug模式，文件选择器等都有它的影子

## 从eclipse迁移过来

作为一个多年的eclipse用户，总有些情节和习惯无法割舍。好，现在
一起看看如何平滑过度：  

* show number line : settings → editor → Appearance → 勾选'show line numbers'
* 编辑窗口与project窗口关联: 点击project栏头部第一个地球仪图案，或者选中齿轮图案的
`AutoScroll to Source`和`AutoScroll from source`。你也可以充分利用`Navigation Bar`
(`⌘ ↑`)
* eclipse: ctrl + o，列出所有成员变量方法，IDEA中`⌘ F12`
* `⌥ ↑` / `⌥ ↓` 扩大/减少选中的词语的范围
* eclipse`ctrl + k`同单词跳转，IDEA建议手动添加绑定`⌘ ⌃ k`(Find Word at Caret),
结合`⌘ g` (Find Next) `⌘ ⇧ g`(find Previous)使用
* svn /git 版本控制，修改过的eclipse默认标记到最高层目录，而IDEA默认只标记
有改动的文件，需要手动设置`settings → Version Control → 勾选show directories with
changed descendants`
* IDEA默认自动保存，无需手动`ctrl + s`
* IDEA文件默认同步刷新到磁盘，无需手动刷新


## 我的其他设置及快捷键

### 未归类
* 关闭拼写检查 `settings → spell`
* 类注释修改  `settings → file template`
* 空格替换⇥ : `settings → code type → java` 
* [color theme](https://github.com/jkaving/intellij-colors-solarized)
`Preferences → Editor → Colors & Fonts`
* 文件过滤`settings → file types → Ignore files and folders`
* `⌘  ,`     全局setting
* `⌘  ;`     项目settting
* `⌘  w`    关闭当前界面
* `⌘ ⇧ w`   自定义,close all
* `F2 / ⇧ F2` next/preview highlighting syntax problem

### 编辑代码

* `⇧ ↩ `/ `⌘ ⌥ ↩ ` 下/上插入新行
* `⌘ j`    必statements complemention更强大， [live template](http://ooxx.me/intellij-idea-live-template.orz)
* `⌘ n`    生成代码/创建文件/生成get&setter
* `⌥  ↩ `    调出出错提示处理方法
* `⌘ d`      double整行、块
* `⌘ ⌦ ` / `⌘ x` 删除一行
* `⌘  c/v/x` 整行操作
* `⇧ ⌘ v`    调出寄存中保存的最近赋值的内容
* `⌘ /`      注释/取消注释
* `⌃ o`      生成override方法
* `⌃ i`      生成implement方方法(用⌥ + enter即可)
* `⇧ ⌥ ↑/↓` 选中的整行行上下移
* `⇧ ⌘ ↑/↓` 更加智能的上下移动
* `⌘ ⌥ t`   对选中代码自动生成环绕(if/try..catch/..)
* `⌘ ⇧ u` 大小写转换

### 代码跳转
* `⌘ ⌥ b` / `⌘ b` Go to declaration / Go to implementation(s)
* `⌃ ⇧ t` Go to type declaration
* `⌘ u`  Go to super-method/super-class
* `⌘ y`  Open quick definition lookup
* `⌥ ⌘ [` / `⌥ ⌘ ]` Move to code block start/end(我机器设置)
* `⌘ ⇧ [` / `⌘ ⇧ ]` pre/next tab
* `⌘ ⌥ ← /→` 在代码间前进后退

### 代码查看
* `⌘ + / ⌘ -` expand/collapse code block
* `⌘ ⇧ + / ⌘ ⇧ -` expand /collapse all
* `⌃ + j` class quick doc  
* `⌃ h` class type hierarchy
* `⌃ ⌥ h` Call hierarchy。常见用法，查看某个方法调用处

### 格式化
* rearranger 格式化代码，给你更精细的格式化控制，设置`settings → rearranger`，
可用云盘同步,快捷键`⌘ ⌥ ⇧ r`
* `settings → code style`调整`⌥ ⌘ l`格式化效果
* `⌃ ⌥ o` 优化import,排除无用的import(optimize import)

### 视图切换

* `⌘  n`   切换到n对应的视图
* `⌃  ⇥ `(plugin → Switch) 打开的文件切换
* `⌘ e`  列出最近查看的额文件列表+视图
* `⇧ ⌘  e` 最近修改的文件

### 搜索

* `⌘ f`搜索，`⌘ r` 搜索替换
* project中，`⌃ ⇧ f/r` 递归搜索/替换，`⌥  a`全部替换
* `⌥  F7`   搜索对象被引用的地方
* `⌘ F7`   搜索对象在当前文件被引用地方
* `⌘ o`    查找类 （正则/大写字母缩略）
* `⌘ ⇧ o`  查找文件 (正则/大些字母缩略)
* `⌘ ⌥ o`  查找文件内容(symbol)

### 重构  
* `F5` 拷贝，只能是文件，到其他地方(类和xml都会相应变动)
* `F6` 移动（文件/变量/域/方法...）
* `⇧ F6`   改名
* `⌘ F6`   修改方法签名(不常用，直接改代码就行)
* `⌘ ⌥ v`  extract vaiable
* `⌘ ⌥ p`  extract parameter
* `⌘ ⌥ f`  extract filed
* `⌘ ⌥ m`  extract method
* `⌘ ⌥ c`  extract constant

### debug & run

* `⌃ ⌥ r` select configuration and run
* `⌃ ⌥ d` select configuration and debug
* `⌃ r`   run当前选中的config
* `⌃ d`   debug当前选中的config
* `F7`  step into
* `F8`  step over
* `⇧ F8` step out
* `⌥ F8` evaluate expression
* `⌥ F9` 下一个断点处



