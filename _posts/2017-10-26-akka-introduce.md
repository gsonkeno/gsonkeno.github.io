---
layout: post
title:  "akka入门简介"
categories: Akka
tags:  Akka
author: gaos
---

* content
{:toc}

此文是针对Akka入门学习的一个入门指示。




## uml学习资源
[plantUML官方网站指导手册](http://plantuml.com/download)，在该页面可以下载该平台下的中文版的plantUML指导手册。

该手册中有对时序图、用例图、类图、活动图、组件图、状态图、对象图的介绍。

## 类图入门示例
基于上面的指导手册和网络学习资源，写了个示例:
```
 @startuml  
 
 interface Executor{
 	~void execute(Runnable command)  //执行线程类
 }
 interface ExecutorService{
 	~<T> Future<T> submit(Callable<T> task)
 	~<T> Future<T> submit(Runnable task, T result)
 	~Future<?> submit(Runnable task)
 	~void shutdown()
 	~List<Runnable> shutdownNow()
 }
 interface ScheduledExecutorService
 
 class ThreadPoolExecutor{
	 + void shutdown()
 }
 
  class Executors{
	 + ExecutorService newFixedThreadPool(int nThreads)
	 + ExecutorService newCachedThreadPool()
	 + ExecutorService newSingleThreadExecutor()
 }
 
 Executor <|-- ExecutorService 
 ExecutorService <|-- ScheduledExecutorService
 ExecutorService <|.. AbstractExecutorService
 AbstractExecutorService <|-- ThreadPoolExecutor

 @enduml
 
 
```
效果图如下:

![uml效果图](http://images2015.cnblogs.com/blog/822071/201707/822071-20170702190444774-1277063148.png)


说明一下:

- 实线的三角箭头表示泛化，如继承,对应java中的extends。
- 虚线的三角箭头表示实现，对应java中的implement。