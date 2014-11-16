---
title: js入门笔记（二）——面向对象
date: '2014-06-13'
description: js  javascript 面向对象
categories: 'Node.js'
---

# 直接创建一个对象

ECMA-262把对象定义为`无序属性的结合，其属性可包含基本值、对象或者函数`。

**创建自定义对象方式一——创建一个Object实例** 

```js
var person = new Object();
person.name = 'tiemie';
person.age = 29;

person.sayName = function() {
    alert(this.name);
};
```

早期用这种方式创建对象，不过后来被方式二替代了。

**方式二——对象字面量** 

```js
var person = {
    name: 'tiemei',
    age: 29,

    sayName: function() {
        alert(this.name);
    }
};
```

### 对象属性

ECMAScript定义对象上有两种属性：

* 数据属性
    - [[Configurable]]，默认true，表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，能否把属性修改为访问器属性
    - [[Enumerable]]，默认true，能否通过for-in循环返回属性
    - [[Writable]]，默认true，能否修改属性的值
    - [[Value]]，默认值undefined，这个属性的数据值
* 访问器属性
    - [[Configurable]]，默认true，能否通过delete删除属性从而重新定义属性，能否修改属性的特性，能否把属性修改为数据属性
    - [[Enumerable]]，默认true，能否通过for-in循环返回属性
    - [[Get]]，默认undefined，在读取属性时调用的函数
    - [[Set]]，默认undefined，在写入属性时调用的函数

上面定义的person对象name属性，前三个数据属性都是true，[[Value]]为'tiemei'。ECMAScript5可通过`defineProperty`可修改属性：  

```js
// 修改数据属性
var person = {};
Object.defineProperty(person, "name", {
    configurable: false,
    value: 'tiemei'
});

// 修改访问器属性
var bool = {
    _year: 2004 // 前置下划线，表示只能通过对象方法访问的属性
};
Object.defineProperty(book, "year", {
    get: function() {
        return this._year;
    };
    set: function(newValue) {
        this._year = newValue;
    };
});
```

* 也可通过`Object.defineProperties`一次定义多个属性。  
* 也可通过`Object.getOwnPropertyDescriptor`读取属性特性。

# 先定义后创建对象

**工厂模式**  

```
function createPerson(name, age) {
    var o = new Object();
    o.name = name;
    o.age = age;

    o.sayName = function() {
        alert(this.name);
    };
    return o;
};

var p1 = createPerson('tiemei', 25);
var p2 = createPerson('ll', 25);
```

缺点：  

* 无法知道一个对象的类型

**构造函数模式**  

```js
function Person(name, age) {
    this.name = name;
    this.age = age;

    this.sayName = function() {
        alert(this.name);
    };
};

var p1 = new Person('tiemei', 25);
var p2 = new Person('tiemei', 25);

// 虽然constructor属性也可用来标识对象类型，但是还是建议用instanceof来判断
alert(p1.constructor == Person); // true
alert(p2.constructor == Person); // true

// 构造函数与其他函数的唯一区别，就在于调用它们的方式不同。
// 任何函数只要通过new操作符调用，那它就可作为构造函数；而任何函数不通过new操作符调用跟普通函数就没啥区别
Person('tiemie', 25); // 此时this对象就是外部环境变量

var o = new Object();
Person.call(o, 'tiemei2', 25); // 在另一个对象作用域中调用
o.sayName(); // tiemei2
```

以上创建对象的方式实际上经历：  

1. 创建一个新对象
2. 将构造函数的作用域赋给新对象（因此this就指向了这个新对象）
3. 执行构造函数中的代码（为这个新对象添加属性）
4. 返回新对象

缺点：

* 每个方法都要在每个对象上重新创建一遍，没有必要

**构造函数的进化模式**  

```js
function Person(name, age) {
    this.name = name;
    this.age = age;

    this.sayName = sayName;
};

// 这种方式创建的函数，在源码之前也可以使用。
// new Function()创建的则不行
function sayName() {
    alert(this.name);
};
```

缺点：  

* 在全局作用域中定义的函数实际上只能被某个对象调用，这让全局作用域名不副实
* 没有封装性可言

**原型模式**  

