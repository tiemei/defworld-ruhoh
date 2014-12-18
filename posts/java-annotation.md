---
title: 笔记 - java annotation
date: '2014-12-18'
description:
categories: 'java'
---

# 分类

java annotation主要分为以下几类：  

* Compiler instructions
* Build-time instructions
* Runtime instructions

Compiler instructions比如Java's Builtin Annotations，give instructions to the Java compiler：    

* @Deprecated
* @Override
* @SuppressWarnings

The build process includes generating source code, compiling the source, generating XML files (e.g. deployment descriptors), packaging the compiled code and files into a JAR file etc. Building the software is typically done by an automatic build tool like Apache Ant or Apache Maven. Build tools may scan your Java code for specific annotations and generate source code or other files based on these annotations. Build-time instructions比如


# 构成

java annotation主要构成：  

* Annotation Elements

        You can use all primitive data types as element data types. You can 
        also use arrays as data type. You cannot use complex objects as data type.

* Annotation Placement,@Target: 

        You can put Java annotations above classes, interfaces, methods, 
        method parameters, fields and local variables.

* Java Annotation Purposes,@Retention: 

        
        /**
         * Annotations are to be discarded by the compiler.
         * (If you create your own annotations for use with build tools that scan the code, you can use this retention policy.)
         */
        SOURCE,
    
        /**
        * Annotations are to be recorded in the class file by the compiler
        * but need not be retained by the VM at run time.  This is the default
        * behavior.
        */
        CLASS,
        
        /**
        * Annotations are to be recorded in the class file by the compiler and
        * retained by the VM at run time, so they may be read reflectively.
        *
        * @see java.lang.reflect.AnnotatedElement
        */
        RUNTIME

* @Inherited 可以被subclasses继承，只能用在class上
* @Documented 标识javaDoc中可以见这个annotation

# 难点：理解CLASS、RUNTIME区别

[这个stackoverflow](http://stackoverflow.com/questions/3107970/annotations-retention-policy)提到RetentionPolicy.CLASS实际上 "Discard during class load.Useful when doing bytecode-level post-processing. "，而RetentionPolicy.RUNTIME: Do not discard. 我们进一步看什么叫做bytecode-level post-processing。[这个stackoverflow](http://stackoverflow.com/questions/15268057/where-does-bytecode-injection-happen)提到，我们可以借助一些工具在多个阶段对bytecode进行处理。例如 Javassist / ASM这两个工具可以做到的。可以参考fastjson的源码，来理解这两个个工具如何使用。


但是，似乎还是不能解释，因为我遇到过一个case，用CLASS级别，同样可以通过反射库拿到这些标签。[这个stackoverflow](http://stackoverflow.com/questions/21167610/does-reflections-library-ignore-the-retentionpolicy?rq=1)解释这个事情是"Though “need not be retained” does not sound like a strong requirement."，说明并不是严格限制，如果你用第三方反射库或者自定义annotation，结果不一定如此。    

## fastjson如何使用反射

待续





参考：  

[Java Reflection(八):注解](http://ifeve.com/java-reflection-8-annotation/)  
[Java Annotations](http://tutorials.jenkov.com/java/annotations.html)  
[Lesson: Annotations](http://docs.oracle.com/javase/tutorial/java/annotations/)  
[Java annotation wiki](http://en.wikipedia.org/wiki/Java_annotation#Processing)      