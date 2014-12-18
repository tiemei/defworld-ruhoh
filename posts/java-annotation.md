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

* @Inherited 可以被subclasses继承
* @Documented 标识javaDoc中可以见这个annotation


参考：  

[Java Reflection(八):注解](http://ifeve.com/java-reflection-8-annotation/)  
[Java Annotations](http://tutorials.jenkov.com/java/annotations.html)  