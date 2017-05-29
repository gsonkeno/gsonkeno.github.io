---
layout: post
title:  "scala符号总结"
categories: Scala
tags:  scala
author: gaos
---

* content
{:toc}

scala学习中会发现符号众多，这篇文章专门对scala中的一些符号做些总结，当然可能某些符号并没有涉及到。




## _*
`_*`可以匹配长度不确定的列表
```
val list = List(1,2,3)
list match{
    case List(a,b)=>a*b
    case List(a,b,_*)=>a-b
}
```
显然，与`list`匹配的是`List(a,b,_*)`，`_*`在这里表示模式匹配时长度不确定的列表，显然符合。

## _
1. _指代集合中的每一个元素

遍历集合中的每个元素进行筛选
```
val list = List(1,2,3)
val filterList = list.filter(_>2); //筛选出大于2的元素
println(filterList) // List(3)
```
