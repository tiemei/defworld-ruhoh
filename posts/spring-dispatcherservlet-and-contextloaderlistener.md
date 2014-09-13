---
title: spring DispatcherServlet & ContextLoaderListener
date: '2014-09-13'
description:
categories: 'java'
---

实际上我们只要知道了servlet config和servlet conext的区别就能清楚spring中的DispatcherServlet和ContextLoaderListener有啥区别了。servlet config是单个servlet维度的，而servlet conext是整个application维度的，一个application可能有多个servlet。  
  
spring是基于servlet标准的serlvet为中心扩展的一个MVC框架（[webx](http://webx.github.io/)以filter为中心扩展的），DispatcherServlet正是这个中心。如下图，Front Controller就是指的DispatchServlet。    

![spring mvc](https://farm4.staticflickr.com/3851/15218580671_18437fa8a9.jpg)  
  
所以我们用类似这样的方式指定的实际是DispatchServlet范围内的context:  

```xml
<servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-context.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
</servlet>
```

而用这样的方式指定的是app范围的：  

```xml
<context-param> <!-- context-param配置的只是一个servlet context维度可以拿到的变量而已 -->
    <param-name>contextConfigLocation</param-name> 
    <param-value>/WEB-INF/applicationContext*.xml</param-value>
</context-param>

<listener><!-- 根据contextConfigLocation指定的配置，初始化spring app context -->
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

在代码层面整个过程如下：  

* 在任何filer和servlet还未初始化时，`ServletContextListener#contextInitialized`被容器调用
* 而spring提供的`ContextLoaderListener`实现了`ServletContextListener#contextInitialized`，也正是此时被调用初始化了spring root context
* `Servlet#init`在servlet container开始初始化servlet，还没有用它服务第一个request的时候，因为`DispatchServlet`实现了`FrameworkServlet#initServletBean`，而这个方法就是在此时被调用的。
* `FrameworkServlet#initServletBean`主要的工作就是初始化context，详见下面这段代码：  

```java
protected WebApplicationContext initWebApplicationContext() {
        // 这里会拿到ContextLoaderListener中初始化的root context
        WebApplicationContext rootContext =
                WebApplicationContextUtils.getWebApplicationContext(getServletContext());
        WebApplicationContext wac = null; // 代表当前servlet对应的context

        if (this.webApplicationContext != null) {
            // A context instance was injected at construction time -> use it
            wac = this.webApplicationContext;
            if (wac instanceof ConfigurableWebApplicationContext) {
                ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext) wac;
                if (!cwac.isActive()) {
                    // The context has not yet been refreshed -> provide services such as
                    // setting the parent context, setting the application context id, etc
                    if (cwac.getParent() == null) {
                        // The context instance was injected without an explicit parent -> set
                        // the root application context (if any; may be null) as the parent
                        cwac.setParent(rootContext);
                    }
                    configureAndRefreshWebApplicationContext(cwac);
                }
            }
        }
        if (wac == null) {
            // No context instance was injected at construction time -> see if one
            // has been registered in the servlet context. If one exists, it is assumed
            // that the parent context (if any) has already been set and that the
            // user has performed any initialization such as setting the context id
            wac = findWebApplicationContext(); // 这里通过contextAttribute参数指定某个特定的context作为当前servlet的context
        }
        if (wac == null) {
            // No context instance is defined for this servlet -> create a local one
            wac = createWebApplicationContext(rootContext); //  这里通过contextConfigLocation参数指定一个文件初始化当前servlet对应的context
        }

        if (!this.refreshEventReceived) {
            // Either the context is not a ConfigurableApplicationContext with refresh
            // support or the context injected at construction time had already been
            // refreshed -> trigger initial onRefresh manually here.
            onRefresh(wac);
        }

        if (this.publishContext) {
            // Publish the context as a servlet context attribute.
            String attrName = getServletContextAttributeName();
            getServletContext().setAttribute(attrName, wac);
            if (this.logger.isDebugEnabled()) {
                this.logger.debug("Published WebApplicationContext of servlet '" + getServletName() +
                        "' as ServletContext attribute with name [" + attrName + "]");
            }
        }

        return wac;
    }
```



参考：  

* 《spring framework reference 3.2.x》17.2 The DispatcherServlet
* 《spring framework reference 3.2.x》5.14 Standard and Custom Events
* 《spring framework reference 3.2.x》6.7 Application contexts and Resource paths
* 《servlet config servlet context》http://stackoverflow.com/questions/4223564/servlet-config-servlet-context
