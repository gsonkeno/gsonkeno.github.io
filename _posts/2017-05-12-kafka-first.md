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
		  zookeeper集群的地址，可以是多个，多个之间用逗号分割 hostname1:port1,hostname2:port2,hostname3:port3
		  
④ 启动zookeeper
进入kafka目录，敲入命令bin/zookeeper-server-start.sh config/zookeeper.properties &
		    
⑤ 启动kafka
进入kafka目录，敲入命令 bin/kafka-server-start.sh config/server.properties &
		
⑥ 检测2181与9092端口
netstat -tunlp|egrep "(2181|9092)"
        
⑦ 单机连通性测试
运行producer
```bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test```
早版本的Kafka，--broker-list localhost:9092需改为--zookeeper localhost:2181   

---
运行consumer                         
```bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning```
在producer端输入字符串并回车，查看consumer端是否显示。 