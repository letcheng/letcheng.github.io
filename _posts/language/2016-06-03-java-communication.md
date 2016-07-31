---
layout: post
title: Java 消息通信模型
category: 编程语言
tags: Java
keywords: Java
description: Java 消息通信模型
---

### TCP/IP + BIO

```java
Socket socket = new Socket("localhost",1234);
PrintWriter printWriter = new PrintWriter(new OutputStreamWriter(socket.getOutputStream()));
printWriter.println("Hello");
printWriter.println("world");
printWriter.close();
```

```java
ServerSocket serverSocket = new ServerSocket(1234);
Socket socket = serverSocket.accept(); // 阻塞至客户端产生请求
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
String tmp = null;
while ((tmp = bufferedReader.readLine() )!= null){
    System.out.println(tmp);
}
bufferedReader.close();
```