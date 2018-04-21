---
layout:     post
title:      Java Web基础总结之JSP
subtitle:   Java Web基础总结之JSP
date:       2018-04-21
author:     mushuzi
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
   - Java
   - web
---

JSP（Java Server Pages）：其根本上是一个简化的
Servlet设计，它实现了在Java当中使用HTML标签。JSP是一种动态网页技术标准，JSP和Servlet一样，都是在服务器端执行的。本文围绕JSP，介绍其中的几个重要的概念。

## JSP页面的生命周期
1. 用户向欢迎页面（通常默认是index.jsp，可以通过配置welcome-page指定）发起请求；
2. 判断是否是第一次请求，如果是第一次请求，则JSP引擎把JSP文件转换成为一个Servlet（一个Servlet，从本质上说就是一个Java类），并生产字节码文件和执行jspInit()初始化方法（注意这个jspInit()方法只在编译生成字节码文件的时候才执行，即在整个生命周期中只执行一次）；如果不是第一次请求，则说明当前内存中已有相应的字节码文件，用户可直接访问该字节码文件。
3. 解析字节码文件，执行jspService()方法，来响应用户请求。其中jspService()方法用来处理客户端请求，对每一个请求，JSP引擎创建一个新的线程来处理，每一个客户端请求对应一个线程。

## get和post的区别
1. get方式请求参数放在URL后面，没有消息体，即以明文的方式通过URL提交数据，数据在URL中可以看到，例如使用get方式向一个页面提交登录信息，提交之后页面URL会有类似显示：localhost:8080/login.jsp?username=zhangsan&password=123456，可以看出安全性较低；post方式实际上是把表单数据封装到一个数据包中，放在消息体中提交，因为安全性较高。
2. get方式主要用于资源查找，表示客户端需要查找服务器上的某个资源；post方式主要用于数据传输，主要用于服务器接收客户端传递过来的信息。

## JSP九大内置对象
1. out
2. request
3. response
4. session
5. application
6. page
7. pageContext
8. exception
9. config

其中前五个是最常用的内置对象，后四个不太常用。

### out内置对象
out对象是JSPWriter类的实例，负责向客户端输出内容。

常用方法如下：
* void println() 向客户端打印字符串
* void clear() 清除缓存区的内容，如果在flush之后调用会抛出异常
* void clearBuffer() 清除缓存区的内容，如果在flush之后调用不会抛出异常
* void flush() 将缓冲区内容输出到客户端
* int getBufferSize() 返回缓冲区字节数的大小
* int getRemaining() 返回缓冲区还剩余多少可用
* boolean isAutoFlush() 返回缓冲区满时，是自动清空还是抛出异常
* void close() 关闭输出流

### request内置对象
request是HttpServletRequest类的实例，它封装了客户端的请求信息，在完成客户端的请求之前，该对象一直有效。

常用方法如下：
* String getParameter(String name) 获取名为name的参数的值
* String[] getParameterValues(String name) 返回包含参数name的所有值的数组
* void setAttribute(String, Object) 存储此请求中的属性
* object getAttribute(String name) 返回指定name对应的属性值
* String getProtocol() 返回请求用的协议类型以及版本号
* String getServerName() 返回接收请求的服务器主机名
* int getServerPort() 返回服务器接收此请求所用的端口号
* String getCharacterEncoding() 返回字符编码方式
* void setCharacterEncoding() 设置请求的字符编码方式
* int getContentLength() 返回请求体的长度（单位为字节）
* String getRemoteAddr() 返回发送请求的客户端的IP地址
* String getRealPath(String path) 返回一虚拟路径的真实路径
* String request.getContextPath() 返回上下文路径

### response内置对象
response是HttpServletResponse类的实例，它包含了响应客户请求的有关信息。response具有页面作用域，当访问一个页面时，该页面内的response对象只对这次访问有效，其他页面的response对象对当前页面无效。

常用方法如下：
* String getCharacterEncoding() 返回响应用的是何种字符编码
* void setContentType(String type) 设置响应的MIME类型
* PrintWriter getWriter() 返回可以向客户端输出字符的一个对象（response.getWriter()获取的输出流在打印时总是提前于内置的out对象）
* sendRedirect(String location) 重新定向客户端的请求到指定JSP页面

#### 请求重定向和请求转发
这里对客户端的请求重定向sendRedirect()和服务器端的getRequestDispatcher().forward(req,resp)进行以下辨析：

* 请求重定向（类比重新拨号）：属于客户端行为，是response内置对象的方法，即response.sendRedirect()，从本质上讲等同于两次请求，前一次的请求对象不会在服务器端保存，地址栏的URL地址会改变
* 请求转发（类比呼叫转移）：属于服务器端行为，是request内置对象的方法，即request.getRequestDispatcher().forward(req,resp).从本质上讲属于一次请求，转发后请求对象会在服务器端保存，浏览器地址栏的URL不会发生改变。

### session内置对象
session是保存用户状态的一种机制，session是保存在服务器内存中的，和用户是一一对应的关系，session是HTTPSession类的实例。

从一个客户打开浏览器并连接到服务器开始，到客户关闭浏览器离开这个服务结束，被称为一个session。当客户访问一个服务器时，可能会在好几个页面之间来回切换，服务器就是通过session对象来确定这些切换行为是不是属于同一个客户的。

#### session的生命周期
1. 创建：当客户端第一次访问某一jsp或者servlet时，服务器会为当前会话创建一个sessionId.每次客户端向服务器发送请求时，都会将此sessionId携带过去，服务端会对sessionId进行验证。
2. 活动：某次会话中用过超链接打开的新页面属于同一个会话；只要当前页面没有全部关闭，重新打开新的浏览器窗口访问同一个项目资源时属于同一次会话（除非关闭所有页面）。注意：尽管产生新的sessionId，但是原有会话的sessionId依然存在，只不过旧的sessionId仍然存在于服务器端，不再参与校验而已，即创建新的会话并不意味着原有会话结束。
3. 销毁：有三种情况可以销毁一个会话：（1）调用session.invalidate()方法；（2）session过期，自动销毁；（3）服务器重新启动。

#### session与cookie
我们知道session和cookie都是保存用户状态的方式，但它们之间还是有很大的区别，以下提供两者之间的辨析：

* session是在服务器端的内存中保存用户信息；cookie是在客户端以文本文件的形式保存用户信息；
* session中保存的object对象类型；cookie中保存的是String字符串类型；
* session随着会话的结束而将其中存储的数据销毁；cookie可以长期保存在客户端，除非手动清空或者有效期截止；
* session一般用来保存比较重要的、安全性较高的信息；cookie一般用来保存相对而言安全性较低的、不重要的信息。

### application对象
application对象是ServletContext类的实例，它实现了用户数据的共享，可以存放全局变量。在用户的前后连接和不同用户的连接中，可以对application对象的同一属性进行操作，在任何地方对application对象属性的操作，都将影响到其他用户对此的访问。

application对象开始于服务器的启动，终止于服务器的关闭。服务器的启动和关闭决定了application的生命。

常用方法如下：
* void setAttribute(String name, Object value) 使用指定name将对象绑定到此对话
* object getAttribute(String name) 返回与此会话中的指定名称绑定在一起的对象
* Enumeration getAttributeNames() 返回所有可用属性名的枚举
* String getServerInfo() 返回JSP（Servlet）引擎名及版本号。
