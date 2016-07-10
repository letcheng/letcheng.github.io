---
layout: post
title: Java 泛型的一些思考与总结
category: 编程语言
tags: Java
keywords: Java
description: Java 泛型的一些思考与总结
---

### 泛型的概述

泛型是 JDK 1.5 的一项新特性，它的本质是是一种语法糖，从 Java 代码到 class 字节码的一种特殊处理过程（类型擦除），也就是说，泛型只是在源码中存在，在编译后的字节码文件中，就已经被替换为原生的类型了，并且在相应的地方插入了强制转型代码。

可以做一个实验：

```java
public static void main(String args[]){
    Map<String,String> map = new HashMap<String,String>();
    map.put("hello","你好");
    System.out.println(map.get("hello"));
}
```

利用 javac 编译成字节码文件，然后再利用字节码反编译工具进行反编译，将会发现泛型已经不见了。

```java
public static void main(String args[]){
    Map map = new HashMap();
    map.put("hello","你好");
    System.out.println((String)map.get("hello"));
}
```

鉴于此，可以解释为什么泛型的重载会有问题：

```java
public static void fun(List<String> list){
}
public static void fun(List<Integer> list){
}
```

同时，以下几种形式的代码都是不合法的。

```java
    //以下几种情况都编译不通过
    if (arg instanceof T) {
    }
    T var = new T();
    T[] array = new T[SIZE];
    T[] array = (T) new Object[SIZE];
```

另外，**基本的数据类型**也不能作为泛型参数。

### 泛型的基本使用

- 泛型类

```java
    public class Container<T> {
        private T t;
        public T get() {
            return t;
        }
        public void set(T t) {
            this.t = t;
        }
    }
```
- 泛型接口

```java
    public interface Generator<T> {
        public T next();
    }
    public class Container<T> implements Generator<String>{ // 类指定泛型接口具体类型
        public String next() {
            return null;
        }
    }
    public class Container<T> implements Generator<T>{ // 类继承泛型接口的泛型
        public T next() {
            return null;
        }
    }
```

- 泛型方法

```java
    public <P> void func(P p){     
    }
```

### 通配符
   
```java
    <? extends Fruit>
    <? super Apple>
    <?>
```