```js
functino Person() {};

Person.prototype.name = 'timei';
Person.prototype.age = 25;
Person.prototype.sayName = function() {
    alert(this.name);
};

// p1, p2公用name, age, sayName
var p1 = new Person();
var p2 = new Person();
```

* p1包含一个指向构造函数的原型对象（见《JavaScript高级程序设计（第2版）》P148）。每当代码读取某个对象的某个属性时，执行一次搜索。从`对象实例`本身，到指向的`原型对象`。  
* 不能通过对象实例重写原型中的值，添加一个属性，实际上在该实例对象中添加了一个同名属性。
* `obj.hasOwnProperty()`可以检测一个属性是否存在于实例中
* `in`操作符会在通过对象能访问到给定属性时返回true。当然不能枚举的属性不包含在内。`for-in`循环可返回所有可枚举属性
* `Objects.keys(obj)`返回一个所有可枚举属性的字符串数组

缺点：  

* 原型对象中的属性和对象公用，虽然可通过重新赋值方式覆盖原型对象中的属性

**原型模式的简化写法**  

```js
function Person() {};

Person.prototype = {
    name: 'tiemie',
    age: 25;

    sayName: function() {
        alert(this.name);
    };
};

// 以上写法，原型对象的constructor属性不会指向Person，虽然instanceof还可用。优化下如下:
function Person() {};

Person.prototype = {
    constructor: Person, // 上面的方式constructor不可枚举，这种方式可枚举
    name: 'tiemie',
    age: 25;

    sayName: function() {
        alert(this.name);
    };
};
```

`原型的动态性`：实例对象创建后，在原型上所做的任何修改，都会立即在实例对象上反映出来，因为实例对象只是保有一个指向原型对象的指针。下面举例说明：    

```
var f = new Person();
Person.prototype.sayHi = function() {};
f.sayHi(); // ok

var f = new Person();
Person.prototype = {
    sayHi: function() {}
};
f.sayHi(); // not ok。因为f对象中指针指向的原型对象不是通过{}方式重新创建的新对象。
```

`原生对象的原型`：原生对象都在其构造函数的原型上定义了方法，因此可以自己添加更多方法，但是不建议这么做。  

缺点：  

* 无法通过构造函数传递不同的初始化参数
* 所有对象共享属性，导致如果属性是引用类型，例如数组，一个对象的修改会影响到另一个对象

**组合构造函数模式和原型模式**  

```js
function Person(name, age) {
    this.name = name;
    this.age = age;
}

Person.prototype = {
    constructor: Person,
    sayName = function() {}
};

// p1 p2的name age属性不同
var p1 = new Person('tiemei', 25);
var p1 = new Person('ll', 25);
```

**动态原型模式**  

```js
function Person(name, age) {
    this.name = name;
    this.age = age;

    if (typeof this.sayName != "fuction") {
        Person.prototype.sayName = fucntion() {

        };
        // .. 这里可写多个方法
    }
}
```

优点：  

* 加强了视觉上的封装性

**寄生构造函数**  

```js
function SpecialArray() {
    var arr = new Array();

    // 用构造函数所接受到的所有参数初始化了数组的值
    arr.push.apply(arr, argsments);

    // 添加新方法
    arr.toPipedString = function() {
        return this.join("|");
    };

    return arr;
}

var colors = new SpecialArray('blue', 'red');
alert(colors.toPipedString());
```

缺点：  

* 不能使用instanceof方法

**稳妥构造函数**  

`稳妥对象`：没有公共属性，而且其方法也不引用this对象。  

```js
function Person(name, age) {
    var o = new Object();
    o.sayName = function() {
        alert(name);
    };

    return o;
}
```

缺点：  

* 不能使用instanceof方法

# js对象继承

由于函数没有签名，在ECMAScript中无法实现接口继承，只支持实现继承，主要是依靠原型链来实现的。  

