---
layout: post
title: Java 泛型的使用技巧
category: 编程语言
tags: Java
keywords: Java
description: Java 泛型的使用技巧
---

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