---
layout: post
title: Part 1 - Kafka Key Terminology
date: 2021-06-15 13:29 -0600
categories: kafka
tags: kafka-series
---

This section focuses on the key terminology and concepts about Apache Kafka.
And also note that each has its contribution to Apache Kafka Architecture.

## Distributed Messaging System
Kafka is a distributed messaging system which is designed for:
- high throughput
- reliability
- resilient
- fast movement of data through the more and more diverse system, particularly when data is growing

## Producers / Publisher
A publisher creates some data and sends it to a broker. Producers are simply an applications
that you write to send the messages.

## Consumers / Subscriber
A subscriber retrieves the message and processes it from the broker to which
it is interested and authorized. Consumers are simply applications
that you write to receive the messages.

## Topics
It is the collection of messages or grouping of messages. And every topic has its name, which is used by producers and consumers to send/receive messages. Topics can
be defined upfront or on-demand as needed.

## Broker or Worker or Node
Kafka broker is a software process also referred to as executable or daemon service
that runs on a machine that can be physical or virtual.

## Cluster
A kafka cluster is the grouping of multiple kafka brokers which can be on a single machine or different machines.

## Controller
A controller is just a worker node or broker like any other. It just happened to be elected from amongst
its peer to officiate in the administrative capacity of a controller. Below are the critical responsibilities of
the controller:
- To maintain an inventory of what workers are available to take on work.
- To maintain a list of work items that have been assigned to workers.
- To maintain the status of workers
- To manage the replication factor and task redundancy in case of failure

## Leader
A leader is also a worker node or broker like any other. It is elected by the controller for assigning the task.
It will peer with other worker nodes based on the replication factor to complete its task. Once a peer has committed to its
leader, the quorum is formed. And these peer takes all the task that is assigned to its leader.

## Communication and Consensus
Virtually every component within a cluster has to keep some form of communication going between themselves.
In a distributed system, these communications are called consensus. Besides the obvious data payloads being transferred
as messages, there are other types of network communication happening that keep the cluster operating normally.
These are some of them:
- Worker node membership and naming
- Configuration management - startup-config
- Leader election
- Health status

## Zookeeper
In the context of Apache Kafka, ZooKeeper serves as a centralized service for maintaining metadata about a cluster
of distributed nodes, which are:
- Configuration information
- Health and Synchronization Status
- Cluster and Quorum Group membership
- Roles of elected nodes

Also, it is the responsibility of the Zookeeper to assign a broker to be responsible for the topic.

## Topic message order
Every message that comes to the broker is grouped by topics and is stored inside a partition.
And each partition stores these messages in an ordered sequence (by time) and immutable by nature means
cannot be updated once it is received by the broker. But in the case of multiple partitions, an order cannot be
guaranteed and it's the responsibility of the consumer to handle this use case.

## Event Souring
An architectural style or approach to maintaining an application’s state by capturing all changes as a
a sequence of time-ordered, immutable events.

## Message Content
Every message that is consumed by the broker contains these three properties:
- Timestamp
- Reference-able identifier
- Payload (binary)

## Offset
It is the last read message position that is maintained by the Kafka consumer. It also corresponds to
the message identifier.

## Message Retention Policy
It is the period for how long messages will be stored in the broker. By default, it is set to 168 hours or seven days.
The retention period is defined on a per topic basis. Physical storage can also constrain the message retention policy.

## Distributed Commit Log (Transaction or Commit Logs)
It is the series of commit logs that can also be used for event sourcing. Following are the key properties of it:
- Source of truth
- Physically stored and maintained
- Higher-order data structures derived from the log
  - Tables, indexes, views, etc
- Point of recovery
- Basis for replication and distribution

## Kafka Partitions
It is the physical representation of the topic as a commit log stored on one or more brokers. It can be found inside
`/tmp/kafka-logs/{topic}-{partition}` directory, as per our example, `/tmp/kafka-logs/my_topic-0` directory will be
created. This directory will contain an index, log files, etc. These are the main properties of partitions:
- Partitions == Log
- Each topic has one or more log files called partitions
- Basis for
  - Scaling
  - Fault-tolerant
  - Higher levels of throughput
- At-least one partition is required for one or more broker

## Replication Factor
It is the factor that decides how much data redundancy should happen inside the cluster.
And this is configured on a per-topic basis. These are the features that it provides:
- Reliable work distribution
  - Redundancy of messages
  - Cluster resiliency
  - Fault-tolerance
- Guarantees
  - N-1 broker failure tolerance
  - 2 or 3 minimum
- Broadcast In-Sync Replicas or ISR to the cluster is equal to the replication factor for that topic and partition

## Notes
- The scalability of Apache Kafka is determined by the number of partitions being managed by multiple broker nodes

## Kafka Series
- [Part 2 - Kafka Partitions](../kafka-partitions)
- [Part 3 - Run a single instance of Apache Kafka](../kafka-single-instance)
- [Part 4 - Run a multiple instances of Apache Kafka](../kafka-multiple-instance)
- [Part 5 - Kafka Producer](../kafka-producer)
- [Part 6 - Kafka Consumer](../kafka-consumer)
- [Part 7 - Kafka Challenges](../kafka-challenges)
- [Part 8 - Producer/Consumer using Kafka Client](../kafka-client)
- [Part 9 - Producer/Consumer using Spring Kafka](../spring-kafka)