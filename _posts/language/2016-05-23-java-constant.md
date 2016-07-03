---
layout: post
title: Java 常量的深入理解
category: 编程语言
tags: Java
keywords: Java
description: Java 常量的深入理解
---
### 问题的引出

- 在 Java 中，声明常量我们通常使用 static final 或者 final static。(两者没有区别)

- 我们知道 static 修饰的变量表示 JVM 装载类的时候就会被初始化，而一般的实例字段在每次创建实例时都会被实例化。从内存结构来说，实例变量是存储在堆内存中，而static变量则是存储在方法区中的。

- 而一个字段被 final 修饰则表示这个字段在初始化完成之后就不可再改变了。

### 比较

* final int a; // 表示常量，存储在堆内存，且必须在实例初始化的时候，完成赋值操作,且只能赋值一次。（在声明变量的时候赋值，或者在构造方法中赋值）

* static int a; // 表示静态字段，存储在方法区。

* final static int a; // 表示常量，存储在方法区。
