---
layout: post
title: Java CyclicBarrier
category: 编程语言
tags: Java
keywords: Java
description: Java CyclicBarrier
---

### 概述

CyclicBarrier 的字面意思是可循环使用（Cyclic）的屏障（Barrier）。它要做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。CyclicBarrier默认的构造方法是CyclicBarrier(int parties)，其参数表示屏障拦截的线程数量，每个线程调用await方法告诉CyclicBarrier我已经到达了屏障，然后当前线程被阻塞。

### 使用方法

```java
static CyclicBarrier cyclicBarrier = new CyclicBarrier(2);
public static void main(String args[]){
    new Thread(new Runnable() {
        public void run() {
            try {
                cyclicBarrier.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
            System.out.println(1);
        }
    }).start();
    try {
        cyclicBarrier.await();
    } catch (InterruptedException e) {
        e.printStackTrace();
    } catch (BrokenBarrierException e) {
        e.printStackTrace();
    }
    System.out.println(2);
}
```

### 应用场景

CyclicBarrier可以用于多线程计算数据，最后合并计算结果的应用场景。比如我们用一个Excel保存了用户所有银行流水，每个Sheet保存一个帐户近一年的每笔银行流水，现在需要统计用户的日均银行流水，先用多线程处理每个sheet里的银行流水，都执行完之后，得到每个sheet的日均银行流水，最后，再用barrierAction用这些线程的计算结果，计算出整个Excel的日均银行流水。


###  CyclicBarrier 与 CountDownLatch 的比较

CyclicBarrier 对象可以使用 reset() 方法进行重置，当重置发生时，在 await() 方法中等待的线程将受到一个 BrokenBarrierException 异常。

另外当很多线程在await()方法上等待的时候，如果一个线程被中断，这个线程将抛出 InterruptedException 异常，其他的等待线程将抛出 BrokenBarrierException 异常，于是 CyclicBarrier 对象就处于损坏状态了。可以通过 isBroken() 判断。