---
layout: post
title: Java NIO Buffer
category: 编程语言
tags: Java
keywords: Java
description:  Java NIO Buffer
---
### 基本概念

![](http://cdn.taotaoshenqi.com/letcheng/buffer_1.png)

* 用户空间是常规进程所在区域，JVM是常规进程，驻守于用户空间。
* 内核空间是指操作系统所在权利，能与设备控制器进行通信。
* 输入数据时会进行内核区域，再拷贝到JVM；输出时也会先由JVM拷贝到内核区域，再输出到终端。

缓冲区是一块不大的内存区域，可以反复使用，利用它可以在IO过程一次交互一批信息，而不是1个字节。

![](http://ifeve.com/wp-content/uploads/2013/06/buffers-modes.png)

缓冲区有以下几个属性：

* capacity 容量

> 作为一个内存块，Buffer有一个固定的大小值，也叫“capacity”.你只能往里写capacity个byte、long，char等类型。一旦Buffer满了，需要将其清空（通过读数据或者清除数据）才能继续写数据往里写数据。

* 当前位置 position

> 当你写数据到Buffer中时，position表示当前的位置。初始的position值为0.当一个byte、long等数据写到Buffer后， position会向前移动到下一个可插入数据的Buffer单元。position最大可为capacity – 1.
> 当读取数据时，也是从某个特定位置读。当将Buffer从写模式切换到读模式，position会被重置为0. 当从Buffer的position处读取数据时，position向前移动到下一个可读的位置。

* limit

> 在写模式下，Buffer的limit表示你最多能往Buffer里写多少数据。 写模式下，limit等于Buffer的capacity。
> 当切换Buffer到读模式时， limit表示你最多能读到多少数据。因此，当切换Buffer到读模式时，limit会被设置成写模式下的position值。换句话说，你能读到之前写入的所有数据（limit被设置成已写数据的数量，这个值在写模式下就是position）

