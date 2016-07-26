---
layout: post
title: Java 随机数和洗牌算法
category: 编程语言
tags: Java，算法
keywords: Java，洗牌
description: Java 随机数和洗牌算法
---

### 随机数

随机数在科学预测上有着非常重要的应用！还有密码学中，随机数也是基础之一。
Java的随机是伪随机，实际上就是一个数字（种子）经过运算后近似随机数的数字。所以实际上伪随机是完全可以通过运算预测的。Java中的随机数种子若不填写就会使用缺省值，即系统时间。

### java.lang.Math.Random 和 jang.util.Random

> java.lang.Math.Random 实际上是对 java.util.Random 的封装，其返回值[0.0,1.0]

```java
public static double random() { //java.lang.Math.Random 的源代码
   Random rnd = randomNumberGenerator;
   if (rnd == null) rnd = initRNG();
   return rnd.nextDouble();
}
```

### 随机数的生成 （携程面试题）

随机生成 100 个随机数(1,100)

从后面一直往前面确定随机数

```java
   int nums[] = {1,2,3...100};
   for(int i=nums.length-1;i>0;i--){
       int rand = new Random().nextInt(i); // Math.floor(Math.random()*i)
       int tmp = nums[i];
       nums[i] = nums[rand];
       nums[rand] = tmp;
   }
```

```java
    int nums[] = {1,2,3...100};
    for(int i=0;i<nums.length;i++){
        int rand = i + new Random().nextInt(nums.length - i);
        int tmp = nums[i];
        nums[i] = nums[rand];
        nums[rand] = tmp;
    }
```

