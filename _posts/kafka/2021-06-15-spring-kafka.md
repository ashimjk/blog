---
layout: post
title: Part 9 - Producer/Consumer using Spring Kafka
date: 2021-06-15 13:29 -0600
categories: kafka
tags: kafka-series
---

## Dependency
```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <version>2.7.2</version>
</dependency>
```

## Configuring KafkaAdmin
We need the `KafkaAdmin` bean for creating a new topic. It just delegates to an `AdminClient` for creating and describing the topic.
It looks for `NewTopic` bean in the application context during its initialization and wraps it inside
`NewTopics` list.

```java
@Bean
public KafkaAdmin kafkaAdmin() {
    Map<String, Object> configs = new HashMap<>();
    configs.put(AdminClientConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddresses);
    return new KafkaAdmin(configs);
}

@Bean
public NewTopic myTopic() {
    return new NewTopic("myTopic", 1, (short) 1);
}
```

## Configuring ProducerFactory and KafkaTemplate
We need the `ProducerFactory` bean for creating a producer. It is a factory class for creating `Producer`
instances. And default implementation is provided by `DefaultKafkaProducerFactory`.

We will also need the `KafkaTemplate` bean, for sending the messages. It provides a high-level operations
like, send the messages which are thread-safe, when used with `DefaultKafkaProducerFactory`.

```java
@Bean
public ProducerFactory<String, String> producerFactory() {
    Map<String, Object> configProps = new HashMap<>();
    configProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
    configProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
    configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
    return new DefaultKafkaProducerFactory<>(configProps);
}

@Bean
public KafkaTemplate<String, String> kafkaTemplate() {
    return new KafkaTemplate<>(producerFactory());
}
```

## Configuring ConsumerFactory and KafkaListenerContainerFactory
We need the `ConsumerFactory` bean for creating a consumer. It is a factory class for creating `Consumer`
instances. And default implementation is provided by `DefaultKafkaConsumerFactory`.

`ConcurrentKafkaListenerContainerFactory` is the subclass of `KafkaListenerContainerFactory` which looks for
`@KafkaListener` annotated methods and are added to the `KafkaListenerEndpointRegistry`.

When messages are received from Kafka Broker, `@KafkaListener` annotated methods are triggered.

```java
@Bean
public ConsumerFactory<String, String> consumerFactory() {
    Map<String, Object> props = new HashMap<>();
    props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
    props.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    return new DefaultKafkaConsumerFactory<>(props);
}

@Bean
public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() {
    ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();
    factory.setConsumerFactory(consumerFactory());
    return factory;
}
```

Note: `@EnableKafka` is required in the configuration class which enables detection of `KafkaListener` annotations
on any spring-managed bean in the container.

## How to run
I have implemented spring kafka using two different approaches:
- By manually configuring the required bean for kafka
- Using auto-configuration provided by spring-kafka

Although both of them are managed using the spring profile: [auto, manual], but does the same thing.

For implementation, please visit the [github](https://github.com/ashimjk/apache-kafka-series/tree/master/spring-kafka).

For Spring Cloud Stream with Kafka implementation, please visit the [github](https://github.com/ashimjk/apache-kafka-series/tree/master/cloud-stream-kafka) here.

## Reference
- [Spring for Apache Kafka Docs](https://spring.io/projects/spring-kafka)

## Kafka Series
- [Part 1 - Kafka Key Terminology](../kafka-key-terms)
- [Part 2 - Kafka Partitions](../kafka-partitions)
- [Part 3 - Run a single instance of Apache Kafka](../kafka-single-instance)
- [Part 4 - Run a multiple instances of Apache Kafka](../kafka-multiple-instance)
- [Part 5 - Kafka Producer](../kafka-producer)
- [Part 6 - Kafka Consumer](../kafka-consumer)
- [Part 7 - Kafka Challenges](../kafka-challenges)
- [Part 8 - Producer/Consumer using Kafka Client](../kafka-client)
