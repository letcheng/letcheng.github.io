---
layout: post
title: Java ThreadLocal
category: 编程语言
tags: Java
keywords: Java
description: Java ThreadLocal
---

### 原理

由于 Java 的堆内存是线程共享的，所以在多线程中的变量是共享变量。为了防止变量在临界区的读写造成不一致的情况，所以同步是并发一个重要的主题。
但是如果多个线程之间的变量并不需要彼此交互变量，可以使用 ThreadLocal 实现，使其变成线程安全。
![](http://cdn.taotaoshenqi.com/letcheng/threadlocal.png)

- 核心方法

```java
public void set(T value) {
     Thread t = Thread.currentThread();
     ThreadLocalMap map = getMap(t);
     if (map != null)
         map.set(this, value);
     else
         createMap(t, value);
 }
```

```java
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null)
            return (T)e.value;
    }
    return setInitialValue();
}
```

```java
private T setInitialValue() {
    T value = initialValue();
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
    return value;
}
```
### 应用

获得JDBC请求，每个请求应该是线程独立的，多个线程之间不需要进行Connection共享变量

```java
public class ConnectionManager {  
  
    private static ThreadLocal<Connection> connectionHolder = new ThreadLocal<Connection>() {  
        @Override  
        protected Connection initialValue() {  
            Connection conn = null;  
            try {  
                conn = DriverManager.getConnection(  
                        "jdbc:mysql://localhost:3306/test", "username",  
                        "password");  
            } catch (SQLException e) {  
                e.printStackTrace();  
            }  
            return conn;  
        }  
    };  
  
    public static Connection getConnection() {  
        return connectionHolder.get();  
    }  
  
    public static void setConnection(Connection conn) {  
        connectionHolder.set(conn);  
    }
      
    public static void closeConnect(){
        connectionHolder.get().close(); // 关闭 conn
        connectionHolder.remove(); // 移除 ThreadLocal
    }
}  
```


