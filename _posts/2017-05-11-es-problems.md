---
layout: post
title:  "elasticsearch学习中遇到的问题"
categories: Java
tags:  java elasticsearch linux
author: gaos
---

* content
{:toc}

本文就elasticsearch学习过程中遇到了一些问题，在通过搜索引擎寻找解决方式的过程中，积累了一些学习心得，供分享。




## head插件下载后要改动什么吗？
在参考中的前两篇文章中有提到要<font color="#ff0000">更改服务器监听地址(通过head/Gruntfile.js文件)</font>，也有提到<font color="#ff0000">要修改连接地址(通过head/_site/app.js)</font>

但就目前的实践结果来看，不需要改动什么文件，






## 参考文档
- [Elasticsearch 5.1.1 head插件安装指南](http://blog.csdn.net/napoay/article/details/53896348)
- [Elasticsearch 5.0 —— Head插件部署指南](http://www.cnblogs.com/xing901022/p/6030296.html)- [elasticsearch中的doc_values]