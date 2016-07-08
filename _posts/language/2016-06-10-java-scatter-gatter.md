---
layout: post
title: Java NIO Scatter/Gatter
category: 编程语言
tags: Java
keywords: Java
description:  Java NIO Scatter/Gatter
---
### 基本概念

![](http://cdn.taotaoshenqi.com/letcheng/buffer_2.png)

* 分散（scatter）从Channel中读取是指在读操作时将**读取**的数据写入多个buffer中。因此，从Channel中读取的数据“分散（scatter）”到多个Buffer中。
* 聚集（gather）写入Channel是指在写操作时将多个buffer的数据*写入*同一个Channel，因此，Channel 将多个Buffer中的数据“聚集（gather）”后发送到Channel。

> scatter / gather经常用于需要将传输的数据分开处理的场合，例如传输一个由消息头和消息体组成的消息，你可能会将消息体和消息头分散到不同的buffer中，这样你可以方便的处理消息头和消息体。

### Scattering Reads

Scattering Reads是指数据从一个channel读取到多个buffer中。

```java
ByteBuffer header = ByteBuffer.allocate(128);
ByteBuffer body = ByteBuffer.allocate(1024);
ByteBuffer[] bufferArray = { header, body };
channel.read(bufferArray);
```

注意buffer首先被插入到数组，然后再将数组作为channel.read() 的输入参数。read()方法按照buffer在数组中的顺序将从channel中读取的数据写入到buffer，当一个buffer被写满后，channel紧接着向另一个buffer中写。
Scattering Reads在移动下一个buffer前，必须填满当前的buffer，这也意味着它不适用于动态消息。
### Gathering Writes

Gathering Writes是指数据从多个buffer写入到同一个channel。

```java
ByteBuffer header = ByteBuffer.allocate(128);
ByteBuffer body   = ByteBuffer.allocate(1024);
ByteBuffer[] bufferArray = { header, body };
channel.read(bufferArray);
```

buffers数组是write()方法的入参，write()方法会按照buffer在数组中的顺序，将数据写入到channel，注意只有position和limit之间的数据才会被写入。因此，如果一个buffer的容量为128byte，但是仅仅包含58byte的数据，那么这58byte的数据将被写入到channel中。因此与Scattering Reads相反，Gathering Writes能较好的处理动态消息。
