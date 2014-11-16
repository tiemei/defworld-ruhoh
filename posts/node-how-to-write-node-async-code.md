---
title: Node的异步代码怎么写
date: '2014-09-19'
description:
categories: 'Node.js'
---

# 函数式编程

函数作为js中的一等公民，可以作为参数或返回值传递，此谓高阶函数。偏函数也是基于此，返回一个函数，但是这个返回的函数一部分已经预置了，举个例子：  

```js
// 一个调用多次才会执行的函数
_.after = function(times, func) {
    if (times <= 0)return func();
    return function() {
        if (--times < 1) {
            return func.apply(this, arguments);
        }
    }
}
```

# 异步编程难点

## 异常处理

## 函数嵌套过深

## 阻塞代码

## 多线程编程

# 异步编程解决方案

## 事件发布订阅模式

Node自身提供的events模块。  

```js
// 订阅
emitter.on("event1", function (message) {
    console.log(message);
});

// 发布
emitter.emit('event1', "I am message!");
```

将事件与具体的处理逻辑解耦，一个时间与多个回调函数关联。从某种角度来说事件的设计就是组件的接口设计。    