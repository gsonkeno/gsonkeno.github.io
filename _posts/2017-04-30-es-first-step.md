---
layout: post
title:  "elasticsearch入门学习"
categories: Java
tags:  java elasticsearch
author: gaos
---

* content
{:toc}

本文学习elasticsearch主要以阅读elasticsearch英文文档为主，辅以网上查找国内的相关文档博客，详见文章末尾的参考。另外，本文是针对elasticsearch5.3版本的学习笔记。




## 1 工具准备
### 1.1 elasticsearch安装包
这个很简单，不做说明，在elasticsearch的官网上下载安装即可，在安装包的内部`bin`目录中有`.bat`文件和`.sh`文件，说明同时支持windows环境和linux环境。

### 1.2 head 插件

### 1.3 curl命令工具
在linux下安装curl命令工具不再赘述。

在windows下安装命令工具，可到[curl下载地址](https://curl.haxx.se/download.html)里下载windows平台的安装包，另外可以看到其下方还有`cygwin`安装包，其实`cygwin`安装包是辅助工具，如果直接在cmd窗口执行`curl`命令，由于windows解析命令行机制与linux下解析命令行机制有些许不同，导致在linux下可以执行的`curl`命令在windows下无法执行。`cygwin`是在windows平台上运行的类unix环境，在`cygmin`上执行`curl`命令即可。如果你已经在windows上安装了`git`工具包，那么它自带的`mingw`工具也能起到类似模拟`unix`环境的效果。

## 2.字段类型
在官方文档中字段类型介绍了有很多中，这里截取我认为比较常用、比较重要的几个类型做下说明。
### 2.1 数组 Array datatype
在es中，没有专门区分元素类型的数组类型，es中的数组可以包含0个或多个值。但是所有这些元素类型都必须一致。

- 字符串数组 `["one","two"]`
- 整数数组 `[1,2]`
- 对象数组 `[ { "name": "Mary", "age": 12 }, { "name": "John", "age": 10 }]`
- 特别的,`[ 1, [ 2, 3 ]]` 等价于`[ 1, 2, 3 ]`，属于整数数组

以上例子均满足数组元素类型一致性。

当在mapping结构中动态添加一个数组类型字段时,数组中第一个元素的类型决定了这个数组的类型,这个数组中后面的元素必须与第一个元素的类型相同，或者说，后面的元素类型可以强制转换为第一个元素的类型。
(`何为动态添加?动态添加就是mapping不是事先定义好而后插入符合字段格式的文档，而是根本事先就没有定义，
直接插入文档时，es内部会根据文档的字段类型而分析得到mapping的字段类型`)

根据以上的定义，一个拥有不同元素类型的数组可能是不被es所支持的，如 `[ 10, "some string" ]`，字符串是无法转换为数字的。

`demo`:

- curl命令插入一条文档
```json
curl -XPUT 'localhost:9200/my_index/my_type/1?pretty' -H 'Content-Type: application/json' -d'
{
  "message": "some arrays in this document...",
  "tags":  [ "elasticsearch", "wow" ], 
  "lists": [ 
    {
      "name": "prog_list",
      "description": "programming list"
    },
    {
      "name": "cool_list",
      "description": "cool stuff list"
    }
  ]
}
'
```
- 查看mapping结构
```json
{
    "state":"open",
    "settings":{
        "index":{
            "creation_date":"1492918343624",
            "number_of_shards":"5",
            "number_of_replicas":"1",
            "uuid":"EX1PbRS5SCqL4yv2be4hZw",
            "version":{
                "created":"5020099"
            },
            "provided_name":"my_index"
        }
    },
    "mappings":{
        "my_type":{
            "properties":{
                "lists":{
                    "properties":{
                        "name":{
                            "type":"text",
                            "fields":{
                                "keyword":{
                                    "ignore_above":256,
                                    "type":"keyword"
                                }
                            }
                        },
                        "description":{
                            "type":"text",
                            "fields":{
                                "keyword":{
                                    "ignore_above":256,
                                    "type":"keyword"
                                }
                            }
                        }
                    }
                },
                "message":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "tags":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                }
            }
        }
    },
    "aliases":[

    ],
    "primary_terms":{
        "0":1,
        "1":1,
        "2":1,
        "3":1,
        "4":1
    },
    "in_sync_allocations":{
        "0":[
            "OdypZrdsSAubpAw4MaUeOw"
        ],
        "1":[
            "4BkxDGL2TWy6NHhOvX5AgQ"
        ],
        "2":[
            "f9fJT1I3QoCajSzRl6qtOw"
        ],
        "3":[
            "xVSfOp1FSeKh36QlbSnfFg"
        ],
        "4":[
            "cB0CTdJGQ2CpbQmLhVYkSw"
        ]
    }
}
```
- head插件table格式查看文档
![table格式展示数据](http://images2015.cnblogs.com/blog/822071/201704/822071-20170423215347147-2031546353.jpg)
- head插件json格式查看文档
```json
{
    "took":2,
    "timed_out":false,
    "_shards":{
        "total":5,
        "successful":5,
        "failed":0
    },
    "hits":{
        "total":1,
        "max_score":1,
        "hits":[
            {
                "_index":"my_index",
                "_type":"my_type",
                "_id":"1",
                "_score":1,
                "_source":{
                    "message":"some arrays in this document...",
                    "tags":[
                        "elasticsearch",
                        "wow"
                    ],
                    "lists":[
                        {
                            "name":"prog_list",
                            "description":"programming list"
                        },
                        {
                            "name":"cool_list",
                            "description":"cool stuff list"
                        }
                    ]
                }
            }
        ]
    }
}
```
通过以上测试，可以发现在动态插入文档时，生成了`mapping`，文档中的 `tags` 字段是字符串数组，但是在 `mapping` 中显示是 `"type":"text"`,文档中的 `lists` 字段是对象数组，但是在 `mapping` 中显示的 `"properties":{...}`,即字段进行了嵌套。另外head插件在显示数组字段上表现是不尽人意的。

对于此demo，如果用java-api查询
```java
        Settings settings = Settings.builder().put("cluster.name", "elasticsearch").build();
        Client client = new PreBuiltTransportClient(settings).
                    addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));

        GetResponse response = client.prepareGet("my_index", "my_type", "1").get();
        Map<String, Object> source = response.getSource();

        System.out.println(source);
        //{lists=[{name=prog_list, description=programming list}, {name=cool_list, description=cool stuff list}],
        // message=some arrays in this document..., tags=[elasticsearch, wow]}

        Object lists = source.get("lists");
        System.out.println(lists);
        //[{name=prog_list, description=programming list}, {name=cool_list, description=cool stuff list}]

        if (lists instanceof ArrayList){
            System.out.println("lists是List对象"); //执行

            ArrayList lists1 = (ArrayList) lists;

            Object o = lists1.get(0);

            if (o instanceof Map){
                System.out.println("o是Map对象"); //执行
            }
        }
```    
可以明显的发现 `lists`字段的值是一个`ArrayList`对象,而对于其内部的每一个元素,又都是`Map`对象。至于`tags`字段，其值也是一个`ArrayList`对象，其内部的每一个元素是`String`对象。

数组类型是存储意义上的,``"type":"array"``是不会在mapping中出现的,根本是不存在的。

---
### 2.2 二进制 Binary datatype
二进制字段接收一个字节数组，这个字节数组映射着经过base64编码后而得到的字符串。
它不可`store`,也不可支持搜索

### 2.3 关键词 Keyword datatype
keyword这种数据类型可以将结构性数据编入索引，比如邮箱地址，主机名，邮编号码，标签。

它主要应用于过滤(filtering),排序(sorting),聚合(aggregations)，此字段类型`只支持精确值搜索`。

如果你想要为邮件主题内容或者产品说明编写索引，你最好使用 `text` 字段类型

## 3 元域 Meta-Fields

### 3.1 _all域
ElasticSearch默认为每个被索引的文档都定义了一个特殊的域 - '_all'，它自动包含被索引文档中一个或者多个域中的内容， 在进行搜索时，如果不指明要搜索的文档的域，ElasticSearch则会去搜索_all域。_all带来搜索方便，其代价是增加了系统在索引阶段对CPU和存储空间资源的开销。
   
默认情况，ElasticSarch自动使用_all所有的文档的域都会被加到_all中进行索引。可以使用"_all" : {"enabled":false} 开关禁用它。如果某个域不希望被加到_all中，可以使用 "include_in_all":false


## 4 映射参数 Mapping parameters
映射参数是对字段类型详细的解释说明。

### 4.1 index

`index`选项决定了该字段是否可以支持检索，接受`true`和`false`两个值，没有编入索引的字段是不可以被检索的。

### 4.2 store
默认地，字段的值通过索引`index`而变得可被搜索，但是他们并没有被存储`store`。这意味着这个字段可被搜寻`query`,但是原始值并不会被检索到。

通常情况下，这关系不大。一般字段的值本身就会是 元域 `_source`字段的一部分，这个元域字段默认是被存储`store`的。

某些情况下，设置一个字段`store`是有意义的。

- 比如你的文档`document`中拥有`title`, `date`, `content`三个字段，`content`字段的内容非常长。可能你只想检索`title`,`date`两个字段，并不想把这两个字段从内容非常长的`_source`字段中抽取出来,这就派上用场了。 
```
//加载mapping
curl -XPUT 'localhost:9200/my_index?pretty' -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "my_type": {
      "properties": {
        "title": {
          "type": "text",
          "store": true 
        },
        "date": {
          "type": "date",
          "store": true 
        },
        "content": {
          "type": "text"
        }
      }
    }
  }
}
'
//插入数据
curl -XPUT 'localhost:9200/my_index/my_type/1?pretty' -H 'Content-Type: application/json' -d'
{
  "title":   "Some short title",
  "date":    "2015-01-01",
  "content": "A very long content field..."
}
'
//检索mapping parameters store 字段
curl -XGET 'localhost:9200/my_index/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "stored_fields": [ "title", "date" ] 
}
'
```
被store的字段返回值是数组 `Stored fields returned as arrays`
为了前后一致性，被store的字段返回的值都是数组类型，因为你无法知道原始值是单个值，多个值还是一个空数组。
如果你想得到原始值，你可以通过检索`_source`字段

```
//执行结果
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "my_index",
        "_type" : "my_type",
        "_id" : "1",
        "_score" : 1.0,
        "fields" : {
          "date" : [
            "2015-01-01T00:00:00.000Z"
          ],
          "title" : [
            "Some short title"
          ]
        }
      }
    ]
  }
}
```
- 如果一个字段，它的值并不会出现在`_source`字段中(比如`copy_to` fields)

### 4.3 copy_to

这个`copy_to`映射参数允许你创建一个自定义`_all`元域字段，换句话说，就是多个字段的值可以复制一份到一个`组字段`中，这个`组字段`可以被当做一个普通的单个字段进行查询。比如,`姓(first_name)`和`名(last_name)`两个字段可以复制到`姓名(full_name)`字段中。
```
curl -XPUT 'localhost:9200/my_index?pretty' -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "my_type": {
      "properties": {
        "first_name": {
          "type": "text",
          "copy_to": "full_name" 
        },
        "last_name": {
          "type": "text",
          "copy_to": "full_name" 
        },
        "full_name": {
          "type": "text"
        }
      }
    }
  }
}
'
curl -XPUT 'localhost:9200/my_index/my_type/1?pretty' -H 'Content-Type: application/json' -d'
{
  "first_name": "John",
  "last_name": "Smith"
}
'
curl -XGET 'localhost:9200/my_index/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "full_name": { 
        "query": "John Smith",
        "operator": "and"
      }
    }
  }
}
'

//执行结果
{
  "took" : 88,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.51623213,
    "hits" : [
      {
        "_index" : "my_index",
        "_type" : "my_type",
        "_id" : "1",
        "_score" : 0.51623213,
        "_source" : {
          "first_name" : "John",
          "last_name" : "Smith"
        }
      }
    ]
  }
}

```
`first name`字段和`last name`字段可以独立地被这两个被这两个字段各自检索。而`full name`字段可以被这两个字段进行检索。

需要注意的几点有:
①原始的`_source`元域中不会展示复制字段，在本例中即`_source`中不会展示`full_name `字段。
②一个字段的值可以被复制到多个目标字段中,使用`"copy_to": [ "field_1", "field_2" ]`

### 4.4 include_in_all
此映射参数 指定该字段是否应该包含在`_all`字段中，默认值为`true`,除非你将`index`映射参数设为`false`

### 4.5 fields
`fields`映射参数可以使一个字段以不同的方式编入索引以达到不同的目的。举个例子，一个`string`类型的字段映射为`text`字段来进行全文检索，或映射为`keyword`字段来进行排序和聚合。
```
curl -XPUT 'localhost:9200/my_index?pretty' -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "my_type": {
      "properties": {
        "city": {
          "type": "text",
          "fields": {
            "raw": { 
              "type":  "keyword"
            }
          }
        }
      }
    }
  }
}
'
curl -XPUT 'localhost:9200/my_index/my_type/1?pretty' -H 'Content-Type: application/json' -d'
{
  "city": "New York"
}
'
curl -XPUT 'localhost:9200/my_index/my_type/2?pretty' -H 'Content-Type: application/json' -d'
{
  "city": "York"
}
'
curl -XGET 'localhost:9200/my_index/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "city": "york" 
    }
  },
  "sort": {
    "city.raw": "asc" 
  },
  "aggs": {
    "Cities": {
      "terms": {
        "field": "city.raw" 
      }
    }
  }
}
'
```
①The `city.raw` field is a `keyword` version of the `city` field.
②The `city` field can be used for full text search.
③The `city.raw` field can be used for sorting and aggregations
执行结果
```
{
  "took" : 52,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : null,
    "hits" : [
      {
        "_index" : "my_index",
        "_type" : "my_type",
        "_id" : "1",
        "_score" : null,
        "_source" : {
          "city" : "New York"
        },
        "sort" : [
          "New York"
        ]
      },
      {
        "_index" : "my_index",
        "_type" : "my_type",
        "_id" : "2",
        "_score" : null,
        "_source" : {
          "city" : "York"
        },
        "sort" : [
          "York"
        ]
      }
    ]
  },
  "aggregations" : {
    "Cities" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "New York",
          "doc_count" : 1
        },
        {
          "key" : "York",
          "doc_count" : 1
        }
      ]
    }
  }
}

```
可以看出升序排列时, `New York`在前面，`York`在后面，改为降序排列时，正好相反。
聚合结果稍后解释。

### 4.6 boost
该映射参数的默认值是1。基本上，它定义了在文档中该字段的重要性。boost的值越高，字段中值的重要性也越高。
### 4.7 term_vector
`词向量`,此属性的值可以设置为no(`默认值`)、yes、with_offsets、with_positions和with_positions_offsets。它定义是否需要计算该字段的Lucence向量(`term vector`)。`如果使用高亮,那就需要计算这个词向量`。

那么词向量是什么东西呢?
词向量包含着分析过程(analysis process)中产生的词条(term)信息，包括

- 一个词条列表
- 每个词条的位置
- 词条在原始字符串中的起始位置和结束位置

### 4.8 analyzer
该映射参数定义用于`索引`和`搜索`的分析器名称。它默认为全局定义的分析器名称(standard analyzer)

### 4.9 search analyzer
该参数定义了的分析器，用于处理发送到特定字段的那部分查询字符串。

### 4.10 fielddata
该参数是对于聚合分析非常有必要，对于`text`类型的字段，`fielddata`映射参数的默认值是default，这表示此字段是不可聚合分析的，如果你需要`text`字段能够聚合分析，请设置 `fielddata:true`

另外，大部分类型的字段默认是支持聚合分析的。
## 5 Query DSL

### 5.1查询和过滤

- 查询上下文(query context)
查询上下文中的查询字句`query clause`描述的是检索时文档匹配相关度

- 过滤上下文(filter context)
过滤上下文中的查询字句`query clause`描述的是检索时文档是否匹配

```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": { 
    "bool": { 
      "must": [
        { "match": { "title":   "Search"        }}, 
        { "match": { "content": "Elasticsearch" }}  
      ],
      "filter": [ 
        { "term":  { "status": "published" }}, 
        { "range": { "publish_date": { "gte": "2015-01-01" }}} 
      ]
    }
  }
}
'
```
①The `query` parameter indicates `query context`.
②The `bool` and two `match` clauses are used in query context, which means that they are used to score how well each document matches.
③The `filter` parameter indicates `filter context`.
④The `term` and `range` clauses are used in filter context. They will filter out documents which do not match, but they will not affect the score for matching documents.

### 5.2 全匹配(match_all)
最简单的匹配方式，会匹配所有的文档，且会使相关性分数`_score`为1。明显，它属于query,而不是filter。
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query": {
        "match_all": {}
    }
}
'
```
### 5.3 全文匹配(match)
match查询是最常用的一种全文检索方式，它可以将全文字段`full text`进行分析`analysis `,得到一个词条`term`列表，构成布尔`bool`查询。
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query": {
        "match" : {
            "message" : "this is a test"
        }
    }
}
'
```

