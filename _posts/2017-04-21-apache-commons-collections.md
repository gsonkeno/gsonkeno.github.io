---
layout: post
title:  "apache commons collections"
categories: Java
tags:  java apache
author: gaos
---

* content
{:toc}

此包是apache-commons工具集合包的一员，主要处理集合操作，是对jdk中集合类处理的一种有效补充，根据实际使用，可以简化代码量。




## 1. bag
bag用来获取集合中某一元素出现次数比较方便；
bag用来获取一个集合中所有不重复的元素比较方便

## 2. BidiMap
BidiMap 是一种能够 键和值双向查找的Map

## 3. MultiKeyMap
多键对应一个值的Map
```java
MultiKeyMap multiKeyMap = new MultiKeyMap();
multiKeyMap.put("中国", "广东", "一亿人口");
Object value = multiKeyMap.get("中国", "广东");
System.out.println(value);
```
## 4. MultiValueMap
一个键对应多个值的Map
```
MultiValueMap multiValueMap = new MultiValueMap();
multiValueMap.put("A班", "李红");
multiValueMap.put("A班", "张全");

Collection students = multiValueMap.getCollection("A班");//[李红, 张全]
List list =(List)multiValueMap.getCollection("B班");//null
```

## 5. ListUtils
### 5.1 求两个集合的交集
```java
public static List intersection(final List list1, final List list2) {
    final ArrayList result = new ArrayList();
    final Iterator iterator = list2.iterator();

    while (iterator.hasNext()) {
        final Object o = iterator.next();

        if (list1.contains(o)) {
            result.add(o);
        }
    }

    return result;
}
```
还有另外一种方式`retainAll`,在我看来这两者并没有太大的区别
```java
public static List retainAll(Collection collection, Collection retain) {
    List list = new ArrayList(Math.min(collection.size(), retain.size()));

    for (Iterator iter = collection.iterator(); iter.hasNext();) {
        Object obj = iter.next();
        if (retain.contains(obj)) {
            list.add(obj);
        }
    }
    return list;
}
```
### 5.2 求两个集合的差集
```java
public static List subtract(final List list1, final List list2) {
    final ArrayList result = new ArrayList(list1);
    final Iterator iterator = list2.iterator();

    while (iterator.hasNext()) {
        result.remove(iterator.next());
    }

    return result;
}
```
### 5.3 求两个集合的并集
```java
public static List union(final List list1, final List list2) {
    final ArrayList result = new ArrayList(list1);
    result.addAll(list2);
    return result;
}
```
### 5.4 求两个集合->非重元素集合的差集
```java
public static List removeAll(Collection collection, Collection remove) {
    List list = new ArrayList();
    for (Iterator iter = collection.iterator(); iter.hasNext();) {
        Object obj = iter.next();
        if (remove.contains(obj) == false) {
            list.add(obj);
        }
    }
    return list;
}
```
总结:所有这些方法，都不会改变输入的集合，返回的是一个新的集合

## 6 CollectionUtils
### 6.1 CollectionUtils.find(c,predicate)
返回集合类c中第一个满足断言的元素
```java
//返回集合中第一个满足指定断言的元素
Predicate predicate = new Predicate() {
	
	public boolean evaluate(Object object) {
		if (object.toString().length() >3) return true;
		return false;
	}
};

List<String> list = Arrays.asList("bar","apple","phone");
Object find = CollectionUtils.find(list, predicate);
System.out.println(find);//apple
```
### 6.2 CollectionUtils.filter(c,predicate)
返回集合类c按照断言predicate过滤后的集合，直接改变了c集合，即移除了c集合中不满足断言predicate的元素

### 6.3 CollectionUtils.countMatches(c,predicate)
返回集合类c中满足断言predicate的元素数量

### 6.4 CollectionUtils.select(c,predicate)
返回集合类c中满足断言predicate的元素构成的一个新集合，原集合不变化

### 6.5 CollectionUtils.isEmpty(c)
判断集合是否为空对象或集合是否有元素存在