---
layout: post
title: Java 定时任务
category: 编程语言
tags: Java
keywords: Java
description:  Java 定时任务
---
### Timer 和 TimerTask

Timer类可以调度任务，TimerTask则是通过在run()方法里实现具体任务。 Timer实例可以调度多任务，它是线程安全的。 

```java
public static void main(String args[]){
    new Timer(false).scheduleAtFixedRate(new TimerTask() {
        @Override
        public void run() {
            System.out.println("do something...");
        }
    },0,500);
}
```

### ScheduledExecutorService

相比于Timer的单线程，ScheduledExecutorService通过线程池的方式来执行任务,是目前原生Java 实现任务定时的最佳方式

```java
public static void main(String args[]){
   Runnable runnable = new Runnable() {
       public void run() {
           System.out.print("do something...");
       }
   };
    ScheduledExecutorService scheduledExecutorService = Executors.newSingleThreadScheduledExecutor();
    scheduledExecutorService.scheduleAtFixedRate(runnable,0,1,TimeUnit.SECONDS);
}
```

### squartz

官网：[http://www.quartz-scheduler.org/](http://www.quartz-scheduler.org/)

1. Maven库

```
  <dependency>
      <groupId>org.quartz-scheduler</groupId>
      <artifactId>quartz</artifactId>
      <version>2.2.1</version>
  </dependency>
```

2. 初始化和启动 Scheduler

```java
  Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
  scheduler.start();
```

3. 实现任务逻辑

```java
public class MyJob implements org.quartz.Job {
  public MyJob() {
  }
  public void execute(JobExecutionContext context) throws JobExecutionException {
      System.err.println("Hello World!  MyJob is executing.");
  }
}
```

4、 设置 JobDetail 和 Trigger

```java
// define the job and tie it to our MyJob class
JobDetail job = newJob(MyJob.class)
  .withIdentity("job1", "group1")
  .build();
// Trigger the job to run now, and then repeat every 40 seconds
Trigger trigger = newTrigger()
  .withIdentity("trigger1", "group1")
  .startNow()
  .withSchedule(simpleSchedule()
          .withIntervalInSeconds(40)
          .repeatForever())
  .build();
// Tell quartz to schedule the job using our trigger
scheduler.scheduleJob(job, trigger);
```
