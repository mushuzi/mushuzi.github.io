---
layout:     post
title:      Java反射机制基础之Class类
subtitle:   Java中的Class类
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

在Java编程中，我们时常需要写一个类，从而实例化一个该类的对象来进行一系列操作。我们说实例化出来的对象是其对应的类的对象，那么，类是对象吗？如果是的话，类是谁的对象呢？

# 正文
针对前言中给出的“类是否是对象？”的问题，这里给出肯定的回答：类是对象。类是java.lang.Class类的实例对象。

值得注意的是：很多人可能会把Class类和class这个词混淆，针对这一点，有这样一句解释：There is a class named Class"，即有一个名称为Class的类，相信可以帮助读者区分这两个概念。

既然Class是一个类，那自然而然我们会关心如何才能得到一个Class的对象？对于普通的对象，为了方便下文叙述，我们自定义一个Student类，可以通过Student s1 = new Student()的方式来new一个Student类的对象。但是请注意：我们不能通过这种new的方式得到Class类的对象！！！这是因为在Java对Class类进行定义的源代码中，Class类的构造方法是用private修饰的，因此只有Java虚拟机自己才可以创建Class类的实例对象。而我们无法外部调用Class的构造方法，如果我们试图用Class c = new Class()的方式创建对象，则Java虚拟机会报错。

那究竟如何才能创建Class类的实例对象呢？有以下三种方式：
1.通过Class c1 = Student.class;的方式。我们前面解释过，Student类是Class类的实例对象，而这里隐含了一个知识点：任何类都有一个隐含的静态成员变量class。 因此我们通过类名.class就可以获得Class类的一个实例对象。

这里我们对class关键字进行一些扩展叙述。在Java中，基本数据类型（例如int），关键字（例如void），已存类（例如Integer）和自定义类（例如Student），都有一个静态成员变量class。例如Class cc = int.class; 打印cc结果为int； Class cc2 = Integer.class; 打印cc2的结果为java.lang.Integer。

2.已知一个类的实例对象，通过getClass()方法可以获取该类的实例对象，代码如下：  

Student s1 = new Student();  

Class c2 = s1.getClass();

3.通过Class类的forName()方法来获取Class类的实例对象，代码如下:  

Class c3 = Class.forName("Student类的全类名");  

当然上述代码在实际编程中需要捕获异常，因为可能找不到Student这个类。

官方文档在这里对c1,c2,c3有这样的表述：c1,c2,c3都表示了Student类的类类型（class type）。通过上述三种方式获取到的Class的实例对象c1,c2,c3，如果我们用"=="来判断这三个量是否相等，你猜结果会是什么？没错，结果会返回true，即c1==c2==c3，因为c1,c2,c3都代表了Student类的类类型，而一个类只可能有一个类类型！

得到了Student类的类类型，我们就有另一种创建Student类的实例对象的方式了，即使用Class类的newInstance()方法，代码如下：  

Student s = (Student) c1.newInstance();  

当然，通过c1.newInstance()创建出来的对象还需要进行强制转化，使其成为Student类型。

最后，在实际编程中，我们经常会遇到类的对象作为方法输入参数的情况。例如方法：public static void printClassMeaasge(Object obj),完成功能：输入一个Object类或者其子类类型的对象obj，然后打印该类的类名和成员方法等信息。我们可以通过Class c = obj.getClass()获取该类的类类型，然后c.getName()获取类名，然后利用getMethods()方法获取到该类的成员方法集合。当然这将涉及到另一个类Method，因为方法也是对象，一个成员方法就是一个Method对象，Method类中封装了对成员方法的操作。

# 总结
正文中对Class类的介绍，以及获取类的类类型的方式，是Java反射机制的绝对基础，必须牢固掌握。后面也将会进一步对Java反射机制加以介绍，希望能在总结和记录中与读者朋友们共同进步~
