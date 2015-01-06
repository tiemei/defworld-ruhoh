---
title: 关系型数据库笔记
date: '2014-12-05'
description: 
categories: 'DB'
---

# 1NF 2NF 3NF ...

范式（NF）：一张数据表的表结构所符合的某种设计标准的级别。要求逐级提高，而高级的同时满足低级的。  

## 1NF

![1NF](https://farm9.staticflickr.com/8616/15925324316_09846f9765_z.jpg) 

如上图进货和销售又细分了属性，不满足1NF。  
每个属性都不可再分。因为真实的DB每列都不可再分的，所以能存到真实DB里就说明满足1NF。

## 2NF

![2NF](https://farm8.staticflickr.com/7495/15786427597_96178e86ab.jpg)  

如上图，满足1NF，但存在以下问题：  

1. 每一名学生的学号、姓名、系名、系主任这些数据重复多次。每个系与对应的系主任的数据也重复多次——**数据冗余过大**
2. 新建一个系，无法将系名与系主任的数据单独添加到数据库——**插入异常** 
3. 删除某个系所欲学生记录，这个系的数据被删除掉 —— **删除异常** 
4. 某同学转系，需要修改三条记录 —— **修改异常** 

先介绍几个概念:  

* 码：K为某表的一个属性或属性组，它是唯一的，即其他属性完全函数依赖于K，称K为候选码，简称码。选择其中一个作为主码
* 函数依赖：一张表中，在属性（或属性组）X值确定的情况下，必能能确定属性Y的值，那么就可以说Y函数依赖于X
* 完全函数依赖：若Y函数依赖X，且对于X的任何一个真子集X'（假如属性组X包含超过一个属性的话），Y函数依赖于X'不成立，称Y对X完全函数依赖。

        （学号，课名） F→ 分数 （注：因为同一个的学号对应的分数不确定，同一个课名对应的分数也不确定）

* 部分函数依赖：不完全函数依赖时反过来就是部分函数依赖。
* 传递函数依赖：假如 Z 函数依赖于 Y，且 Y 函数依赖于 X （严格来说还有一个X 不包含于Y，且 Y 不函数依赖于Z的前提条件），那么我们就称 Z 传递函数依赖于 X 
* 非主属性：包含在任何一个码中的属性成为主属性

2NF定义：2NF在1NF的基础之上，消除了非主属性对于码的部分函数依赖。判断步骤：  

* 第一步：找出数据表中所有的码。
* 第二步：根据第一步所得到的码，找出所有的主属性。
* 第三步：数据表中，除去所有的主属性，剩下的就都是非主属性了。
* 第四步：查看是否存在非主属性对码的部分函数依赖。  

![2NF-2](https://farm9.staticflickr.com/8619/15784930058_d21ff87fe3.jpg)  

```
码：（学号、课名）
主属性有两个：学号 与 课名
非主属性有四个：姓名、系名、系主任、分数

对于（学号，课名） → 姓名，有 学号 → 姓名，存在非主属性 姓名 对码（学号，课名）的部分函数依赖。
对于（学号，课名） → 系名，有 学号 → 系名，存在非主属性 系名 对码（学号，课名）的部分函数依赖。
对于（学号，课名） → 系主任，有 学号 → 系主任，存在非主属性 对码（学号，课名）的部分函数依赖。

所以表3存在非主属性对于码的部分函数依赖，最高只符合1NF的要求，不符合2NF的要求。
```

那就要进行模式分解，分解成下面两个表：  

```
选课（学号，课名，分数）
学生（学号，姓名，系名，系主任）
```

新的函数依赖图：  

![2NF-3]()  

满足了2NF，还是存在一些问题：  

* 删除某个系中所有的学生记录：该系的信息仍然全部丢失。——无改进
* 插入一个尚无学生的新系的信息：因为学生表的码是学号，不能为空，所以此操作不被允许。——无改进

## 3NF

3NF在2NF的基础之上，消除了非主属性对于码的传递函数依赖。  

对于选课表，主码为（学号，课名），主属性为学号和课名，非主属性只有一个，为分数，不可能存在传递函数依赖，所以选课表的设计，符合3NF的要求。

对于学生表，主码为学号，主属性为学号，非主属性为姓名、系名和系主任。因为 学号 → 系名，同时 系名 → 系主任，所以存在非主属性系主任对于码学号的传递函数依赖，所以学生表的设计，不符合3NF的要求。。

进一步模式分解：  

```
选课（学号，课名，分数）
学生（学号，姓名，系名）
系（系名，系主任）
```


# How to Store a Tree?

## 邻接表



## 枚举路径

## 嵌套集

## 闭包表


# 几种操作DB工具

## java的jdbc

jdbc这边包括几个概念，如下图：  

![jdbc](https://farm8.staticflickr.com/7488/15839289920_b67e7d5e88.jpg)   

写代码时面对的是jdbc api，统一的，而怎么连接具体类型的db由具体的JDBC Driver实现，方便在程序切换DB。  

```java
public class JDBCDemo {
    public staic void main(String args[]) {
        /* get conn */
        Class.forName("com.mysql.jdbc.Driver").newInstance();
        String url = "jdbc:mysql://localhost/coffeebreak";
        Connection conn = DriverManager.getConnection(url, "username", "password");

        /* query db */
        String query = "SELECT COF_NAME, PRICE FROM COFFEES";
        Statement st = conn.createStatement();
        ResultSet rs = st.executeQuery(query);
        while (rs.next()) {
            String s = rs.getString("COF_NAME");
            float n = rs.getFloat("PRICE");
            System.out.println(s + "   " + n);
        }

        /* close conn */
        conn.close();
    }
}
```

## java的ibatis

DB列名和Do对象的映射，通过sql.xml配置文件配置，不需要像jdbc代码手写。  

## java的hibernate

略

## RoR

# 事务

## mysql事务

上层的封装其实依赖底层DB支持，mysql如何支持，后续再展开。  

## jdbc事务

### JDBC事务隔离级别

JDBC定义了五种事务隔离级别：  

* TRANSACTION_NONE JDBC驱动不支持事务
* TRANSACTION_READ_UNCOMMITTED 允许脏读、不可重复读和幻读。
* TRANSACTION_READ_COMMITTED 禁止脏读，但允许不可重复读和幻读。
* TRANSACTION_REPEATABLE_READ 禁止脏读和不可重复读，但允许幻读。
* TRANSACTION_SERIALIZABLE 禁止脏读、不可重复读和幻读

JDBC定义了SavePoint接口，提供在一个更细粒度的事务控制机制。当设置了一个保存点后，可以rollback到该保存点处的状态，而不是rollback整个事务。  
JDBC规范虽然定义了事务的以上支持行为，但是各个JDBC驱动，数据库厂商对事务的支持程度可能各不相同。为此，JDBC提供了DatabaseMetaData接口，提供了一系列JDBC特性支持情况的获取方法。  

```
脏读(dirty read)：一个事物读取了另一个事务尚未提交的数据
不可重复读(non-repeatable read) ：解决了脏读问题，一个事务的操作导致另一个事务前后两次读取到不同的数据
幻读(phantom read) ：解决了不可重复读问题。保证了同一个事务里，查询的结果都是事务开始时的状态（一致性）。但是，如果另一个事务同时提交了新数据，本事务再更新时，就会“惊奇的”发现了这些新数据。

脏读和不可重复读的区别是，脏读是读到未提交的数据，不可重复读读到的确实是已经提交的数据。  
```

### jdbc savepoint

将一个事务再细分，不是整个事务都要回滚。

## spring事务支持

### 隔离级别（isolation level）

参考jdbc事务隔离

### 事务只读（transaction readonly）

Spring只是提供设置入口，最终起作用是在ORM框架和JDBC impl层，这里[有篇文章](http://www.codeinstructions.com/2009/04/read-only-transactions-with-spring-and.html)

### 事务传播（transaction propagation）

```
outer transaction : 外部methodA对应的transaction
inner transaction : methodA调用的methodB对ing的transaction
```

以下说明，相似的实现带扣Spring源码。  

#### Required

outer transaction和inner transaction公用一个physical transaction（对应DB transaction），但其实是两个独立的logical transaction。methodB失败，会在physical tranaction身上mark roolback only，然后methodB methodA都需要回滚。

#### RequiresNew

outer transaction和inner transaction各自一个physical transaction，commit / rollback都是独立的。  

#### Nested

跟RequiresNew不同的是，用transaction的savapoint概念来实现，methodB开始之前启动一个savapoint，methodB失败，只是这个savapoint回滚，而整个transaction可以不回滚。   

参考:  

[知乎：解释一下关系数据库的第一第二第三范式？](http://www.zhihu.com/question/24696366)  
[一个教程](http://www3.ntu.edu.sg/home/ehchua/programming/index.html)      
[oracle官方jdbc教程](http://docs.oracle.com/javase/tutorial/jdbc/overview/index.html)  
《Mysql技术内幕-InnoDB存储引擎》  
《Marking Rollback Only》https://docs.oracle.com/cd/E23095_01/Platform.93/ATGProgGuide/html/s1204markingrollbackonly01.html  
《spring-reference-3.2x Transaction propagation》  
《Spring声明式事务管理与配置详解》http://www.cnblogs.com/hellojava/archive/2012/11/21/2780694.html  
《HTTP幂等性概念和应用》http://coolshell.cn/articles/4787.html  
