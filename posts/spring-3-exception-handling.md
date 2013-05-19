---
title: spring异常处理
date: '2013-05-19'
description: spring异常处理
categories: "java"
tags: spring
---
## 我经手的两个项目中异常处理如何做？

**项目一**  
  所有对外接口均返回json数据，现有的异常处理是实现spring容器提供的
HandlerExceptionResolver，所有的异常均返回一个静态的错误页面。  
**项目二**  
返回html也返回json数据，同样实现HandlerExceptionResolver，针对请求html
的情况，返回错误页面；而针对请求Json数据的情况，返回一个json数据，并告诉
调用方resultCode+resultMsg。  
  
显然项目二针对json数据设置resultcode的方法更合适  
  
## spring提供了异常处理方案？
 
首先，重温下servlet container中的异常处理如何做：  
**before servlet 3:**  
我们用web.xml的error-page元素在error-code或exception-type两个粒度规定发生异常时
应该返回哪个页面。错误页面中可显示部分动态内容：java.servlet.error.statuts_code
和javax.servlet.error.message   
**serlet 3**:  
可以直接映射到一个path:  
    \<error-page\> 
      \<location\>/error\</location\>
    \</error-page\>

然后，你可以实现一个controller或者一个jsp页面来返回错误信息。不过你还是只能拿到
java.servlet.error.statuts_code和javax.servlet.error.message等最基本的信息就是了。
  
spring提供的常用方案是：  

* 实现HandlerExceptionResolver
* 直接使用spring实现好的SimpleMappingExceptionResolver：定义exception class与error page的映射
* @ExceptionHandler在controller中定义（当前controller，父类中的会被继承）
* @ExceptionHandler在@ControllerAdvice class中定义(any controller)

前两种方案会强制要求你返回一个ModelAndView对象，也就是只能定位到一个view。  
如果需要对入参和返回值有更灵活的控制，用下面两种。比如，你不是需要返回一个view,
而是希望直接往resp body中写东西。  

## Standard Spring MVC Exceptions
在spring处理request过程中可能跑出spring mvc定义的异常，用上面的第一第二种方案将异常映射到
error page固然能处理掉。不过为了client更方便处理，你可能希望设置resp code。DefaultHandlerExceptionResolver
正是做了这件事，它被自动注册了，无需使用者关心。  
DefaultHandlerExceptionResolver设置了resp code，不过你可能希望自定义error view(试试这两种方式：ContentNeogitatingViewResolver,
MappingJacksonJsonView)。同样的问题，你不仅需要控制error page，甚至希望自己动态生成resp body，用@ExceptionHandler方式
可以拿到目的，不过可以尝试继承ResponseEntityExceptionHandler，因为它已经帮你做了很多。  

## @ResponseStatus
ResponseStatusExceptionResolver会将设置了@ResponseStatus的状态全部放到resp中.  
默认DispatcherServlet已经注册了ResponseStatusExceptionResolver  

## 总结
故项目二的也有可改进空间：  
1. 返回的json数据还是通过渲染vm的方式，可以替换直接返回responseEntity
2. 对其他异常的处理过于简单（也可能是业务需求即如此）
3. 没有正确设置resp code
