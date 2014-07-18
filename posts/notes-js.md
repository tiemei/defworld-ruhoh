---
title: js入门笔记（一）——基础
date: '2014-06-13'
description: 'nodes javascript'
categories: 'Node.js'
---

# js函数有啥特点

* 函数的参数实际上在内部是用一个数组表示的，在函数体内可以通过`arguments`对象访问。arguments只是跟数组类型可以通过方括号访问每一个元素，实际不是Array实例
* 函数定义的参数与实际传递的参数没有个数相等的限制，不像Java语言会编译校验这个
* 因为函数参数是由包含另零个或多个值的数组来表示的，没有函数签名，故`没有重载`


# js基本类型和引用类型

* `基本数据类型`：Undefined 、 Null 、 Boolean 、 Number 、 String
* 不能给基本数据类型添加属性，添加以后访问也是undifined
* 因为String在js不同于其他语言是基本类型，所以赋值的时候会将值拷贝一份

### 检测类型

**`typeof`区分是基本类型还是应用类型**  

```js
var s = 'Nicholas';
var b = true;
var i = 22;
var u;
var n = null;
var o = new Object();

alert(typeof s); // string
alert(typeof i); // number
alert(typeof b); // boolean
alert(typeof u); // undefined
alert(typeof n); // object
alert(typeof o); // object
```
  
**`instanceof`检测对象是啥基本类型** 

* 所有引用类型都是Object的实例
* 对基本类型使用都会返回false

函数也是对象。  

### 基本包装类型Boolean Number String

不建议在代码中使用包装类型。  
之所以基本类型上有方法可以访问，是因为执行的瞬间后台创建了一个包装类型，例如：  

```js
var s1 = 'some text';
var s2 = s1.substring(2);

// 上面的实际上再后台是这么运行的

var s1 = new String('some text');
var s2 = s1.substring(2);
s1 = null; // 使用new操作符创建的引用类型实例作用域内一直存在，而这里只是存在于一行代码的执行瞬间
```

1. 创建String类型的一个实例
2. 在实例上调用指定的方法
3. 销毁这个实例

```js
// 两种创建包装类型的方式

var obj = new Object('some text');

// 等价于

var str = new String('some text');
```

```
// 转型函数与包装类型

var value = '25';
var number = Number(value); // 转型函数
alert(typeof number); // 'number'

var obj = new Number(value); // 构造函数
alert(typeof obj); // 'object'

```

**布尔表达式中所有对象都会被转换成true**  

```js
var falseObj = new Boolean(false);
var result = falseObj && true;
alert(result); // true
```


# js执行环境及作用域

## 执行环境与作用域链

每个`执行环境`都有一个与之关联的**变量对象（variable object）**，环境中定义的所有变量和函数都保存在这个对象中，虽然我们编写的代码无法访问这个对象，但解析器在处理数据时会在后台使用它。  

* 每个函数有自己的执行环境
* 代码在一个环境中执行时，会创建变量对象的一个`作用域链（scope chain）`，超找对象也按此链形成一个优先级

**延长作用域链**  

```js
function buildUrl() {
    var qs = '?debug=true';

    with(location) {
        var url = herf + qs; // herf实际上是location.herf
    }

    return url;
}
```

`try-catch`的catch块也是类似  

**没有块级作用域**  

* 所有控制语句的代码块内部声明的变量都会逸出到外部，不同于Java语言。  
* 函数中的声明的var变量作用域仅限于函数内部

参考:  

JavaScript高级程序设计（第2版）》  
