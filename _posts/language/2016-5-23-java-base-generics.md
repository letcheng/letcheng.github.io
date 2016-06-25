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

### 通配符
   
```java
    <? extends Fruit>
    <? super Apple>
    <?>
```

### 泛型擦擦

泛型是JDK1.5才出现的，所以为了兼容，采用了擦除的方式实现。泛型类型只有在**静态类型检查**期间才出现，在此之后，程序中所有泛型类型都被擦除，替换为他们的非泛型上界。

- 导致的问题

```java
    //以下几种情况都编译不通过
    if (arg instanceof T) {
    }
    T var = new T();
    T[] array = new T[SIZE];
    T[] array = (T) new Object[SIZE];
```

- 奇淫技巧
 
    * 解决不能使用 new T()的问题，可以考虑工厂类解决

```java
  interface IFactory<T> {
      T create();
  }
  
  class Foo2<T> {
      private T x;
  
      public <F extends IFactory<T>> Foo2(F factory) {
          x = factory.create(); //避免使用new T();
      }
  }
  // 使用
  new Foo2<Integer>(new IFactory<Integer>(){
        Integer create(){
            return new Integer(0);
        }
  })
```

### 其他注意的问题

- 任何基本类型都不能作为类型参数
- 重载
```java
    //Compile Error. 编译不能通过
    public class UseList<W,T>{
        void f(List<T> v){}
        void f(List<W> v){}
    }
```