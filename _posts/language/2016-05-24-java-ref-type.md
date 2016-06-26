---
layout: post
title: Java 引用关系
category: 编程语言
tags: Java
keywords: Java
description: Java 引用关系
---

### 引用队列（java.lang.ref.ReferenceQueue）　

引用队列配合Reference的子类等使用，当引用对象所指向的内存空间被GC回收后，该引用对象则被追加到引用队列的末尾。

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

> 软引用非常适合于创建缓存。当系统内存不足的时候，缓存中的内容是可以被释放的。比如考虑一个图像编辑器的程序。该程序会把图像文件的全部内容都读取到内存中，以方便进行处理。而用户也可以同时打开多个文件。当同时打开的文件过多的时候，就可能造成内存不足。如果使用软引用来指向图像文件内容的话，垃圾回收器就可以在必要的时候回收掉这些内存。

```java 
public class ImageData {
    private String path;
    private SoftReference<byte[]> dataRef;
    public ImageData(String path) {
        this.path = path;
        dataRef = new SoftReference<byte[]>(new byte[0]);
    }
    private byte[] readImage() {
        return new byte[1024 * 1024]; //省略了读取文件的操作
  }
    public byte[] getData() {
        byte[] dataArray = dataRef.get();
        if (dataArray == null || dataArray.length == 0) {
            dataArray = readImage();
            dataRef = new SoftReference<byte[]>(dataArray);
        }
        return dataArray;
    }
}
```

- 弱引用(Weak Reference)：

强度比Soft Reference弱，使用WeakReference来表示，只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程，因此不一定会很快发现那些只具有弱引用的对象。
弱引用的作用在于解决强引用所带来的对象之间在存活时间上的耦合关系。弱引用最常见的用处是在集合类中，尤其在哈希表中。哈希表的接口允许使用任何Java对象作为键来使用。当一个键值对被放入到哈希表中之后，哈希表对象本身就有了对这些键和值对象的引用。如果这种引用是强引用的话，那么只要哈希表对象本身还存活，其中所包含的键和值对象是不会被回收的。如果某个存活时间很长的哈希表中包含的键值对很多，最终就有可能消耗掉JVM中全部的内存。

- 虚引用(Phantom Reference)：

强度最弱，使用PhantomReference来表示，在任何时刻，虚引用的对象都可被回收，还有虚引用**必须**和ReferenceQueue联合使用。

> Java提供的对象终止化机制（finalization）。在Object类里面有个finalize方法，其设计的初衷是在一个对象被真正回收之前，可以用来执行一些清理的工作。因为Java并没有提供类似C++的析构函数一样的机制，就通过 finalize方法来实现。但是问题在于垃圾回收器的运行时间是不固定的，所以这些清理工作的实际运行时间也是不能预知的。虚引用（phantom reference）可以解决这个问题。在创建虚引用PhantomReference的时候必须要指定一个引用队列。当一个对象的finalize方法已经被调用了之后，这个对象的虚引用会被加入到队列中。通过检查该队列里面的内容就知道一个对象是不是已经准备要被回收了。
  虚引用及其队列的使用情况并不多见，主要用来实现比较精细的内存使用控制，这对于移动设备来说是很有意义的。程序可以在确定一个对象要被回收之后，再申请内存创建新的对象。通过这种方式可以使得程序所消耗的内存维持在一个相对较低的数量。比如下面的代码给出了一个缓冲区的实现示例。
 
```java
//创建一个缓存区
public class PhantomBuffer {
    private byte[] data = new byte[0];
    private ReferenceQueue<byte[]> queue = new ReferenceQueue<byte[]>();
    private PhantomReference<byte[]> ref = new PhantomReference<byte[]>(data, queue);
    public byte[] get(int size) {
        if (size <= 0) {
            throw new IllegalArgumentException("Wrong buffer size");
        }
        if (data.length < size) {
            data = null;
            System.gc(); //强制运行垃圾回收器
             try {
                queue.remove(); //该方法会阻塞直到队列非空
                ref.clear(); //虚引用不会自动清空，要手动运行
                ref = null;
                data = new byte[size];
                ref = new PhantomReference<byte[]>(data, queue);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
       }
       return data;
    }
}
```


 

