---
title: 我的Sublime Text快捷键
date: '2014-05-09'
description:
categories: 'tool'
---

### select 

* `⌃+⌘ +G` find all `Escape` cancle
* `⌃ ⇧ ↑` `⌃ ⇧ ↓` column select 。需要先把系统的shortcuts绑定去掉

### line

* `⇧ ⌘ D` duplicate line
* `⌃ ⇧ K` delete line
* `⌃ ⌘ ↑`  `⌃ ⌘ ↓` swap line
* `⌘ ]` `⌘ [` line indent / unindent
* `⌘ ↩ ` insert line after
* `⇧ ⌘ ↩` insert line before
* `⌃ K`  `⌘ ⌦ ` delete to end / beginning
* `⌃ a` go to line head `⌃ e` go to line end

### comment

* `⌘ /` toggle comment
* `⌥ ⌘ /` toggle block comment

### code folding

* `⌥ ⌘ [` `⌥ ⌘ ]` fold /unfold
* `⌘ K, ⌘ 1` flod all

### edit

* `⌘ K, ⌘ U` `⌘ K, ⌘ L` upper/lower case
* `⌃ space` show complete

### find & goto

* `⌘ G` `⇧ ⌘ G` find next/previous
* `⌥ ⌘ F` replace / replace all
* `⌘ P` go to anything
* `⌘ R` go to symbol
* `⌥ ⌘ ↓` go to define
* `⌃ -` jump back
* `⌃ ⇧ -` jump forward
* `⌃ G` go to line

### panes & view

* `⌥ ⌘ 2/3/4` two/three/four column (vertical) panes
* `⇧ ⌥ ⌘ 2/3/4` two/three/four row (horizontal) panes 
* `⌘ K, ⌘ B` toggle the sidebar

### package

* `⌘ ⇧ P` 调出package control

### 其他

**key bindings**  

`single_line`表示不仅作用于当前行，作用于整个文本。  
```json
[
  { "keys": ["ctrl+shift+r"], "command": "reveal_in_side_bar"},
  { "keys": ["ctrl+shift+f"], "command": "reindent", "args": {"single_line": false}},
  { "keys": ["ctrl+shift+w"], "command": "close_all" },
  { "keys": ["ctrl+shift+s"], "command": "sava_all"},
  { "keys": ["ctrl+shift+b"], "command": "view_in_browser", "args": { "browser": "chrome" } }
]
```

### 插件

* JSONLint json语法检查插件
* PrettyJSON json格式化，因为reindent对json不好用，`⌃ ⌘ j`快捷键