### 5.4 多全文匹配(multi match)

这是一种对多个字段做相同查询单的手段。
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": {
    "multi_match" : {
      "query":    "this is a test", 
      "fields": [ "subject", "message" ] 
    }
  }
}
'
// fields中只要有一个字段满足匹配，就会返回该条记录
```

### 5.5 字符串查询(Query String Query)
字符串查询`String Query`会将查询语句进行分析`analysis`
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query": {
        "query_string" : {
            "default_field" : "content",
            "query" : "this AND that OR thus"
        }
    }
}
'
```

### 5.6 简单字符串查询(Simple Query String Query)

它与字符串查询`String Query`类似，不过它不会抛出异常，它会丢弃那些无效的部分。
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": {
    "simple_query_string" : {
        "query": "\"fried eggs\" +(eggplant | potato) -frittata",
        "analyzer": "snowball",
        "fields": ["body^5","_all"],
        "default_operator": "and"
    }
  }
}
'

```
### 5.7 词条查询(term)
词条查询`term`不会去对搜索时输入的内容做分析`analyze`,只会匹配给定字段中`含有`该`词条`的文档。请注意，`text`类型的字段是会被分析的，这意味着它们的值第一步会被默认分词器`default`分析，产生若干词条；第二步这些词条会被`转化为小写`写入倒排索引。这就表示你如果插入一个包含title字段值为China的文档时，因为它写入索引时变成了china,所以你用`term`查询China时，是不会有结果的。
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "should": [
        {
          "term": {
            "status": {
              "value": "urgent",
              "boost": 2.0 
            }
          }
        },
        {
          "term": {
            "status": "normal" 
          }
        }
      ]
    }
  }
}
'
```
### 5.8 多词条查询(terms)
与词条查询`term`类似，允许匹配在那些内容中含有某些词条的文档，这个某些默认是一条文档即可，你可以设置`minimum_match`来决定至少包含几个词条。

