---
layout: post
title: Java Semaphore
category: 编程语言
tags: Java
keywords: Java
description: Java Semaphore
---

### 概述

信号量（Semaphore）是用来控制同时访问特定资源的线程数量，通过协调各个资源，以保证合理地使用公共资源。

### 关键方法

```java
public Semaphore(int permits);
public Semaphore(int permits, boolean fair)
```

```java
public void acquire() throws InterruptedException
public void acquireUninterruptibly()
public boolean tryAcquire()
public boolean tryAcquire(long timeout, TimeUnit unit)
public void release()
...
```

### 使用场景

Semaphore可以用于做流量控制，适用于公用资源有限的应用场景，比如数据库连接。
假如有一个需求，要读取几万个文件的数据，因为都是IO密集型任务，我们可以启动几十个线程并发的读取，但是如果读到内存后，还需要存储到数据库中，而数据库的连接数只有10个，这时我们必须控制只有十个线程同时获取数据库连接保存数据，否则会报错无法获取数据库连接。这个时候，我们就可以使用Semaphore来做流控。

```java
private static ExecutorService executor = Executors.newFixedThreadPool(5);
private static Semaphore semaphore = new Semaphore(2);
public static void main(String args[]){
    for(int i=0;i<5;i++){
        executor.execute(new Runnable() {
            public void run() {
                try {
                    semaphore.acquire();
                    System.out.println("thread-"+Thread.currentThread().getId() +">> save to db ...");
                    TimeUnit.SECONDS.sleep(10);
                    semaphore.release();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
    }
    executor.shutdown();
}
```