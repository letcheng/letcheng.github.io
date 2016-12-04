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

### UDP/IP + BIO

```java
DatagramSocket clientSocket = new DatagramSocket();
    String datas = "Hello World";
    DatagramPacket sendPacket = new DatagramPacket(datas.getBytes(),datas.getBytes().length, InetAddress.getLocalHost(),1234);
    clientSocket.send(sendPacket);
```

```java
DatagramSocket serverSocket = new DatagramSocket(1234);
byte[] buffer = new byte[65507];
DatagramPacket recevicePacket = new DatagramPacket(buffer,buffer.length);
serverSocket.receive(recevicePacket);
System.out.println(new String(buffer));
```