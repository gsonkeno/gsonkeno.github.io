---
layout: post
title:  "kafka学习中遇到的问题"
categories: Java
tags:  java kafka linux
author: gaos
---

* content
{:toc}

在学习kafka的过程中，个人遇到了一些问题，有的可以在网上快速的找到解决方式，但是有的却并没有有效地在搜索引擎中寻得解决，总体上看浪费了不少的时间，这里做下记录，供分享。




## kafka消费主题阻塞
```
ConsumerRecords<String, String> records = consumer.poll(100);
```
以上代码来自官方api，在消费数据时，此方法会阻塞，解决方式:

将kafka中的配置文件(consumer相关,producer相关,server相关)中的关于主机的配置全部显示使用本机ip,具体原因暂不分析。

## kafka-java-api删除主题报错
通过`kafka api`的`TopicCommand`类的`main(String[] option)`执行删除主题操作时，可能报错:

`Error while executing topic command : org/apache/kafka/common/internals/TopicConstants`

你可以换种api执行删除主题操作,如下:
```
//删除主题
public static void deleteTopic(String zkAddr, String topicName){
//总是失败，暂不知原因
//        String[] options = new String[] {
//                "--delete",
//                "--zookeeper",zkAddr,
//                "--topic", topicName };
//
//        TopicCommand.main(options);

        ZkUtils zkUtils = ZkUtils.apply(zkAddr, 30000, 30000, JaasUtils.isZkSecurityEnabled());
        // 删除topic 't1'
        AdminUtils.deleteTopic(zkUtils, topicName);
        zkUtils.close();
    }
```
## kafka集群搭建异常
kafka的集群搭建可以参照网上相关文档。但是本人在启动时，前2个结点自动形成了集群，第三个结点却没有自动加入集群，日志中发现一句警告:

`WARN No meta.properties file under dir /tmp/kafka-logs/meta.properties (kafka.server.BrokerMetadata
`

指的是在kafka日志文件目录下没有`meta.properties`文件，但是经查看验证，此文件是存在的，接着日志警告位置往上看，会发现一句日志记录如下:
```
[2017-05-17 22:06:24,749] INFO Log directory '/tmp/kafka-logs' not found, creating it. (kafka.log.LogManager)
```
**那为什么警告中显示文件不存在呢?文件不存在为什么此结点进入不了集群呢?**

个人现在还不是很明白，不过能够确定的是日志表明文件不存在的时候，文件确实还未创建成功。

**暂时的处理方式是关闭kafka进程，重新启动一次，则结点成功加入集群。**