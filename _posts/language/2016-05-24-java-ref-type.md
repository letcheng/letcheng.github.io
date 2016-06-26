---
layout: post
title: Java 引用关系
category: 编程语言
tags: Java
keywords: Java
description: Java 引用关系
---

### 引用队列（java.lang.ref.ReferenceQueue）　

引用队列配合Reference的子类等使用，当引用对象所指向的内存空间被GC回收后，该引用对象则被追加到引用队列的末尾

```java
// 只供Reference实例调用，且仅能调用一次
boolean enqueue(Reference<? extends T> r)
// 从队列中出队一个元素，若队列为空则返回null。
Reference<? extends T> ReferenceQueue#poll()
// 从队列中出队一个元素，若没有则阻塞直到有元素可出队。
Reference<? extends T> ReferenceQueue#remove()
// 从队列中出队一个元素，若没有则阻塞直到有元素可出队或超过timeout指定的毫秒数（由于采用wait(long timeout)方式实现等待，因此时间不能保证）。
Reference<? extends T> ReferenceQueue#remove(long timeout) 
```

### 四种引用关系

- 强引用(Strong Reference)

使用最多的引用，比如 A a = new A()，如果对象具有强引用，那么垃圾回收器是不会回收这个对象的。当内存不足时，jvm会抛出OutOfMemoryError错误。

- 软引用(Soft Reference)

强度比Strong Reference弱，使用SoftReference来表示，当** jvm 堆内存不足**（即将抛出OOM）的时候，垃圾回收器会回收被SoftReference指向的对象，回收完成后，如果内存不足，则会抛出OOM。

SoftReference通常是和ReferenceQueue关联使用，如果SoftReference所指向的对象被GC回收了，jvm会把这个引用加入到ReferenceQueue里

```java 
ReferenceQueue q = new ReferenceQueue();

// 获取数据并缓存
Object obj = new Object();
SoftReference sr = new SoftReference(obj, q);

// 下次使用时
Object obj = (Object)sr.get();
if (obj == null){
  // 当软引用被回收后才重新获取
  obj = new Object();
}

// 清理被收回后剩下来的软引用对象
SoftReference ref = null;
while((ref = q.poll()) != null){
  // 清理工作
}
```

- 弱引用(Weak Reference)：

强度比Soft Reference弱，使用WeakReference来表示，GC的时候，如果一个对象所有引用是弱引用的话，那么垃圾回收器不管内存是否不足，都会回收该对象，如果WeakReference和ReferenceQueue联合使用的话，那么WeakReference会被加入到引用队列中。

- 虚引用(Phantom Reference)：

强度最弱，使用PhantomReference来表示，在任何时刻，虚引用的对象都可被回收，还有虚引用必须和ReferenceQueue联合使用。


 

