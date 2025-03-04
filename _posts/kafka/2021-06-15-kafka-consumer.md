---
layout: post
title: Part 6 - Kafka Consumer
date: 2021-06-15 13:29 -0600
categories: kafka
tags: kafka-series
---

This section describes the Kafka Consumer and its internals.
For implementation, please visit the [github](https://github.com/ashimjk/apache-kafka-series/tree/master/kafka-client).

## Three Major Required Properties
- bootstrap.servers
  - required for the consumer to start up
  - used for discovering full membership of the cluster
  - used for determining the partition leaders or owners
- key.deserializer
- value.deserializer

Here is the full list of [consumer configs](https://kafka.apache.org/documentation/#consumerconfigs).

## What subscription does?
- It will use automatic/dynamic partition assignment for topics
- Providing a single topic means pulling from every partition with that topic (one-to-many relationship)
- Providing multiple topics means pulling from every partition for all topics (many-to-many relationship)

## Consumer Coordinator
- It's aware of automatic or dynamic partition reassignment
- Push notification of assignment changes to the subscription state object
- It commits the offsets to the cluster

## Offset
- The message that has been read, does not mean its also committed
- Configuration for Offset Gap
  - enable.auto.commit = true (default)
  - auto.commit.interval.ms = 5000 (default)
  - auto.offset.reset = "latest" (default)
    - "earliest"
    - "none"
- Offset behavior varies between whether you are a single consumer or a consumer group topology

## Offset Management
- There are two different ways to manage the offset commit: automatic/manual
- To use manual offset commit, we need to set `enable.auto.commit` config to false
- There are two ways to commit offset in manual mode:
  - commitSync
    - It is a synchronous request which blocks until it receives the response from the cluster
    - In case of error, we can use `retry.backoff.ms (default: 100)` config for retries until it succeeds or unrecoverable error
  - commitAsync
    - As the name suggests, it is an asynchronous request which a non-blocking but also a non-deterministic
    - It doesn't have retries functionality
    - It also supports callback option which can help to process even further when the response comes from the cluster

## Consumer Group
- It's a Kafka solution to consumer side scale-out
- `group.id` config can be used for defining the consumer group
- The main goal of the consumer group is to share the message consumption and processing load
  - provides parallelism and high throughput
  - increases the level of redundancy
  - increases the performance of message processing

## Group Coordinator
- It is a broker who is elected to serve as a group coordinator
- It monitors and maintains the consumer groups membership using
  - `heartbeat.interval.ms` : 3000 (default)
  - `session.timeout.ms` : 10000 (default)
- It evenly balances available consumers to partitions
- It tries to assign one consumer to one partition of that topic but it varies on no of consumer and partitions
- It also initiates re-balancing protocol which will happen only if
  - new partitions are added
  - any consumer failure

## Kafka Consumer Internals
- Properties ~> ConsumerConfig
- Message ~> ConsumerRecord

## Some useful configuration
- `fetch.min.bytes (default: 1)` : minimum number of bytes that must be returned from the poll
- `fetch.max.bytes (default: 52428800 - 50MB)` : maximum number of bytes that must be returned from the poll
- `max.fetch.wait.ms (default: 500)` : amount of time to wait if there isn't enough data to meet above threshold
- `max.partition.fetch.bytes (default: 1048576 - 1MB)` : maximum number of bytes to be returned per partition per cycle
- `max.poll.records (default: 500)` : maximum number of records allowed per poll cycle
- `max.poll.interval.ms (default: 300000 - 5min)` : maximum delay between invocations of poll
- `allow.auto.create.topics (default: true)` : allows automatic topic creation

## Some useful consumer control
- Consumer Position Control
  - seek() ~> read any specific message by providing an offset
  - seekToBeginning() ~> read message from the beginning
  - seekToEnd() ~> read message from the end
- Flow Control
  - pause() ~> pause the message for any partition of topic
  - resume() ~> resume the message for any partition of topic
- Re-balance Listeners ~> listen for consumer group re-balancing

## Kafka Series
- [Part 1 - Kafka Key Terminology](../kafka-key-terms)
- [Part 2 - Kafka Partitions](../kafka-partitions)
- [Part 3 - Run a single instance of Apache Kafka](../kafka-single-instance)
- [Part 4 - Run a multiple instances of Apache Kafka](../kafka-multiple-instance)
- [Part 5 - Kafka Producer](../kafka-producer)
- [Part 7 - Kafka Challenges](../kafka-challenges)
- [Part 8 - Producer/Consumer using Kafka Client](../kafka-client)
- [Part 9 - Producer/Consumer using Spring Kafka](../spring-kafka)