---
layout: post
title: Java CountDownLatch
category: 编程语言
tags: Java
keywords: Java
description: Java CountDownLatch
---

### 概述

CountDownLatch是一个同步工具类，它允许一个或多个线程一直等待，直到其他线程的操作执行完后再执行。

CountDownLatch是通过一个计数器来实现的，计数器的初始值为线程的数量。每当一个线程完成了自己的任务后，计数器的值就会减1。当计数器值到达0时，它表示所有的线程已经完成了任务，然后在闭锁上等待的线程就可以恢复执行任务。

![](http://incdn1.b0.upaiyun.com/2015/04/f65cc83b7b4664916fad5d1398a36005.png)

### 流程和使用方法

![](http://cdn.taotaoshenqi.com/letcheng/countdownlatch.png)

```java
public void CountDownLatch(int count)
```

构造函数中的计数值（count）实际上就是闭锁需要等待的线程数量。这个值只能被设置一次，而且CountDownLatch没有提供任何机制去重新设置这个计数值。

与CountDownLatch的第一次交互是主线程等待其他线程。主线程必须在启动其他线程后立即调用CountDownLatch.await()方法。这样主线程的操作就会在这个方法上阻塞，直到其他线程完成各自的任务。

其他N 个线程必须引用闭锁对象，因为他们需要通知CountDownLatch对象，他们已经完成了各自的任务。这种通知机制是通过 CountDownLatch.countDown()方法来完成的；每调用一次这个方法，在构造函数中初始化的count值就减1。所以当N个线程都调 用了这个方法，count的值等于0，然后主线程将能唤醒所有调用await()方法而进入休眠的线程。

### 案例

应用程序的主线程希望在负责启动框架服务的线程已经启动所有的框架服务之后再执行。

```java
public abstract class BaseHealthChecker implements Runnable {
    private CountDownLatch countDownLatch;
    private String serviceName;
    private boolean status;
    public BaseHealthChecker(String serviceName,CountDownLatch countDownLatch){
        super();
        this.countDownLatch = countDownLatch;
        this.serviceName = serviceName;
        this.status = false;
    }

    public void run() {
        try {
            verifyService();
            status = true;
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if(countDownLatch!=null){
                countDownLatch.countDown();
            }
        }
    }
    protected abstract void verifyService();

    public boolean isStatus() {
        return status;
    }
    public void setStatus(boolean status) {
        this.status = status;
    }
    public String getServiceName() {
        return serviceName;
    }

    public void setServiceName(String serviceName) {
        this.serviceName = serviceName;
    }
}
```

```java
public class NetWorkHealthChecker extends BaseHealthChecker {

    public NetWorkHealthChecker(CountDownLatch countDownLatch) {
        super("NetWork Service", countDownLatch);
    }

    protected void verifyService() {
        System.out.println("Checking " + this.getServiceName());
        try
        {
            TimeUnit.SECONDS.sleep(5);
        }
        catch (InterruptedException e)
        {
            e.printStackTrace();
        }
        System.out.println(this.getServiceName() + " is UP");
    }
}
```

```java
public class ApplicationStartupUtils {
    private static List<BaseHealthChecker> services;
    private static CountDownLatch countDownLatch;
    public static boolean checkExternalServices() {
        countDownLatch = new CountDownLatch(1);
        services = new ArrayList<BaseHealthChecker>();
        services.add(new NetWorkHealthChecker(countDownLatch));
        Executor executor = Executors.newFixedThreadPool(services.size());
        for (BaseHealthChecker checker : services) {
            executor.execute(checker);
        }
        try {
            countDownLatch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        for (final BaseHealthChecker checker : services) {
            if (!checker.isStatus()) {
                return false;
            }
        }
        return true;
    }
    public static void main(String args[]){
        boolean result = ApplicationStartupUtils.checkExternalServices();
        System.out.println("result is >>>" +result);
    }
}
```