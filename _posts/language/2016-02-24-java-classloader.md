---
layout: post
title: Java 类加载器深入理解
category: 编程语言
tags: Java
keywords: Java
description: Java 类加载器深入理解
---

### 类加载机制

1. 首先根据java后的运行模式配置项或<JAVA_HOME>/jre/lib/i386/jvm.cfg来决定是以client还是server模式运行JVM，然后加载<JAVA_HOME>/jre/bin/client或server/jvm.dll，并开始启动JVM；

2. 在启动JVM的同时将加载Bootstrap ClassLoader（启动类加载器，使用C/C++编写，属于JVM的一部分）；

3. 通过Bootstrap ClassLoader加载**sun.misc.Launcher**类（ExtClassLoader和AppClassLoader是它的内部类）；

4. sun.misc.Launcher类在执行初始化阶段时，会创建一个自己的实例，在创建过程中会创建一个ExtClassLoader（扩展类加载器）实例、一个AppClassLoader（系统类加载器）实例，并将AppClassLoader实例设置为主线程的ThreadContextClassLoader（线程上下文类加载器）。

5. 然后AppClassLoader实例就开始加载Main.class及其所依赖的类库了


![类加载机制](http://images.cnitblog.com/blog/347002/201502/110912451518649.gif)

JVM 是通过 一个称为 ClassLoader 的东西 来加载 class 文件，每当 JVM 启动的时候，就会产生 三个 ClassLoader，它们分别是Bootstrap Loader， ExtClassLoader 和 AppClassLoader。 这三个 ClassLoader 的作用是不同的，它们所加载的class 文件也不同。



- BootStrap Loader：作为JVM的一部分无法在应用程序中直接引用，由C/C++实现。负责加载①\<JAVA_HOME\>/jre/lib目录 或 ②-Xbootclasspath参数所指定的目录 或 ③系统属性sun.boot.class.path指定的目录 中特定名称的jar包。在JVM启动时将通过Bootstrap ClassLoader加载rt.jar，并初始化sun.misc.Launcher从而创建Extension ClassLoader和System ClassLoader实例，和将System ClassLoader实例设置为主线程的默认Context ClassLoader（线程上下文加载器）。

_注意：Bootstrap ClassLoader只会加载特定名称的类库，如rt.jar等。假如我们自己定义一个jar类库丢进<JAVA_HOME>/jre/lib目录下也不会被加载的！_

```java
    @Test
    public void testGetBootstrapClassPath(){
        URL[] urls = Launcher.getBootstrapClassPath().getURLs();
        for (URL url : urls)
            System.out.println(url.toExternalForm());
    }
    /*输出结果
     file:/C:/Java/jdk1.7.0_79/jre/lib/resources.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/rt.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/sunrsasign.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/jsse.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/jce.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/charsets.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/jfr.jar
     file:/C:/Java/jdk1.7.0_79/jre/classes*/
```

- ExtClassLoader：扩展类加载器，负责加载 <JAVA_HOME>/lib/ext 下的类库，或者被系统变量 java.ext.dirs 指定的路径下的所有的类库
- AppClassLoader：应用程序类加载器，负责加载用户指定的classpath下的类加载器


### 双亲委派模型



### 自定义类加载器


