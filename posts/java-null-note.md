---
title: 笔记 - 关于Java中的Null
date: '2014-09-12'
description: 
categories: 'java'
---

有人说写出NPE，是程序员的逻辑错误，我们在做code review的时候也经常提醒或者被提醒这一点，殊不知这个坑真的太容易踩了。但是如果你不辞辛劳的判断Null，你会发现你的代码变成一坨奇怪的形状，就像脸蛋白皙五官精致的姑娘被各种乱涂乱画。下面大家看看这个例子：  

```java
computer.getSoundCard().getUsb().getVersion();
```

我们很容易被吐槽（博主经常被测试妹子吐槽这里这里检查了NPE了么），一个电脑肯定有声卡么，声卡肯定有usa插口么？然后代码变成了这样：  

```java
if (computer != null) {
    SoundCard sc = computer.getSoundcard();
    if (sc != null) {
        Usb usb = sc.getUsb();
        if (usab != null) {
            return usb.getVersion();
        } else {
            return null;
        }      
    } else {
        return null;
    }
} else {
    return null;
}
```

有人说你这么写太夸张了，不是可以精简一下：  

```java
if (computer != null && computer.getSoundCard() != null && computer.getSoundCard().getUsb() != null) {
    return computer.getSoundCard().getUsb().getVersion();
} else {
    return null;
}
```

我们来对比下这种情况其他语言和工具是怎么写的：  
groovy用语言级别支持的?处理清晰简洁：  

```Groovy
String version = computer?.getSoundcard()?.getUsb()?.getVersion();
```

Guava框架用自定义的Optional<T>对象：  

```java
Optional<Computer> maybeComputer = Optional.of(computer); // 如果此处computer为null，将跑出NPE，可用Optional.fromNullable(T)允许传入Null表示缺失
if (maybeComputer.isPresent()) { // 这里只有present/absent两种状态，present时就拿到的对象就肯定不为null
    maybeComputer.getSoundCard();
}
// ...
```

Java8：  
简单解释下下面代码，`maybeSoundcard.orElse(new Soundcard("defaut"))`等价于`soundcard != null ? soundcard : new Soundcard("basic_sound_card")`，`maybeSoundcard.map(Soundcard::getUSB)`等价于`soundcard != null ? souncard.getUSB() : null`，而`Computer::getSoundcard`是Lambdas写法。  

```java
String name = computer.flatMap(Computer::getSoundcard)
                          .flatMap(Soundcard::getUSB)
                          .map(USB::getVersion)
                          .orElse("UNKNOWN");
```

总结一下，Map中get一个值返回Null有可能表示缺失也可能表示存在且值就为Null，而Guava Optional<T>强制提醒开发者需要去关注是否null，从而去避免NPE引起执行流程的异常甚至整个程序中断，它里面其他一些工具一定程度上简化了各种判断和默认值的书写，但还不够彻底。使用java8的Optional<T>加Lambdas加Streams的写法，在Guava基础上做的更加彻底，但比Groovy这种语言级别的支持略显笨重。  
建议先在框架的某个层面（比如控制层）把所有NPE处理掉，不要往下层流，尤其是在多个模块甚至多个系统之间交互的时候，最好能够一方处理掉这个，最起码保证另一方怎么写代码都不会跑NPE。其次才是用Optional<T>这样的工具来处理。最次是在接口doc上写清楚可能为null的情况。    

参考：  

*《Tired of Null Pointer Exceptions? Consider Using Java SE 8's Optional!》http://www.oracle.com/technetwork/articles/java/java8-optional-2175753.html  
*《避免Java中的空对象检查》http://it.deepinmind.com/java/2014/08/08/avoiding-null-checks-in-java.html  
*《[Google Guava] 1.1-使用和避免null》http://ifeve.com/google-guava-using-and-avoiding-null/  