```js
function SuperType() {
    this.property = true;
}
SuperType.prototype.getSuperValue = function() {
    return this.property;
};

function SubType() {
    this.subproperty = false;
}

// 继承了SuperType
// 原来存在于SuperType的实例中所有的属性和方法，现在也存在于SubType.prototype中了
// 实际上SubType.prototype指向的对象，就是SupterType的实例。
SubType.prototype = new SuperType();
// 添加新方法
SubType.prototype.getSubValue = function() {
    return this.subproperty;
};
// 重写超类中的方法
// SubType.prototypegetSubValue = function() {...};

// instance指向SubType的原型，SubType作为一个SuperType的对象又指向SuperType的原型。
// 实际上所有函数的默认原型都是Object实例，因此SuperType的原型对象持有一个指向Object原型的指针
var instance = new SubType();
alert(instance.getSuperValue()); // true
```

![prototype inherit](https://farm4.staticflickr.com/3924/15135450548_b4f2082b7c.jpg)  

原型链继承所带来的缺点：  

* 父类中的引用类型的属性，被所有子类的实例共享。不同于Java等语言的方式
* 因为子类的原型是父类的实例对象，子类的所有实例对象共享这个父类对象，所以没有办法在不影响所有子类对象实例的情况下给超类的构造函数传递参数

**借用构造函数（constructor stealing）**  

```js
function SuperType() {
    this.colors = ['red', 'blue'];
}

function SubType() {
    // 继承SuperType
    SuperType.call(this);
}

var instance1 = new SubType();
```

优点：  

* 解决原型链模型中，父类实例属性被所有子类对象共享的问题

缺点：  
 
* 仅用构造函数，方法都在构造函数中定义，函数每次都是new出来的，没法复用
* 在超类型的原型中定义的方法，对子类型而言是不可见的
* 因此，借用构造函数技术很少单独使用

**组合集成**  

最常用方式。思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来试下你对实例属性的继承。  

```js
function SuperType(name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

SuperType.prototype.sayName = function() {
    alert(this.name);
};

function SubType(name, age) {
    // 继承属性
    SuperType.call(this, name); // 第二次调用SupertType()。这里创建的name，colors属性会屏蔽第一次创建的属性
    this.age = age;
}

// 继承方法
SubType.prototype = new SuperType(); // 第一调用SupterType()
// 自定义方法
SubType.prototype.sayAge = function() {
    alert(this.age);
};
```

缺点：  

* 无论什么情况下，都会调用两次超类型构造函数

**原型式继承**  

思路是，借助原型可以基于已有对象创建新对象，同时不必因此创建自定义类型。  

```js
// 新对象持有一个指向o的原型指针，因此新对象能访问到o的所有属性和方法
function objecct(o) {
    function F() {};
    F.prototype = o;
    return new F();
}

var person = {
    name: 'tiemei',
    friends: ['Su', 'Wang']
};

// anotherPerson实例持有一个原型指针指向person对象
var anotherPerson = object(person); 
```

ECMAScript5通过新增`Object.create()`方法规范了原型式继承。  

```js
// 接上一个例子
// 等价于上面的object方法
var anotherPerson = Object.create(person); 

// 第二个参数与Object.defineProperties()方法第二个参数格式相同
var yeAnotherPerson = Object.create(person, {
    name: {
        value: "Greg"
    }
});

```

**寄生式继承**  

创建一个用于封装继承过程的函数。  

```js
function createAnother(original) {
    var clone = object(orginal); // object()不是必须的，任何能返回新对象的函数都适用于此模式

    clone.sayHi = function() {};
    return clone;
}

var person = {};
var anotherPerson = createAnother(person);
another.sayHi();
```

缺点：  

* 与构造函数模式类似，做不到函数的复用

**寄生组合式继承**  

`最理想的继承方式`。为了解决组合继承的缺点，出现了寄生组合式继承。借用构造函数来继承属性，通过原型链混成形式来继承方法。思路是，不必为了指定子类型的原型而调用超类型的构造函数，我们所需的无非就是超类型原型的一个副本而已。  

```js
// 
function inheritPrototype(subType, superType) {
    var prototype = object(superType.prototype);
    prototype.constructor = subType;
    subType.prototype = prototype;
}

function SuperType(name) {
    this.name = name;
}
SuperType.prototype.sayName = function() {};

function SubType(name, age) {
    SuperType.call(this, name);

    this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function() {
    // ...
};
```

优点：  

* 只调用了一次SuperType构造函数，因此避免了在SubType.prototype上创建不必要的多余的属性。  

# js闭包

待续

参考:  

JavaScript高级程序设计（第2版）》  


