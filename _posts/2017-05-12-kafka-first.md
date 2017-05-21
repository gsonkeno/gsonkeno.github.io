---
layout: post
title:  "kafka入门学习"
categories: Java
tags:  java kafka linux apache
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

### 查询topic的offset的范围
- linux
```
bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 192.168.10.1:9092 -topic test --time -2 //查询test主题最小offset
bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 192.168.10.1:9092 -topic test --time -1 //查询test主题最大offset
```
- windows
```
bin\windows\kafka-run-class.bat kafka.tools.GetOffsetShell --broker-list 192.168.10.1:9092 -topic test --time -2 //查询test主题最小offset
bin\windows\kafka-run-class.bat kafka.tools.GetOffsetShell --broker-list 192.168.10.1:9092 -topic test --time -1 //查询test主题最大offset
```

![示例结果](http://images2015.cnblogs.com/blog/822071/201705/822071-20170521104935885-1822001346.jpg)
结果中，第一列是主题，第二列是分区号，第三列是偏移量
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

### ISR
Leader会跟踪与其保持同步的Replica列表，该列表称为ISR（即in-sync Replica）。

如果一个Follower宕机，或者落后太多，Leader将把它从ISR中移除。这里所描述的“落后太多”指Follower复制的消息落后于Leader后的条数超过预定值（该值可在`$KAFKA_HOME/config/server.properties中通过replica.lag.max.messages`配置，其默认值是`4000`）或者Follower超过一定时间（该值可在`$KAFKA_HOME/config/server.properties中通过replica.lag.time.max.ms`来配置，其默认值是`10000`）未向Leader发送fetch请求。

详见参考文档6

### 消息有序性
kafka只能保证一个partition中的消息被某个consumer消费时,消息是顺序的.事实上,从Topic角度来说,消息仍不是有序的。

### __consumer_offsets
由于Zookeeper并不适合大批量的频繁写入操作，新版Kafka已推荐将consumer的位移信息保存在Kafka内部的topic中，即`__consumer_offsets` topic，并且默认提供了kafka_consumer_groups.sh脚本供用户查看consumer信息。

详见参考文档7

### java api
可以参考:

[kafka中文教程](http://orchome.com/kafka/index)

[kafka-java-api doc](http://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html)

## kafka监测工具
### [KafkaOffsetMonitor](https://github.com/Morningstar/kafka-offset-monitor/releases)

### kafka-manager
- 修改配置文件
修改config/application.conf文件下的`kafka-manager.zkhosts`项,比如
```
kafka-manager.zkhosts="192.168.10.1:2181,192.168.10.2:2181,192.168.10.3:2181"
```
- 启动
在指定端口，指定配置文件下后台启动(默认端口是9000)
```
nohup bin/kafka-manager -Dconfig.file=conf/application.conf -Dhttp.port=8080 &
```
## <span id="reference">参考文档</span>

- 1 [kafka百度百科](http://baike.baidu.com/link?url=su33oZuNAPZbY6_AHeHA4rC3waV-CYXo4qJelfIesstCWKsvKSV-N1U6GKkj5bRFhGGHFdX-xnhaiFYg5-fUH_)
- 2 [kafka中文教程](http://orchome.com/kafka/index)
- 3 [kafka入门中文翻译](http://www.cnblogs.com/likehua/p/3999538.html)
- 4 [kafka-java-api doc](http://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html)
- 5 [KafkaOffsetMonitor](https://github.com/quantifind/KafkaOffsetMonitor)
- 6 [Kafka设计解析（二）：Kafka High Availability （上）](http://www.infoq.com/cn/articles/kafka-analysis-part-2/)
- 7 [__consumer_offsets](http://blog.csdn.net/xw_classmate/article/details/53264303)