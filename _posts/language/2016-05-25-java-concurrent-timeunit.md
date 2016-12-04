---
layout: post
title: Java TimeUnit
category: 编程语言
tags: Java
keywords: Java
description:  Java TimeUnit
---
### 时间的转换

```java
long start_time = System.nanoTime();
try {
    Thread.sleep(10);
} catch (InterruptedException e) {
    e.printStackTrace();
}
long end_time = System.nanoTime();
System.out.println(TimeUnit.NANOSECONDS.toMillis(end_time-start_time)); // 转换为毫秒进行输出
```

### 线程生命周期管理
    
通常我们使用Thread.sleep()方法来当前线程进行休眠，当时由于采用毫秒为时间单位，可读性很不友好。

```java
new Thread(){
    @Override
    public void run() {
        super.run();
        try {
            // Thread.sleep(1000); //可读性不友好
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}.start();
```

