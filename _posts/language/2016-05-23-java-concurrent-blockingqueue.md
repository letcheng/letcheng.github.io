---
layout: post
title: Java BlockingQueue 和 ConcurrentLinkedQueue 分析
category: 编程语言
tags: Java
keywords: Java
description:  Java BlockingQueue 和 ConcurrentLinkedQueue 分析
---

### 为什么 BlockingQueue ?

当使用 BlockingQueue 的时候，我们再也不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程,是生产者和消费者模型的首选。

### BlockingQueue 常用的方法

```java
boolean add(E) 
boolean offer(E) 
boolean offer(E, long, TimeUnit) 
void put(E) // 阻塞式方法
E take() // 阻塞式方法
E poll(long, TimeUnit)

int remainingCapacity() 
boolean remove(Object) 
boolean contains(Object) 
//
int drainTo(Collection<? super E>)
int drainTo(Collection<? super E>, int)
```


### BlockingQueue 常用的实现类

![](http://cdn.taotaoshenqi.com/letcheng/BlockingQueue-UML.png)

 ArrayBlockingQueue和LinkedBlockingQueue的区别

1. 队列中锁的实现不同
    ArrayBlockingQueue实现的队列中的锁是没有分离的，即生产和消费用的是同一个锁；
    LinkedBlockingQueue实现的队列中的锁是分离的，即生产用的是putLock，消费是takeLock
2. 在生产或消费时操作不同
    ArrayBlockingQueue实现的队列中在生产和消费的时候，是直接将枚举对象插入或移除的；
    LinkedBlockingQueue实现的队列中在生产和消费的时候，需要把枚举对象转换为Node<E>进行插入或移除，会影响性能
3. 队列大小初始化方式不同
    ArrayBlockingQueue实现的队列中必须指定队列的大小；
    LinkedBlockingQueue实现的队列中可以不指定队列的大小，但是默认是Integer.MAX_VALUE
    
### 非阻塞式的并发队列

使用阻塞算法的队列可以用一个锁（入队和出队用同一把锁）或两个锁（入队和出队用不同的锁）等方式来实现，而非阻塞的实现方式则可以使用循环CAS的方式来实现，