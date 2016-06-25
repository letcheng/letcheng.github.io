---
layout: post
title: Java 引用关系
category: 编程语言
tags: Java
keywords: Java
description: Java 引用关系
---

### 四种引用关系

- 强引用
  
  最常用的引用类型，如Object obj = new Object(); 。只要强引用存在则GC时则必定不被回收。

- 软引用
  
  用于描述还游泳但非必须的对象，当堆将发生OOM（Out Of Memory）时则会回收软引用所指向的内存空间，若回收后依然空间不足才会抛出OOM。
  一般用于实现内存敏感的高速缓存。

- 弱引用

  发生GC时必定回收弱引用指向的内存空间。

- 虚引用

  

JVM 是通过 一个称为 ClassLoader 的东西 来加载 class 文件，每当 JVM 启动的时候，就会产生 三个 ClassLoader，它们分别是Bootstrap Loader， ExtClassLoader 和 AppClassLoader。 这三个 ClassLoader 的作用是不同的，它们所加载的class 文件也不同。

- BootStrap Loader：启动类加载器，负责加载 <JAVA_HOME>/lib 下的类库，或者被-Xbootclasspath指定的路径中的类库
- ExtClassLoader：扩展类加载器，负责加载 <JAVA_HOME>/lib/ext 下的类库，或者被系统变量 java.ext.dirs 指定的路径下的所有的类库
- AppClassLoader：应用程序类加载器，负责加载用户指定的classpath下的类加载器


### 双亲委派模型



### 自定义类加载器


