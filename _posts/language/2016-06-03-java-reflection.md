---
layout: post
title: Java 反射机制总结与思考
category: 编程语言
tags: Java
keywords: Java
description: Java 反射机制总结与思考
---

## 概述

Java反射机制可以让我们在编译期(Compile Time)之外的运行期(Runtime)检查类，接口，变量以及方法的信息。反射还可以让我们在运行期实例化对象，调用方法，通过调用get/set方法获取变量的值。


## Class 对象

Java中的所有类型包括基本类型(int, long, float等等)，即使是数组都有与之关联的Class类的对象。

```java
Class clazz = MyObject.class;
```

```java
String className = "com.ruyuapp.MyObject";
Class clazz = Class.forName(className); 
```

### 获取类名

```java
clazz.getName(); // 包含包名
clazz.getSimpleName();//不包含包名
```

### 获取修饰符

可以通过Class对象来访问一个类的修饰符，即public,private,static等等的关键字

```java
int modifiers = clazz.getModifiers();
```

修饰符都被包装成一个int类型的数字，这样每个修饰符都是一个位标识(flag bit)，这个位标识可以设置和清除修饰符的类型。
可以使用java.lang.reflect.Modifier类中的方法来检查修饰符的类型：

```java
Modifier.isAbstract(int modifiers);
Modifier.isFinal(int modifiers);
Modifier.isInterface(int modifiers);
Modifier.isNative(int modifiers);
Modifier.isPrivate(int modifiers);
Modifier.isProtected(int modifiers);
Modifier.isPublic(int modifiers);
Modifier.isStatic(int modifiers);
Modifier.isStrict(int modifiers);
Modifier.isSynchronized(int modifiers);
Modifier.isTransient(int modifiers);
Modifier.isVolatile(int modifiers);
```

### 包信息

```java
Package pack = clazz.getPackage();
```

通过Package对象你可以获取包的相关信息，比如包名，你也可以通过Manifest文件访问位于编译路径下jar包的指定信息，比如你可以在Manifest文件中指定包的版本编号。

### 父类

由于 Java 里面采用单继承，所以可以取得父类的clazz

```java
Class superclazz = clazz.getSuperClass();
```

### 实现的接口

```java
Class[] interfaces = clazz.getInterfaces();
```

由于一个类可以实现多个接口，因此getInterfaces();方法返回一个Class数组，在Java中接口同样有对应的Class对象。

> 注意：getInterfaces()方法仅仅只返回当前类所实现的接口。当前类的父类如果实现了接口，这些接口是不会在返回的Class集合中的，尽管实际上当前类其实已经实现了父类接口。

### 构造器

通过如下方式访问一个类的构造方法。



### 方法

通过如下方式访问一个类的所有方法。

```java
Method[] methods = clazz.getMethods();
```

### 变量

```java
Field[] methods = clazz.getFields();
```

### 注解

```java
Annotation[] annotations = clazz.getAnnotations();
```

## 构造器

利用Java的反射机制你可以检查一个类的构造方法，并且可以在运行期创建一个对象。这些功能都是通过java.lang.reflect.Constructor这个类实现的。

### 获取 Constructor 对象

```java
Constructor[] constructors = clazz.getConstructors();
```

返回的Constructor数组包含每一个声明为公有的（Public）构造方法。

如果你知道你要访问的构造方法的方法参数类型，你可以用下面的方法获取指定的构造方法，这例子返回的构造方法的方法参数为String类型：

```java
Constructor constructor = clazz.getConstructor(new Class[]{String.class})
```

如果没有指定的构造方法能满足匹配的方法参数则会抛出：NoSuchMethodException。

### 获取构造方法的参数

```java
Constructor constructor = ... //获取Constructor对象
Class[] parameterTypes = constructor.getParameterTypes();
```

### 利用Constructor对象实例化一个类

```java
Constructor constructor = MyObject.class.getConstructor(String.class);
MyObject myObject = (MyObject) constructor.newInstance("constructor-arg1");
```

constructor.newInstance()方法的方法参数是一个可变参数列表，但是当你调用构造方法的时候你必须提供精确的参数，即形参与实参必须一一对应。在这个例子中构造方法需要一个String类型的参数，那我们在调用newInstance方法的时候就必须传入一个String类型的参数。