---
layout: post
title: Java 类加载器深入理解
category: 编程语言
tags: Java
keywords: Java
description: Java 类加载器深入理解
---

### Java 的执行过程

1. 首先根据java后的运行模式配置项或<JAVA_HOME>/jre/lib/i386/jvm.cfg来决定是以client还是server模式运行JVM，然后加载<JAVA_HOME>/jre/bin/client或server/jvm.dll，并开始启动JVM；

2. 在启动JVM的同时将加载Bootstrap ClassLoader（启动类加载器，使用C/C++编写，属于JVM的一部分）；

3. 通过Bootstrap ClassLoader加载**sun.misc.Launcher**类（ExtClassLoader和AppClassLoader是它的内部类）；

4. sun.misc.Launcher类在执行初始化阶段时，会创建一个自己的实例，在创建过程中会创建一个ExtClassLoader（扩展类加载器）实例、一个AppClassLoader（系统类加载器）实例，并将AppClassLoader实例设置为主线程的ThreadContextClassLoader（线程上下文类加载器）。

5. 然后AppClassLoader实例就开始加载Main.class及其所依赖的类库了



### 四种类加载器

- BootStrap Loader：作为JVM的一部分无法在应用程序中直接引用，由C/C++实现。负责加载 ① \<JAVA_HOME\>/jre/lib目录 或 ② -Xbootclasspath参数所指定的目录 或 ③ 系统属性sun.boot.class.path指定的目录 中特定名称的jar包。

> 注意：Bootstrap ClassLoader只会加载特定名称的类库，如rt.jar等。假如我们自己定义一个jar类库丢进\<JAVA_HOME\>/jre/lib目录下也不会被加载的！

```java
    @Test
    public void testGetBootstrapClassPath(){
        URL[] urls = Launcher.getBootstrapClassPath().getURLs();
        for (URL url : urls)
            System.out.println(url.toExternalForm());
    }
    /*输出结果
     file:/C:/Java/jdk1.7.0_79/jre/lib/resources.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/rt.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/sunrsasign.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/jsse.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/jce.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/charsets.jar
     file:/C:/Java/jdk1.7.0_79/jre/lib/jfr.jar
     file:/C:/Java/jdk1.7.0_79/jre/classes*/
```

- ExtClassLoader：仅含一个实例，由 sun.misc.Launcher$ExtClassLoader 实现，负责加载 ① \<JAVA_HOME\>/jre/lib/ext目录 或 ② 系统属性java.ext.dirs所指定的目录中的所有类库。

- AppClassLoader：仅含一个实例，由 sun.misc.Launcher$AppClassLoader 实现，可通过 java.lang.ClassLoader.getSystemClassLoader 获取。负责加载 ① 系统环境变量 ClassPath 或 ② -cp 或 ③ 系统属性 java.class.path 所指定的目录下的类库。

- Context ClassLoader（线程上下文加载器）：默认为System ClassLoader，可通过Thread.currentThread().setContextClassLoader(ClassLoader)来设置，可通过ClassLoader Thread.currentThread().getContextClassLoader()来获取。每个线程均将Context ClassLoader预先设置为父线程的Context ClassLoader。该类加载器主要用于打破双亲委派模型，容许父类加载器通过子类加载器加载所需的类库。

### 双亲委派模型

 当一个类加载器收到类加载的请求，首先会将请求委派给父类加载器，这样一层一层委派到Bootstrap ClassLoader。然后加载器根据请求尝试搜索和加载类，若搜索失败则向子类加载器反馈信息（抛出ClassNotFoundException），然后子类加载器才尝试自己去加载。JAVA中采用组合的方式实现双亲委派模型，而不是继承的方式。

 ![类加载机制](http://images.cnitblog.com/blog/347002/201502/110912451518649.gif)

### 手动加载类

1. 利用现有的类加载器

```java
    // 通过当前类的类加载器加载（会执行初始化）
    Class.forName("二进制名称");
    Class.forName("二进制名称", true, this.getClass().getClassLoader());
    
    // 通过当前类的类加载器加载（不会执行初始化）
    Class.forName("二进制名称", false, this.getClass().getClassLoader());
    this.getClass().loadClass("二进制名称");
    
    // 通过系统类加载器加载（不会执行初始化）
    ClassLoader.getSystemClassLoader().loadClass("二进制名称");
    
    // 通过线程上下文类加载器加载（不会执行初始化）
    Thread.currentThread().getContextClassLoader().loadClass("二进制名称");
```

2. 利用URLClassLoader

```java
    URL[] baseUrls = {new URL("file:/d:/testLib/")};
    URLClassLoader loader = new URLClassLoader(baseUrl, ClassLoader.getContextClassLoader());
    Class clazz = loader.loadClass("com.fsjohnhuang.HelloWorld");
```

### 自定义类加载器

```java
    public class MyClassLoader extends ClassLoader{
      private String dir;
    
      public MyClassLoader(String dir, ClassLoader parent){
        super(parent);
        this.dir = dir;
      }
      
      @Override
      protect Class<?> findClass(String binaryName) throws ClassNotFoundException{
        String pathSegmentSeperator = System.getProperty("file.separator");
        String path = binaryName.replace(".", pathSegmentSeperator ).concat(".class");
    
        FileInputStream fis = new FileInputStream(dir + pathSegmentSeperator  + path);
        byte[] b = new byte[fis.available()];
        fis.read(b, 0, b.length);
        fis.close();
        return defineClass(binaryName, b, 0, b.length);
      }
    }
```

### 加载图片、视频等非类资源

 ClassLoader除了用于加载类外，还可以用于加载图片、视频等非类资源。同样是采用双亲委派模型将加载资源的请求传递到顶层的Bootstrap ClassLoader，在其管辖的目录下搜索资源，若失败才逐层返回逐层搜索。

```java
    URL getResource(String name)
    InputStream getResourceAsStream(String name)
    Enumeration<URL> getResources(String name)
```

