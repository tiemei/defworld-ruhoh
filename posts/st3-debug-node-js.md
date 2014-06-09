---
title: run & debug node.js
date: '2014-06-08'
description:
categories: 'Node.js'
---

# debug node.js

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


# run node.js

## 用[node-dev](https://github.com/fgnass/node-dev)做文件热加载

**准备工作**  

```bash
npm install -g node-dev
# 你也可以选择Growl or libnotify，osX可以内置的提醒器
sudo gem install terminal-notifier
```

**开始吧**  

`node-dev foo.js`  
可用两个选项：  

* `--no-deps` Watch only the project's own files and linked modules (via `npm link`)
* `--all-deps` Watch the whole dependency tree
  
By default node-dev will watch all first-level dependencies, i.e. the ones in the project's node_modulesfolder.  

**其他**  

* CoffeeScript这样的翻译成js的语言也可以用这个工具
* 还有其他高级配置项可供选择
* 如果子进程需要重启，会发一个`SIGTERM`信号，如果你的代码没有处理这些
信号，`process.exit(0)`就会被强行调用

参考：  
[Debugging JavaScript](https://developer.chrome.com/devtools/docs/javascript-debugging?csw=1)  
[Debugging with Node Inspector](http://docs.strongloop.com/display/DOC/Debugging+with+Node+Inspector#DebuggingwithNodeInspector-Keyboardshortcuts)
