---
layout: post
title: Java NIO Channel
category: 编程语言
tags: Java
keywords: Java
description:  Java NIO Channel
---
### 基本概念

Java NIO的通道类似流，但又有些不同：

* 既可以从通道中读取数据，又可以写数据到通道。但流的读写通常是单向的。
* 通道可以异步地读写。
* 通道中的数据总是要先读到一个Buffer，或者总是要从一个Buffer中写入。

### 通道的实现

* FileChannel 从文件中读写数据。

* DatagramChannel 能通过UDP读写网络中的数据。

* SocketChannel 能通过TCP读写网络中的数据。

* ServerSocketChannel可以监听新进来的TCP连接，像Web服务器那样。对每一个新进来的连接都会创建一个SocketChannel。

### 通道之间的数据传输

在Java NIO中，如果两个通道中有一个是FileChannel，那你可以直接将数据从一个channel传输到另外一个channel。

FileChannel的transferFrom()方法可以将数据从源通道传输到FileChannel中。
