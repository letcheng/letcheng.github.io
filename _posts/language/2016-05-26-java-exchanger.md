---
layout: post
title: Java Exchanger
category: 编程语言
tags: Java
keywords: Java
description: Java Exchanger
---
### Exchanger

Exchanger提供了 一个同步点 ， 在这个同步点，两个线程可以交换数据。
当线程A调用Exchange对象的exchange()方法后，他会陷入阻塞状态，直到线程B也调用了exchange()方法，然后以线程安全的方式交换数据，之后线程A和B继续运行。


### 案例：生产者和消费者问题

```java
public class Producer implements Runnable {
    private List<String> buffer;
    private final Exchanger<List<String>> exchanger;
    public Producer(List<String> buffer,Exchanger<List<String>> exchanger) {
        this.buffer = buffer;
        this.exchanger = exchanger;
    }
    public void run() {
        for (int i=0;i<10;i++){
            System.out.printf("Producer thread:  %d\n",i);
            for(int j=0;j<10;j++){
                String message = "message " + (i*10+j);
                System.out.printf("Producer message: %s\n",message);
                buffer.add(message);
            }
            try {
                buffer = exchanger.exchange(buffer);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```java
public class Comsumer implements Runnable {
    private List<String> buffer;
    private final Exchanger<List<String>> exchanger;
    public Comsumer(List<String> buffer,Exchanger<List<String>> exchanger){
        this.buffer = buffer;
        this.exchanger = exchanger;
    }
    public void run() {
        for(int i=0;i<10;i++){
            System.out.printf("Consumer thread:  %d\n",i);
            try {
                buffer = exchanger.exchange(buffer);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            for(int j=0;j<10;j++){
                String message = buffer.get(0);
                System.out.println("Comsumer message: "+message);
                buffer.remove(0);
            }
        }
    }
}
```

```java
public static void main(String args[]){
    List<String> buffer1 = new ArrayList<String>();
    List<String> buffer2 = new ArrayList<String>();
    Exchanger<List<String>> exchanger = new Exchanger<List<String>>();
    new Thread(new Producer(buffer1,exchanger)).start();
    new Thread(new Comsumer(buffer2,exchanger)).start();
}
```