---
title: Spring Integration
date: 2019-08-26 17:16:02 +0545
categories: [integration]
tags: [middleware]
---

## Introduction
Spring Integration (SI) is a framework enabling a collection of individual applications to
integrate together to deliver a business enterprise system. The framework is essentially a
lightweight messaging system that enables spring based applications to communicate with one
another and supports integration with external systems via declarative adapters. It is based
on the 'filters and pipes' design architecture. A key feature of it is that it achieves this
integration in a minimally intrusive way.


## Three main components:

### Messages
`Encapsulate the data to be transferred from one place to another`. They comprise of a `header`
and a `payload` (your data typically in the form of a POJO).

- Header (holds metadata)
    - message id
    - timestamp etc


### Channels
`Provide a mechanism to transport messages from one endpoint to another`. Represents the pipes
in the `pipes & filters architecture`. SI offers two types of channels, namely
Pollable and Subscribable Channels.

The former rely on consumers to periodically check for messages whereas
the latter is directly responsible for notifying registered consumers when messages become available.

### Endpoints
Consumer/Producer of messages. `Performs some action based on the payload`. Endpoints come in
various flavours, each performing a different function.

- Transformers (transform data)
- Routers (route data)
- Filters (filter data)
- Splitter (splits messages)
- Aggregator (aggregates group of messages into single message)
- Service Activator (connecting messages to Services)
- Channel Adapters (connect channels to external applications)

## Message Channel
A message channel is the component through which messages are moved so it can be thought as a pipe
between message producer and consumer. A Producer sends the message to a channel, and a consumer
receives the message from the channel. A Message Channel may follow either Point-to-Point or Publish/Subscribe
semantics. With a Point-to-Point channel, at most one consumer can receive each message sent to
the channel. With Publish/Subscribe channels, multiple subscribers can receive each Message sent
to the channel. Spring Integration supports both of these.

## Different channels
Direct channel and null-channel are used. Direct channel is the default channel type within Spring
Integration and simplest point-to-point channel option. Null Channel is a dummy message channel to
be used mainly for testing and debugging. It does not send the message from sender to receiver
but its send method always returns true and receive method returns null value. In addition to
DirectChannel and NullChannel, Spring Integration provides different Message Channel Implementations
such as PublishSubscribeChannel, QueueChannel, PriorityChannel, RendezvousChannel, ExecutorChannel and ScopedChannel.


## Message Endpoint
A message endpoint isolates application code from the infrastructure. In other words, it is an
abstraction layer between the application code and the messaging framework.

## Main Message Endpoints :

### Transformer
A Message Transformer is responsible for converting a Message’s content or structure and returning
the modified Message. For example

it may be used to transform message payload from one format to another or to modify message header values.

### Filter
A Message Filter determines whether the message should be passed to the message channel.

### Router
A Message Router decides what channel(s) should receive the Message next if it is available.

### Splitter
A Splitter breaks an incoming message into multiple messages and send them to the appropriate channel.

### Aggregator
An Aggregator combines multiple messages into a single message.

### Service Activator
A Service Activator is a generic endpoint for connecting a service instance to the messaging system.

### Channel Adapter
A Channel Adapter is an endpoint that connects a Message Channel to external system. Channel Adapters
may be either inbound or outbound.

#### Inbound Channel
An inbound Channel Adapter endpoint connects a external system to a MessageChannel.

#### Outbound Channel
An outbound Channel Adapter endpoint connects a MessageChannel to a external system.

### Messaging Gateway
A gateway is an entry point for the messaging system and hides the messaging API from external system.
It is bidirectional by covering request and reply channels.

Also Spring Integration provides various Channel Adapters and Messaging Gateways (for AMQP, File, Redis,
Gemfire, Http, Jdbc, JPA, JMS, RMI, Stream etc..) to support Message-based communication with external
systems. Please visit Spring Integration Reference documentation for the detailed information.
