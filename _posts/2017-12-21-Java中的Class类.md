---
layout:     post
title:      Java中的Class类
subtitle:   函数式编程框架 ReactiveCocoa 进阶
date:       2017-12-21
author:     mushuzi
catalog: true
tags:
    - Java
---
我们知道，Java是一门面向对象的编程语言，在学习Java时，经常会听到“在Java中，一切皆对象”。那么，事实上是这样吗？这里需要明确的是，在Java中，有两样东西
不是面向对象的。一种是Java的普通数据类型，如int、char、boolean等，为了弥补这一点，Java为每一个基本数据类型定义了封装类，例如int型有其对应的Integer类
，char型有其对应的Character类。另一种则是Java中静态的东西（即被static修饰的方法或属性等），静态的东西不属于某个对象，而是属于类。

# 前言

>在[上篇文章](http://qiubaiying.github.io/2016/12/26/ReactiveCocoa-基础/)中介绍了**ReactiveCocoa**的基础知识,接下来我们来深入介绍**ReactiveCocoa**及其在**MVVM**中的用法。


![ReactiveCocoa进阶思维导图](https://ww3.sinaimg.cn/large/006y8lVagw1fbgye3re5xj30je0iomz8.jpg)
# 常见操作方法介绍


#### 操作须知

所有的信号（RACSignal）都可以进行操作处理，因为所有操作方法都定义在RACStream.h中，因此只要继承RACStream就有了操作处理方法。
#### 操作思想

运用的是Hook（钩子）思想，Hook是一种用于改变API(应用程序编程接口：方法)执行结果的技术.
