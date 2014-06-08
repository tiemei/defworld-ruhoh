---
title: run & debug node.js
date: '2014-06-08'
description:
categories: 'Node.js'
---

1. `sudo npm install -g node-inspector`
2. `node-debug xxx.js`
3. set breakpoints
4. request & break auto

**shortcuts**  

* Resume script execution (F8): 继续执行
* Step over next function call (F10): 按步执行，不进入另一个函数
* Step into next function call (F11): 按步执行，进入另一个函数内部
* Step out of current functino (⇧ F11): 跳出当前函数，进入父函数

* `⌘ o` find file
* `⇧ ⌘ o` find function member in current file



**other**  

* 可给断点设置条件
* 可通过console查看当前断点下上下文状态
* Pause on exception 可选择打开或关闭
* 可打开左侧源码窗口，保持两边窗口的同步


参考：  
[Debugging JavaScript](https://developer.chrome.com/devtools/docs/javascript-debugging?csw=1)  
[Debugging with Node Inspector](http://docs.strongloop.com/display/DOC/Debugging+with+Node+Inspector#DebuggingwithNodeInspector-Keyboardshortcuts)
