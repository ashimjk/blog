---
title: Stream Processing
date: 2019-09-04 12:02:16 +0545
categories: [technology]
tags: [middleware]
---

It isn't enough to just read, write, and store streams of data,
the purpose is to enable real-time processing of streams.

In a stream processor is anything that takes continual streams of data
from input topics, performs some processing on this input, and produces
continual streams of data to output topics.

For example, a retail application might take in input streams of sales and
shipments, and output a stream of reorders and price adjustments computed off this data.

It is possible to do simple processing directly using the producer and consumer APIs.

## Kafka Stream Processing

For more complex transformations Kafka provides a fully integrated Streams API.
This allows building applications that do non-trivial processing that compute
aggregations off of streams or join streams together.

This facility helps solve the hard problems this type of application faces:

- handling out-of-order data
- reprocessing input as code changes
- performing stateful computations, etc.

The streams API builds on the core primitives Kafka provides:

- it uses the producer and consumer APIs for input
- uses Kafka for stateful storage
- uses the same group mechanism for fault tolerance among the stream processor instances
