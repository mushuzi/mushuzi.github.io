---
layout:     post
title:      Java Web基础总结之Servlet
subtitle:   Java Web基础总结之Servlet
date:       2018-04-21
author:     mushuzi
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
   - Java
   - web
   - Servlet
---

Servlet在MVC思想中承担着Controller这一重要角色，虽然项目中常有用到，但对其很多基本概念掌握得还是很模糊，这里做一个简单的总结。

## Servlet相关接口以及类
1. Servlet接口：该接口主要提供了三个方法：init()、service()和destory()；其中：init()方法用来对容器进行初始化，destory()方法用户销毁容器，service()方法用来决定执行哪个具体的响应方法来响应用户请求（即通过service()方法来调用doGet()或doPost()之类的方法）。
2. GenericServlet接口：该接口实现了Servlet接口，实现了一个与协议无关的Servlet；
3. HTTPServlet抽象类：该抽象类通过继承GenericServlet抽象类，实现了Http协议下的Servlet，其中提供了与Http请求类型相关的一些处理方法，例如doGet()、doPost()等等；
4. 用户自定义Servlet：一般需要用户自定义一个继承自HttpServle类的Servlet类，需要根据业务需求重写其中的doGet()和doPost()方法。

## Servlet的执行顺序
假定我们在servlet包底下定义了一个名为HelloServlet的自定义Servlet类，并且在其中重写了doPost()和doPost()方法。

相应的web应用的配置文件web.xml中有如下配置：

    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>servlet.HelloServlet</servlet.class>
    <servlet>
    <servlet-mapping>
        <servlet-name>HelloServlet</servlet-name>
        <url-pattern>/servlet/HelloServlet</url-pattern>
    </servlet-mapping>

然后，我们使用下列语句在浏览器页面上定义一个超链接：

    <a href = "servlet/HelloServlet">


web应用启动后，当用户点击该超链接时，Servlet会经历如下的执行顺序：
1. 服务器在web.xml配置文件的<servlet-mapping><servlet-mapping>标签下的<url-pattern></url-pattern>中寻找与被点击的链接"servlet/HelloService"对应的URL地址;
2. 服务器根据找到的URL地址在<servlet-mapping><servlet-mapping>标签下的<servlet-name></servlet-name>中找到与之对应的Servlet的名字HelloServlet；
3. 服务器找到Servlet的名字HelloServlet后再到<servlet></servlet>标签下的<servlet-class></servlet-class>中寻找与Servlet名字相对应的处理类servlet.HelloServlet；
4. 执行处理类servlet.HelloServlet中的相应的请求方法来响应用户请求。

## Servlet生命周期
1. 容器启动，会调用/conf/server.xml文件，以确定启动端口和web应用存放的路径；
2. web容器启动会读取每个web应用的web.xml文件，并解析得到servlet配置信息。
3. 容器加载并实例化servlet（如果web.xml文件中有超过一个启动容器的配置，则实例化servlet，如果没有则在第一次请求时进行实例化）
4. 容器调用init()方法对servlet进行实例化；
5. 调用service()方法处理请求。
6. 容器调用destory()方法丢弃servlet。

其中，只有步骤5的粒度是“请求”级别的，即每次请求都会执行步骤5.其他步骤在整个servlet生命周期中只会执行一次，保证servlet只有一个。总结而言：单实例，多线程。

## Servlet容器装载Servlet的三种情况：
1. Servlet容器在启动时自动装载某些Servlet，实现它只需要在web.xml文件中的<servlet></servlet>标签下使用<load-startup></load-startup>子标签进行指定。例如：<load-startup>1</load-startup>。子标签中间的数字越小，表示装载的优先级越高。
2. 在Servlet容器启动后，客户首次向Servlet发送请求时，容器会装载Servlet；
3. Servlet类文件被更新后，容器会重新装载Servlet。

注意：Servlet被装载后，Servlet容器会创建一个Servlet实例并且调用Servlet的init()方法对其进行初始化。在Servlet的整个生命周期中，init()方法只被调用一次。也就是Servlet生命周期中所说的“单实例”的含义。