### 5.9 范围查询(range)
范围查询针对单字段，字段可以是字符串的，也可以是数字的，这都会映射到`lucence`进行查询。
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query": {
        "range" : {
            "age" : {
                "gte" : 10,
                "lte" : 20,
                "boost" : 2.0
            }
        }
    }
}
'
```
### 5.10 前缀查询(prefix Query)
匹配特定的字段以给定的前缀开始的文档
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{ "query": {
    "prefix" : { "user" :  { "value" : "ki", "boost" : 2.0 } }
  }
}
'
```

现在举个例子，现在你创建了一个索引，设置了type，mapping也设置了，其中有一个字段叫 `HPHM`，存储的是不带归属地的字符串,该字段类型`是keyword`即不进行分析，直接编入索引。现在你往索引中插入一条文档，文档的`HPHM`字段的值是`A12345`,如果你对该字段进行前缀查询，请务必使用`A`开头进行前缀查询，不要使用`a`开头进行前缀查询。

因为，`keyword`类型的字符串不会进行解析，编入索引时就是`A12345`，进行前缀查询时也不会对输入的字符串进行解析。

### 5.11 通配符查询(Wildcard Query)
通配符查询与词条查询非常相似，只不过可以进行模糊匹配，主要是`?`和`*`两个通配符，前者代表一个字符，后者代表若干个字符(包含0个)。
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query": {
        "wildcard" : { "user" : { "value" : "ki*y", "boost" : 2.0 } }
    }
}
'
```

搜索时输入的内容不会被分析，文档中的内容已经被分词成若干个词条，其实是拿输入内容和文档特定字段的若干词条进行通配符匹配查询，切记不是与文档特定字段的整个内容进行通配符查询。(除非你的这个字段没有被分词`analyze`,纯理解，待验证)
### 5.12 模糊查询(Fuzzy Query)
模糊查询基于`距离算法`,能够查询指定距离内的相似文档。
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query": {
        "fuzzy" : {
            "user" : {
                "value" :         "ki",
                    "boost" :         1.0,
                    "fuzziness" :     2,
                    "prefix_length" : 0,
                    "max_expansions": 100
            }
        }
    }
}
'
``` 
查找到的文档与`ki`差异不能超过两个字符。
![fuzzy示例](http://images2015.cnblogs.com/blog/822071/201704/822071-20170429102100537-426413732.jpg)

### 5.13 常量评分查询(Constant Score Query)
此`复合查询`允许一个查询`query`包裹一个查询`query`或`filter`,匹配的文档相关性得分全设为常量。
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query": {
        "constant_score" : {
            "filter" : {
                "term" : { "user" : "kimchy"}
            },
            "boost" : 1.2
        }
    }
}
'
```

### 5.14 布尔查询(Bool Query)
布尔查询可以算是`复合查询`中最重要，最常见的查询方式了。每个布尔查询右若干个`布尔子句`组成，每个布尔子句都有其类型，类型共有`must`,`must not`,`should`,`filter`四种

### 5.15 加权查询(Boosting Query)
此`复合查询`使用不多，包含三个重要要素，`positive`部分，包含所返回文档`得分不会被改变`的查询；`negative`部分，返回的文档`得分被降低`;`negative_boost`部分，包含用来降低negative部分查询得分的加权值。
```
curl -XGET 'localhost:9200/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query": {
        "boosting" : {
            "positive" : {
                "term" : {
                    "field1" : "value1"
                }
            },
            "negative" : {
                 "term" : {
                     "field2" : "value2"
                }
            },
            "negative_boost" : 0.2
        }
    }
}
'
```
查询出来的文档`field`值必须包含`value1`,另外如果文档的`field2`值也包含`value2`,则该文档的得分变为0.2，其他的文档得分不变。

## 6 集群管理
### 6.1 集群健康度
指示的是集群中所有分片的分配情况，所有分片若妥善分配，则为100%
## 7.参考
- [官网elasticsearch指引文档](https://www.elastic.co/guide/en/elasticsearch/reference/5.3/index.html)
- [官网elasticsearch的java-api文档](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/index.html)
- [ElasticSearch的_all域](http://blog.csdn.net/quicknet/article/details/29341159)
- [csdn中的es文章系列1](http://blog.csdn.net/dm_vincent/article/category/2718099)
- [csdn中的es文章系列2](http://blog.csdn.net/yangwenbo214/article/category/6602335)