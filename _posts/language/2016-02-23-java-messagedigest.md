---
layout: post
title: Java MessageDigest类的使用
category: 编程语言
tags: Java
keywords: 散列，MessageDigest
description: Java MessageDigest类的使用
---


## MessageDigest简介
 * MessageDigest 类是一个引擎类，它是为了提供诸如 SHA1 或 MD5 等密码上安全的报文摘要功能而设计的。密码上安全的报文摘要可接受任意大小的输入（一个字节数组），并产生固定大小的输出，该输出称为一个摘要或散列。摘要具有以下属性
   + 无法通过计算找到两个散列成相同值的报文。
   + 摘要不反映任何与输入有关的内容
 * 使用报文摘要可以生成数据唯一且可靠的标识符。有时它们被称为数据的“数字指纹”。

## 创建MessageDigest实例

```java
 public static MessageDigest getInstance(String algorithm)
 public static MessageDigest getInstance(String algorithm, String provider)
```

## 更新报文摘要对象

```java
  public void update(byte input)
  public void update(byte[] input)
  public void update(byte[] input, int offset, int len) 
```

## 计算摘要

```java
  public byte[] digest()
  public byte[] digest(byte[] input)
  public int digest(byte[] buf, int offset, int len)
```

## Attention

  * 对于给定数量的更新数据，digest 方法只能被调用一次。digest 方法被调用后，MessageDigest  对象被重新设置成其初始状态。
  * 由于历史原因，此类是抽象的，是从 MessageDigestSpi 扩展的,只应该注意在此 MessageDigest 类中定义的方法；超类中的所有方法是供希望提供自己的信息摘要算法实现的加密服务提供者使用的。
  * MessageDigest并不是单实例的。
