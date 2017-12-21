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

# 前言
我们知道，Java是一门面向对象的编程语言，在学习Java时，经常会听到“在Java中，一切皆对象”。那么，事实上是这样吗？这里需要明确的是，在Java中，有两样东西
不是面向对象的。一种是Java的普通数据类型，如int、char、boolean等，为了弥补这一点，Java为每一个基本数据类型定义了封装类，例如int型有其对应的Integer类
，char型有其对应的Character类。另一种则是Java中静态的东西（即被static修饰的方法或属性等），静态的东西不属于某个对象，而是属于类。

在Java编程中，我们时常需要写一个类，从而实例化一个该类的对象来进行一系列操作。我们说实例化出来的对象是类的对象，那么，类是对象吗？如果是的话，类是谁的对象呢？

