---
layout: post
title: Part 8 - Producer/Consumer using Kafka Client
date: 2021-06-15 13:29 -0600
categories: kafka
tags: kafka-series
---

In this section, we are going to use `kafka client api` and see how to programmatically create, configure and use Kafka Cluster.
Following are the main topic, that we are going to look at: 
- Use Admin API to create topics
- Send the message using Producer
- Receive the message using Consumer

## Dependency for Kafka Client
```xml
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>2.8.0</version>
</dependency>
```

## Create Configuration
Kafka requires configuration in key/values pairs. And for that we can use `java.util.Properites`
class. Following are required config for Kafka Producer:
```java
Properties properties = new Properties();
properties.put("bootstrap.servers", "localhost:9090,localhost:9091");
properties.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
properties.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
```

## Run Kafka Instance
Although, there many ways to start the kafka instance, which are:
- Using [kafka single instance](../kafka-single-instance)
- Using [kafka multiple instances](../kafka-multiple-instance)
- Using [docker-compose](https://github.com/ashimjk/apache-kafka-series/tree/master/docker-compose.yml)
- Using [TestContainers](https://www.testcontainers.org) for the test case

## Dependency for TestContainers
```xml
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>kafka</artifactId>
    <version>${test-container.version}</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>${test-container.version}</version>
    <scope>test</scope>
</dependency>
```

## Admin API Usage

### Required config
- bootstrap.servers

### Create Kafka Topic Programmatically
It will create the topic with default options. Note that `kafkaFuter.get()` will block the thread
until topic creation has been completed or failed.

```java
try (Admin admin = Admin.create(properties)) {
    int numOfPartitions = 1;
    short replicationFactor = 1;
    NewTopic newTopic = new NewTopic(topicName, numOfPartitions, replicationFactor);

    CreateTopicsResult topicsResult = admin.createTopics(Collections.singleton(newTopic));

    KafkaFuture<Void> kafkaFuture = topicsResult.values().get(topicName);
    kafkaFuture.get();
}
```

## Kafka Producer
### Required config
- bootstrap.servers
- key.serializer
- value.serializer

### Implementation
```java
try (KafkaProducer<String, String> producer = new KafkaProducer<>(properties)) {

        messages.entrySet()
        .stream()
        .map(p -> new ProducerRecord<>(TOPIC_NAME, p.getKey(), p.getValue()))
        .forEach(producer::send);

        } 
```

## Kafka Consumer
### Required config
- bootstrap.servers
- key.deserializer
- value.deserializer
- group.id

### Implementation
```java
try (KafkaConsumer<String, String> consumer = new KafkaConsumer<>(properties)) {
    consumer.subscribe(List.of(TOPIC_NAME));

    while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(10));
    records.forEach(KafkaConsumerApp::printRecord);
    }
}
```

Source of this section is available on [GitHub](https://github.com/ashimjk/apache-kafka-series/tree/master/kafka-client).

## Kafka Series
- [Part 1 - Kafka Key Terminology](../kafka-key-terms)
- [Part 2 - Kafka Partitions](../kafka-partitions)
- [Part 3 - Run a single instance of Apache Kafka](../kafka-single-instance)
- [Part 4 - Run a multiple instances of Apache Kafka](../kafka-multiple-instance)
- [Part 5 - Kafka Producer](../kafka-producer)
- [Part 6 - Kafka Consumer](../kafka-consumer)
- [Part 7 - Kafka Challenges](../kafka-challenges)
- [Part 9 - Producer/Consumer using Spring Kafka](../spring-kafka)