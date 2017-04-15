---
layout: post
title:  "文件操作"
categories: Java
tags:  java
author: gaos
---

* content
{:toc}

文件操作在jdk中有主要类File，在第三方包commons-io中有FileUtils，关于它们的基本使用这里不再赘述，这里主要针对几种情景需求做下api使用总结。




## 1.创建新的文件
需求:不管文件是否存在，都创建新的文件，也即若其存在，则完全覆盖。

`假如现在有一个File对象，知道其是文件，但不知道其在硬盘上的真实文件资源是否存在，就是要在硬盘上
建立新的文件资源，应该如何做?`

> * `FileUtils.touch(File file)`
此方法是创建新文件使用的，不能创建文件夹。若其父目录不存在，则会自动创建父目录。若文件本身已经存在,则修改其修改日期属性。api中提到，若修改其日期属性不成功，则抛出异常。一般这是因为文件被占用，才无法修改日期，但我目前还无法模拟这种情形

> * `boolean FileUtils.deleteQuietly(File file)`
这个方法永远不会抛出异常，若File对象不存在，则删除失败，返回false；若File对象表示文件夹，则会递归删除。那么它与`File.delete()`方法有什么区别呢?
其一:File.delete()删除文件夹时，要求其是`空文件夹`。
其二:File.delete()删除文件或文件夹失败时(不包括资源不存在)，不会抛出异常。

所以结合考虑，不管其是否存在，先删掉此文件，再创建此文件
```java
    /**
     * 强制创建新的文件，不管其是否已经存在
     * @param filePath 文件路径
     * @throws IOException 
     */
    public static void forceCreateFile(String filePath) throws IOException {
        File file = new File(filePath);
        org.apache.commons.io.FileUtils.deleteQuietly(file);
        org.apache.commons.io.FileUtils.touch(file);
    }
```
## 2.创建新的文件夹
基本思考过程同上，先不管文件夹是否存在，先删除掉，再去建立。先了解一个方法的使用。

`FileUtils.forceMkDir(File file)`
若文件夹的父目录不存在，则其会创建父目录及自身文件夹；
若文件夹已经存在，则不作任何处理。
```java
    /**
     * 强制创建新的文件夹，不管其是否已经存在
     * @param dirPath 文件夹路径
     * @throws IOException
     */
    public static void forceCreateDir(String dirPath) throws IOException {
        File file = new File(dirPath);
        org.apache.commons.io.FileUtils.deleteQuietly(file);
        org.apache.commons.io.FileUtils.forceMkdir(file);
    }
```