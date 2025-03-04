---
layout: post
title: Part 5 - Kafka Producer
date: 2021-06-15 13:29 -0600
categories: kafka
tags: kafka-series
---

This section describes the Kafka Producer and its internals.
For implementation, please visit the [github](https://github.com/ashimjk/apache-kafka-series/tree/master/kafka-client).

## Three Major Required Properties
- bootstrap.servers
  - required for a producer to start up
  - used for discovering full membership of the cluster
  - used for determining the partition leaders or owners
- key.serializer
- value.serializer

Here is the full list of [producer configs](https://kafka.apache.org/documentation/#producerconfigs).

## Producer Record
- topic
  - Topic to which the ProducerRecord will be sent
- value
  - The message content (matching the serializer type for value)
- partition
  - specific partition within the topic to send ProducerRecord
- timestamp
  - Unix timestamp applied to the record
    Kafka Producer instances can only send ProducerRecord that matches the key and value serializer types it is configured with.
- key
  - a value to be used as the basis of determining the partitioning strategy to be employed by the Kafka Producer
  - Additional information in the message
  - Can determine what partitions the message will be written to

## Record Accumulator
- Implements Micro-batching pattern
  - Small and fast batches of messages
  - Sending (Producer)
  - Writing (Broker)
  - Reading (Consumer)
- Uses Queue for storing records
- Maintains RecordBatch for each topic partition

## Message Buffering Properties
- `batch.size (default: 16384)` : buffer size per RecordBatch in bytes
- `buffer.memory (default: 33554432)` : buffer size of all record batch in bytes
- `max.block.ms (default: 60000 - 1min)` : time for blocking the send record call
- `linger.ms (default: 0)` : time to wait before sending record

## Broker Acknowledgement - "acks"
- 0: fire and forget
- 1: leader acknowledged
- 2: replication quorum acknowledged

## Configuration for Broker that responds with an error
- `retries (default: 2147483647)`: how many times producer should try to send the record
- `retry.backoff.ms (default: 100)`: how many milliseconds does the producer need to wait for retries

## Message Order
- No guarantee of order if multiple partitions are used
- If there is an error while sending a record then there may be a chance of an unordered record
  - Set `max.in.flight.request.per.connection (default: 5)` to 1 if an order needs to be maintained

## Delivery Semantics
- at-most-once
- at-least-once
- only-once

## Kafka Producer Internals
- Properties ~> ProducerConfig
- Message ~> ProducerRecord
- Processing Pipeline ~> Serializers and Partitioners
- Micro-batching ~> RecordAccumulator and RecordBuffer

## Some useful configuration
- `delivery.timeout.ms (default: 120000)`: an upper bound on the time to report success or failure after a call to send
- `max.request.size (default: 1048576)`: maximum size of a request in bytes

## Kafka Series
- [Part 1 - Kafka Key Terminology](../kafka-key-terms)
- [Part 2 - Kafka Partitions](../kafka-partitions)
- [Part 3 - Run a single instance of Apache Kafka](../kafka-single-instance)
- [Part 4 - Run a multiple instances of Apache Kafka](../kafka-multiple-instance)
- [Part 6 - Kafka Consumer](../kafka-consumer)
- [Part 7 - Kafka Challenges](../kafka-challenges)
- [Part 8 - Producer/Consumer using Kafka Client](../kafka-client)
- [Part 9 - Producer/Consumer using Spring Kafka](../spring-kafka)