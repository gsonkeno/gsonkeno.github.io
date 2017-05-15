---
layout: post
title:  "kafka入门学习"
categories: Java
tags:  java kafka linux
author: gaos
---

* content
{:toc}

kafka入门学习笔记




## 安装部署
1.下载kafka并解压。

2.修改config/server.properties配置

① broker.id =0

每一个broker在集群中的唯一表示，要求是正数。当该服务器的IP地址发生改变时，broker.id没有变化，则不会影响consumers的消息情况
		    
② log.dirs=/data/kafka-logs

kafka数据的存放地址目录，多个地址目录的话用逗号分割, 多个目录分布在不同磁盘上可以提高读写性能  /data/kafka-logs-1，/data/kafka-logs-2
		  
③ zookeeper.connect = localhost:2181

zookeeper集群的地址，可以是多个，多个之间用逗号分割hostname1:port1,hostname2:port2,hostname3:port3
		  
④ 启动zookeeper

进入kafka目录，敲入命令bin/zookeeper-server-start.sh config/zookeeper.properties &
		    
⑤ 启动kafka

进入kafka目录，敲入命令 bin/kafka-server-start.sh config/server.properties &
		
⑥ 检测2181与9092端口

```
netstat -apn|grep 2181
netstat -apn|grep 9092
```
        
⑦ 单机连通性测试
运行producer

```bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test```

早版本的Kafka，--broker-list localhost:9092需改为--zookeeper localhost:2181   

---

运行consumer        

```bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning```

在producer端输入字符串并回车，查看consumer端是否显示。 

## 常用命令

### 创建主题
- linux
```
//新建一个拥有三个副本，一个分区的主题
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic
```
- windows
```
//新建一个拥有三个副本，一个分区的主题
bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic
```

### 查看主题状态
- linux
```
bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic
```

- windows
```
bin\windows\kafka-topics.bat --describe --zookeeper localhost:2181 --topic my-replicated-topic
```

### 生产消息
- linux
```
bin/kafka-console-producer.sh --broker-list 192.168.10.1:9092 --topic my-replicated-topic
```
- windows
```
bin\windows\kafka-console-producer.bat --broker-list 192.168.10.1:9092 --topic my-replicated-topic
```

### 消费消息
- linux
```
bin/kafka-console-consumer.sh --bootstrap-server 192.168.10.1:9092 --from-beginning --topic my-replicated-topic
```
- windows
```
bin\windows\kafka-console-consumer.bat --bootstrap-server 192.168.10.1:9092 --from-beginning --topic my-replicated-topic
```

### 删除主题
- linux
```
bin/kafka-topics.sh --delete --zookeeper 192.168.10.1:2181 --topic test
```
- windows
```
bin\windows\kafka-topics.bat --delete --zookeeper 192.168.10.1:2181 --topic test
```

### 查看所有主题
- linux
```
bin/kafka-topics --list --zookeeper 192.168.10.1:2181
```

- windows
```
bin\windows\kafka-topics --list --zookeeper 192.168.10.1:2181
```

## 重要知识点

### group consumer
<font color="ff0000">若一个消息被一个消费者consumer消费了，则此消息不能被该消费者consumer所在的组内的其他消费者再次消费。</font>

<font color="ff0000">一个消息可以被不同的组group消费，对于每个组而言，实质上是组group内部的某一个消费者consumer消费</font>

### 日志log
- 如果一个topic的名称为"my_topic",它有2个partitions,那么日志将会保存在my_topic_0和my_topic_1两个目录中


### leader and follower

- kafka将每个partition数据复制到多个server上,任何一个partition有一个leader和多个follower(可以没有);备份的个数可以通过broker配置文件来设定。<font color="#ff0000">leader处理所有的read-write请求,follower需要和leader保持同步。</font>

### java api
可以参考:

[kafka中文教程](http://orchome.com/kafka/index)

[kafka-java-api doc](http://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html)

### kafka监测工具
[KafkaOffsetMonitor](https://github.com/Morningstar/kafka-offset-monitor/releases)
## 参考文档

- [kafka百度百科](http://baike.baidu.com/link?url=su33oZuNAPZbY6_AHeHA4rC3waV-CYXo4qJelfIesstCWKsvKSV-N1U6GKkj5bRFhGGHFdX-xnhaiFYg5-fUH_)
- [kafka中文教程](http://orchome.com/kafka/index)
- [kafka入门中文翻译](http://www.cnblogs.com/likehua/p/3999538.html)
- [kafka-java-api doc](http://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html)
- [KafkaOffsetMonitor](https://github.com/quantifind/KafkaOffsetMonitor)