# 大数据总览

## 文件系统

- HDFS Hadoop Distributed File System, HDFS是一个高度容错性的系统，适合部署在廉价的机器上
- Ceph 新一代开源分布式文件系统，主要目标是设计成基于POSIX的没有单点故障的分布式文件系统，提高数据的容错性并实现无缝的复制。

## 数据存储

- mongodb
- redis
- hdfs
- hbase 是Hadoop的数据库，一个分布式、可扩展、大数据的存储，可以对大数据进行随机性的实时读取/写入访问,基于Hadoop和Hadoop分布式文件系统（HDFS）而建
- Neo4j 是一个高性能的，NOSQL图形数据库
- Cassandra 是一个混合型的非关系的数据库，类似于Google的BigTable

## 数据搜集

- Sqoop 用来在关系型数据库和Hadoop之间传输数据。可以使用Sqoop来从RDBMS(MySQL or Oracle)导入数据到Hadoop环境里，或者通过MapReduce转换数据，把数据导回到RDBMS
- Logstash 
- Scribe Facebook开源的日志收集系统
- Flume 是Cloudera提供的一个高可用的、高可靠的、分布式的海量日志采集、聚合和传输的系统

## 消息系统

- Kafka 
- RabbitMQ 
- ActiveMQ 

## 数据处理

- MapReduce
- Spark 
- Spark Streaming
- Flink 是一个高效、分布式的通用大数据分析引擎。Flink支持增量迭代计算，使得系统可以快速地处理数据密集型、迭代的任务
- Storm 类似于Hadoop的实时数据处理框架。编程模型简单，显著地降低了实时处理的难度毫秒级低延时

## 查询引擎

- Presto 
- Drill 
- Hive, Pig
- SparkSQL
- Kylin

## 分析报告

- Kylin 
- Kibana 
- Druid 
- Splunk 

## 调度管理

- YARN 
- Mesos 
- Azkaban 
- ZooKeeper 
- Ambari Hadoop生态系统的一部分，提供了基于Web的直观界面，可用于配置、管理和监控Hadoop集群