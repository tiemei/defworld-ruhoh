---
title: spring annotation note
date: '2013-05-23'
description: spring annotation 
categories: "java"
tags: 

---

http://www.mkyong.com/spring/spring-auto-scanning-components/

# JavaConfig

参考:  
[Spring 3 JavaConfig Example](http://www.mkyong.com/spring3/spring-3-javaconfig-example/)  
[Spring 3 JavaConfig @Import Example](http://www.mkyong.com/spring3/spring-3-javaconfig-import-example/)  

![java config](http://farm8.staticflickr.com/7445/13234177294_d12d406149.jpg)  
![java config import](http://farm4.staticflickr.com/3744/13234269634_753f991942.jpg)  
  
JavaConfig用来替代最原始的xml配置方式，将bean的声明放到class文件中  

# 常用annotation

## 常用class声明标记

参考:  
[spring-auto-scanning-components](http://www.mkyong.com/spring/spring-auto-scanning-components/)  
[spring-filtering-components-in-auto-scanning](http://www.mkyong.com/spring/spring-filtering-components-in-auto-scanning/)  


一般在class上声明这些组件，但需要加上自动扫描。自动扫描支持用正则指定只扫描
一部分class，或者指定不扫描一部分class.  

* @Component  Indicates a auto scan component
* @Repository – Indicates DAO component in the persistence layer.
* @Service – Indicates a Service component in the business layer.
* @Controller – Indicates a controller component in the presentation layer.

### Auto-Wiring

参考:  
[Spring Auto-Wiring Beans](http://www.mkyong.com/spring/spring-auto-wiring-beans-in-xml/)  
[spring-properties-dependency-checking](http://www.mkyong.com/spring/spring-properties-dependency-checking/)  

#### spring支持以下几种注入方式

1. no - 需手动声明
2. byName - 通过setter方法
3. byType - 通过setter方法
4. constructor - 调用构造函数
5. autodetect - 先走构造器，否则走byType

#### 依赖检查

参考  
[spring-dependency-checking-with-required-annotation](http://www.mkyong.com/spring/spring-dependency-checking-with-required-annotation/)  
[spring-define-custom-required-style-annotation](http://www.mkyong.com/spring/spring-define-custom-required-style-annotation/)  

dependency-check：  

* none – No dependency checking.
* simple – If any properties of primitive type (int, long,double…) and collection types (map, list..) have not been set, UnsatisfiedDependencyException will be thrown.
* objects – If any properties of object type have not been set, UnsatisfiedDependencyException will be thrown.
* all – If any properties of any type have not been set, an UnsatisfiedDependencyException will be thrown.

更多时候，用@Required检查某个几个field就够了，不需要dependency-check.  

#### @Autowired

参考:  
[Spring Auto-Wiring Beans With @Autowired Annotation](http://www.mkyong.com/spring/spring-auto-wiring-beans-with-autowired-annotation/)  

* @Autowired can be applied on setter method, constructor or a field  
* 默认如果标识了@Autowired字段找不到对应的bean，将抛出异常，可`@Autowired(required=false)`
这样关闭掉依赖检查。  
* @Qualifier

        // 指定装配某个名字的bean
        @Autowired
        @Qualifier("PersonBean1")
        private Person person;


### 插手bean创建和销毁过程

[spring-initializingbean-and-disposablebean-example](http://www.mkyong.com/spring/spring-initializingbean-and-disposablebean-example/)  
[Spring Init-Method And Destroy-Method Example](http://www.mkyong.com/spring/spring-init-method-and-destroy-method-example/)  
[spring-postconstruct-and-predestroy-example](http://www.mkyong.com/spring/spring-postconstruct-and-predestroy-example/)  

可继承一个接口实现创建(all bean properties have been set)和销毁(after Spring container is released the bean)时被调用的方法（不推荐），你还有两种选择：  
1. 在xml配置时指定init-method和destroy-method
2. @PostConstruct and @PreDestroy
