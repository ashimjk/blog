---
layout: post
title: Part 3 - Run a single instance of Apache Kafka
date: 2021-06-15 13:29 -0600
categories: kafka
tags: kafka-series
---

To follow the below sections, you need to set up the Apache Kafka.
And for that, you can follow the steps define in [startup section](../kafka).

After the installation, go to the Kafka directory, something like this `kafka_2.13-2.8.0`
and follow through the steps to create a topic, start producing and consuming the messages.

## Kafka Topic

### Create
`bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic my_topic`

### List
`bin/kafka-topics.sh --list --zookeeper localhost:2181`

### View
`bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my_topic`


## Kafka Console Producer
`bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic my_topic`


## Kafka Console Consumer
`bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic my_topic --from-beginning`


## Kafka Message Log
- Stored in `/tmp/kafka-logs`
- For the above topic, `my_topic-0` directory will be created
- View log using `cat /tmp/kafka-logs/my_topic-0/00000000000000000000.log`

## Kafka Series
- [Part 1 - Kafka Key Terminology](../kafka-key-terms)
- [Part 2 - Kafka Partitions](../kafka-partitions)
- [Part 4 - Run a multiple instances of Apache Kafka](../kafka-multiple-instance)
- [Part 5 - Kafka Producer](../kafka-producer)
- [Part 6 - Kafka Consumer](../kafka-consumer)
- [Part 7 - Kafka Challenges](../kafka-challenges)
- [Part 8 - Producer/Consumer using Kafka Client](../kafka-client)
- [Part 9 - Producer/Consumer using Spring Kafka](../spring-kafka)