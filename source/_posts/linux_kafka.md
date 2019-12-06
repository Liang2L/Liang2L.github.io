---
title: "Linux常用命令"
date: 2019-07-15 15:47:11
categories:
- "常用命令"
- "Kafka"
---
## kafka启动
bin/kafka-server-start.sh config/server.properties

## kafka生产者启动
bin/kafka-console-producer.sh --broker-list localhost:20882 --topic test

## kafka消费者启动
bin/kafka-console-consumer.sh --zookeeper localhost:48890 --topic test --from-beginning

## kafka创建topic
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 3 --topic test

## kafka topic列表
bin/kafka-topics.sh --list --zookeeper localhost:48889
