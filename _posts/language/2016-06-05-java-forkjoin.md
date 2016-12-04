---
layout: post
title: Java 并发-ForkJoin框架
category: 编程语言
tags: Java
keywords: Java
description: Java 并发-ForkJoin框架
---

## 概述

* Fork/Join 框架是 jdk1.7 提供了的一个用于并行执行任务的框架， 是一个把大任务分割成若干个小任务，最终汇总每个小任务结果后得到大任务结果的框架。

* 对于符合 Fork/Join 模式的应用，软件开发人员不再需要处理各种并行相关事务，例如同步、通信等，以难以调试而闻名的死锁和 data race 等错误也就不会出现，提升了思考问题的层次。

* 如果一个应用能被分解成多个子任务，并且组合多个子任务的结果就能够获得最终的答案，那么这个应用就适合用 Fork/Join 模式来解决。

![](http://dl.iteye.com/upload/attachment/234119/4f7cd1b0-9f58-306f-9590-87499929861d.jpg)

### 工作窃取算法 (work-stealing)

* 假如我们需要做一个比较大的任务，我们可以把这个任务分割为若干互不依赖的子任务，为了减少线程间的竞争，于是把这些子任务分别放到不同的队列里，并为每个队列创建一个单独的线程来执行队列里的任务，线程和队列一一对应，比如A线程负责处理A队列里的任务。

* 但是有的线程会先把自己队列里的任务干完，而其他线程对应的队列里还有任务等待处理。干完活的线程与其等着，不如去帮其他线程干活，于是它就去其他线程的队列里窃取一个任务来执行。

* 而在这时它们会访问同一个队列，所以为了减少窃取任务线程和被窃取任务线程之间的竞争，通常会使用双端队列，**被窃取任务线程永远从双端队列的头部拿任务执行，而窃取任务的线程永远从双端队列的尾部拿任务执行。**

### 主要的类结构

![](http://dl.iteye.com/upload/attachment/234124/8703a34a-9f05-37a5-bb08-c135bb5bffc8.jpg)

* RecursiveAction 用于任务没有返回结果的场景

* RecursiveTask 用于任务又返回结果的场景

## 典型用法

```
Result solve(Problem problem) {
    if (problem is small)
        directly solve problem
    else {
        split problem into independent parts
        fork new subtasks to solve each part
        join all subtasks
        compose result from subresults
    }
}
```

Fork操作将会启动一个新的并行fork/join子任务。Join操作会一直等待直到所有的子任务都结束。Fork/Join算法，如同其他分治算法一样，总是会递归的、反复的划分子任务，直到这些子任务可以用足够简单的、短小的顺序方法来执行。


## Fork/Join 框架实现原理

ForkJoinPool 由 **ForkJoinTask** 数组和 **ForkJoinWorkerThread** 数组组成，ForkJoinTask 数组负责存放程序提交给 ForkJoinPool 的任务，而 ForkJoinWorkerThread 数组负责执行这些任务。

### fork过程源码探讨

当我们调用 ForkJoinTask 的 fork 方法时，程序员会调用 ForkJoinWorkerThread 的 pushTask 方法异步执行这个任务，然后立即返回结果。

```java
    public final ForkJoinTask<V> fork() {
        ((ForkJoinWorkerThread) Thread.currentThread())
            .pushTask(this);
        return this;
    }
```

pushTask 方法把当前任务存放在 ForkJoinTask 数组queue里。然后再调用ForkJoinPool的signalWork()方法唤醒或创建一个工作线程来执行任务。

```java
final void pushTask(ForkJoinTask<?> t) {
    ForkJoinTask<?>[] q; int s, m;
    if ((q = queue) != null) {    // ignore if queue removed
        long u = (((s = queueTop) & (m = q.length - 1)) << ASHIFT) + ABASE;
        UNSAFE.putOrderedObject(q, u, t);
        queueTop = s + 1;         // or use putOrderedInt
        if ((s -= queueBase) <= 2)
            pool.signalWork();
        else if (s == m)
            growQueue();
    }
}
```

### join 过程源码探讨

Join方法的主要作用是阻塞当前线程并等待获取结果。

```java
public final V join() {
    if (doJoin() != NORMAL)
        return reportResult();
    else
        return getRawResult();
}
```

```java
private V reportResult() {
    int s; Throwable ex;
    if ((s = status) == CANCELLED)
        throw new CancellationException();
    if (s == EXCEPTIONAL && (ex = getThrowableException()) != null)
        UNSAFE.throwException(ex);
    return getRawResult();
}
```

首先，它调用了doJoin()方法，通过doJoin()方法得到当前任务的状态来判断返回什么结果，任务状态有四种：已完成（NORMAL），被取消（CANCELLED），信号（SIGNAL）和出现异常（EXCEPTIONAL）。

* 如果任务状态是已完成，则直接返回任务结果。
* 如果任务状态是被取消，则直接抛出CancellationException。
* 如果任务状态是抛出异常，则直接抛出对应的异常。

```java
private int doJoin() {
    Thread t; ForkJoinWorkerThread w; int s; boolean completed;
    if ((t = Thread.currentThread()) instanceof ForkJoinWorkerThread) {
        if ((s = status) < 0)
            return s;
        if ((w = (ForkJoinWorkerThread)t).unpushTask(this)) {
            try {
                completed = exec();
            } catch (Throwable rex) {
                return setExceptionalCompletion(rex);
            }
            if (completed)
                return setCompletion(NORMAL);
        }
        return w.joinTask(this);
    }
    else
        return externalAwaitDone();
}
```

在doJoin()方法里，首先通过查看任务的状态，看任务是否已经执行完了，如果执行完了，则直接返回任务状态，如果没有执行完，则从任务数组里取出任务并执行。如果任务顺利执行完成了，则设置任务状态为NORMAL，如果出现异常，则纪录异常，并将任务状态设置为EXCEPTIONAL。