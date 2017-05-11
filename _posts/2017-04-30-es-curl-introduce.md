---
layout: post
title:  "elasticsearch的curl命令介绍"
categories: Java
tags:  java elasticsearch curl
author: gaos
---

* content
{:toc}

curl可以用来模拟http请求，最初使用到它，就是在学习elasticsearch的过程中使用curl命令向服务端发送请求。另外google浏览器支持一款curl请求的浏览器插件`sense`,在windows平台下操作比较方便，且有智能提示，比较友好；另外head插件是一款可视化插件，功能也是非常强大。本文只对在elasticsearch中常用的curl命令做个简单笔记积累，使用其他工具稍加修改即可。




## 1 新建索引
```
curl -XPUT 'localhost:9200/my_index?pretty&pretty'
```

## 2 创建mapping
### 2.1 方式一
```
curl -XPUT 'localhost:9200/my_index?pretty' -H 'Content-Type:application/json' -d'
{
  "mappings": {
    "my_type": {
      "properties": {
        "name": {
          "type": "text"
        },
        "blob": {
          "type": "binary"
        }
      }
    }
  }
}
'
```
### 2.2 方式二
```
curl -XPOST http://localhost:9200/index/fulltext/_mapping -d'
{
    "fulltext": {
        "_all": {
            "analyzer": "ik_max_word",
            "search_analyzer": "ik_max_word",
            "term_vector": "no",
            "store": "false"
        },
        "properties": {
            "content": {
                "type": "text",
                "analyzer": "ik_max_word",
                "search_analyzer": "ik_max_word",
                "include_in_all": "true",
                "boost": 8
            }
        }
    }
}'
```
索引名index,type为fulltext,`_all`中全部字段索引时和检索时都使用`ik_max_word`形式的ik分词器

## 3 插入一条文档
```
curl -XPUT 'localhost:9200/my_index/my_type/1?pretty' -H 'Content-Type:application/json' -d'
{
  "name": "Some binary blob",
  "blob": "U29tZSBiaW5hcnkgYmxvYg==" 
}
'
```

## 4 查询文档(by id)
```
curl -XGET 'localhost:9200/my_index/my_type/1?pretty&pretty'
```

## 5 删除索引
```
curl -XDELETE 'localhost:9200/my_index?pretty'
```
## 6 查看分词效果
```
curl -XPOST  "http://localhost:9200/my_index/_analyze?analyzer=ik_max_word&pretty=true&text=我是中国人" 
```
对于elasticsearch的默认分词器`standard_analyzer`来讲，会将中文汉字一个一个切分开来，这对于中国用户是不友好的，ik分词器可以有效的避免这一点。
## 7.参考
- [官网elasticsearch指引文档](https://www.elastic.co/guide/en/elasticsearch/reference/5.3/index.html)
- [官网elasticsearch的java-api文档](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/index.html)
- [ElasticSearch的_all域](http://blog.csdn.net/quicknet/article/details/29341159)
- [csdn中的es文章系列1](http://blog.csdn.net/dm_vincent/article/category/2718099)
- [csdn中的es文章系列2](http://blog.csdn.net/yangwenbo214/article/category/6602335)