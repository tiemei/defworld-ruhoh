---
title: js入门笔记（三）
date: '2014-06-15'
description:
categories: 'Node.js'

type: draft
---

## 利用`__defineGetter__``__defineSetter__`定义get/set方法，并且注意下js没有块作用域的问题

```js
// 错误的写法
var Record = function(data) {
  this.fieldValues = {}
  this._data = data;
  var record = this;
  for(var key in data) {
    record.__defineGetter__(key, function() {
      return record._data[key];
    });
    record.__defineSetter__(key, function(val) {
      record.fieldValues[key] = val; // 因为key是全局变量，对data对象遍历时，最终key为prop2
    });
  }
}

var myRecord = new Record({prop1: 'prop1test', prop2: 'prop2test'});

myRecord.prop1 = 'newvalue';

console.log(myRecord.fieldValues.prop1); // undefined
console.log(myRecord.fieldValues.prop2); // 'newvalue'

// 正确的写法
var Record = function(data) {
    var key;
    this.fieldValues = {}
    this._data = data;
    for(key in data) {
        //closure maintains state of "key" variable
        //without being overwritten each iteration
        (function (record, key) { // 把key变成局部对象，函数内部的使用将不会随着外部key变量的指向而改变
            record.__defineGetter__(key, function() {
                return record._data[key];
            });
            record.__defineSetter__(key, function(val) {
                record.fieldValues[key] = val;
            });
        }(this, key));
    }
}
```


参考:  
[Weird behavior with Javascript getter/setters](http://stackoverflow.com/questions/10453725/weird-behavior-with-javascript-getter-setters)  
