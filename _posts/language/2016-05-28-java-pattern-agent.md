---
layout: post
title: Java 静态代理和动态代理
category: 编程语言
tags: Java
keywords: Java
description: Java 静态代理和动态代理
---

### 静态代理

代理的设计模式提供了一种无侵入式的代码扩展方式。

```java
public interface Subject   
{   
  public void doSomething();   
}
```

```java
public class RealSubject implements Subject   
{   
  public void doSomething()   
  {   
    System.out.println( "call doSomething()" );   
  }   
}  
```

```java
public class SubjectProxy implements Subject
{
  Subject subimpl = new RealSubject();
  public void doSomething()
  {
     subimpl.doSomething();
  }
}
```

```java
public static void main(String args[])
{
   Subject sub = new SubjectProxy();
   sub.doSomething();
}
```

静态代理这个模式本身有个大问题，如果类方法数量越来越多的时候，代理类的代码量是十分庞大的。所以引入动态代理来解决此类问题。

### 动态代理

```java
作者：雨夜偷牛的人
链接：https://www.zhihu.com/question/20794107/answer/23330381
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

public class ProxyHandler implements InvocationHandler
{
    private Object tar;
    //绑定委托对象，并返回代理类
    public Object bind(Object tar)
    {
        this.tar = tar;
        //绑定该类实现的所有接口，取得代理类 
        return Proxy.newProxyInstance(tar.getClass().getClassLoader(),
                                      tar.getClass().getInterfaces(),
                                      this);
    }    
    public Object invoke(Object proxy , Method method , Object[] args)throws Throwable
    {
        Object result = null;
        //这里就可以进行所谓的AOP编程了
        //在调用具体函数方法前，执行功能处理
        result = method.invoke(tar,args);
        //在调用具体函数方法后，执行功能处理
        return result;
    }
}
```

```java
public static void main(String args[])
{
    ProxyHandler proxy = new ProxyHandler();
    //绑定该类实现的所有接口
    Subject sub = (Subject) proxy.bind(new RealSubject());
    sub.doSomething();
}
```