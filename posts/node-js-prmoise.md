---
title: Node.js Promise/Deferred入门
date: '2014-10-16'
description: 'nodejs promoise'
categories: 'Node.js'
---

Promise/Deffered模式只是解决异步编程的一个模式，我这里一个一个来说吧。  


## 回调/高阶函数/函数编程

**高阶函数**编程是指，函数可作为参数或返回值。其中**偏函数**是其中一种应用，举个例子：  

```javascript
// Underscore库提供
// 生成一个需要调用多次才真正执行的函数
_.after = function(times, func) {
    if (times <= 0) return func();
    return function() {
        if (--times < 1) {
            return func.apply(this, arguments);
        }
    } 
}
```

### 优点

* 多线程编程模型的问题
* 很好的处理 I/O 密集型问题
* V8
* 调用C/C++扩展模块

### 缺点

> 难点一：异常处理

```javascript
// 只能捕获调用async方法的异常，实际执行callback的异常因为是异步执行无法捕获
try {
    async(callback);
} catch (e) {
    // TODO
}
```

Node在处理异常时形成一种约定，将异常作为回调函数的第一个实参传回：  

```javascript
// 如果发生了异常，async方法的实现需要在调用callback时讲err传回
async(function (err, result) {
    // TODO
});
```


> 难点二：函数嵌套过深

> 难点三：阻塞代码

因为是单进程，不能像java一样阻塞后续代码的执行。

> 难点四：多线程编程 

浏览器中最终JavaScript执行线程与UI渲染公用一个，为了更好利用多核CPU，提出Web Workders，将JavaScript执行与UI渲染分离，但并没有形成一个标准。Node可以编写多进程代码，充分利用多核CPU。  

> 异步转同步

无法很好的支持需要同步编程的场景。  

## 事件发布/订阅模式

即回调函数的事件化。  
Node自身提供的events模块，是发布/订阅模式的一个简单实现，Node中部分模块继承自它。   

```javascript
// 下面是Node中Stream对象继承EventEmitter例子。
// Node提供的核心模块中有近半数都继承自EventEmitter。
var events = require('events');

function Stream() {
    events.EventEmitter.call(this); // 继承属性
}

util.inherits(Stream, events.EventEmitter); // 继承方法
```

### 利用事件队列解决雪崩问题

比如缓存是失效时，高并发将压力打到后方数据源。  

```javascript
var select = function (callback) {
    db.select("SQL", function (result) {
        callback(results);
    });
};
```

加上状态锁：  

```javascript
var status = "ready";
var select = function (callback) {
    if (status ==== 'ready') {
        status = "pending";
        db.select("SQL", function (results) {
            status = "ready";
            callback(results);
        });
    }
};
```

这样的调用只有第一次石油结果返回的，其他的调用没有结果。改进下，利用once：  

```javascript
var proxy = new evnets.EvnetEmitter();
vat status = "ready";
var select = function (callback) {
    // 将请求的事件压入事件队列中，执行一次这个listner就会被移除
    proxy.once("selected", callback);
    if (status === 'ready') {
        stauts = 'pending';
        db.select("SQL", function (result) {
            proxy.emit("selected", results);
            status = "ready";
        });
    }
};
```

### 多异步之间的写作方案（一对多，多对一，多对多）

### EventProxy

对事件订阅/发布模型的扩充。  

## Promise/Deffered模式

Prmoise/A提议对单个异步操作做了这样的抽象定义：  

* Prmoise操作只会处在3中状态的一种：未完成态、完成态何失败态
* Prmoise的状态只会从未完成态向完成态或失败态转化，不能逆反。完成态和失败态不能互相转化。
* Prmoise的状态一旦转化，将不能被更改。

在API的定义上，Prmoise/A提议是比较简单的。一个Prmoise对象只要具备then()方法即可。Deferred对象主要是用于内部，用于维护异步模型的状态;Prmoise则作用域外部，通过then()方法暴露给外部以添加自定义逻辑。将业务不可变的部分封装在Deferred中，将可变的部分交给了Promise。Prmoise是高级接口，事件是低级接口，低级接口可以构成更多复杂的场景。  
下面看一个Node的events模块实现的版本。  

```javascript


```



## 流程控制库



