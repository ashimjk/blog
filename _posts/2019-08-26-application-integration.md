---
title: Application Integration or Enterprise Application Integration
date: 2019-08-26 17:16:02 +0545
categories: [integration]
tags: [middleware]
---


It's the sharing of processes and data among different applications in an enterprise.

## Different levels of integration
- Presentation Level
- Business Process Level
- Data Level
- Communications Level

## Presentation Level
In this level, Integration is achieved by presenting several different applications
as a single application with a common user inteface (UI). Also known as `Screen Scraping`.

This older approach to integration involves using : middleware technology to collect the
information that a user enters into a web page or some other user interface.

## Business Process Level
The logical processes required by an enterprise to conduct its business are mapped onto its
IT assets, which often reside in different parts of the enterprise and increasingly, the cloud.
By identifying individual actions in a workflow and approaching their IT assets as a meta-system
(i.e. a system of systems), `enterprises can use applications integration to define how individual
applications will interact in order to automate crucial business processes`, resulting in the faster
delivery of goods and services to customers, reduced chances for human error, and lower
operational costs.

## Data Level
Aside from business process integration, data integration is also required for successful
applications integration. `If an application can’t exchange and understand data from another
application, inconsistencies can arise and business processes become less efficient`.
`Data integration is achieved by either writing code that enables each application to understand
data from other applications in the enterprise or by making use of an intermediate data format
that can be interpreted by both sender and receiver applications`. The latter approach is preferable
over the former since it scales better as enterprise systems grow in size and complexity.
In both cases, `access`, `interpretation`, and `data transformation` are important capabilities for
successfully integrating data.

## Communications Level
Underlying business process and data integration are communications-level integration. `This refers
to how different applications within an enterprise talk to each other, either through file transfer,
request/reply methods, or messaging`. In many cases, applications weren’t designed to communicate with
each other, requiring technologies for enabling such communication. These include
`Application Programming Interfaces (APIs)`, which specify how applications can be called, and connectors
that act as intermediaries between applications. At the communications level, it is also important to
consider the `architecture of interactions between applications`, which can be integrated according to
a `point-to-point model`, `hub-and-spoke approach`, or with an `Enterprise Service Bus (ESB)`.

## Synchronous vs. Asynchronous Communication
Without effective communication, business processes and data cannot be properly integrated. Depending on
an enterprise’s particular needs, communication can be either synchronous, asynchronous,
or some combination of the two.

In synchronous communication, a sender application sends a request to a receiver application and must wait
for a reply before it can continue with its processing. This pattern is typically used in scenarios where data
requests need to be coordinated in a sequential manner.

In asynchronous communication, a sender application sends a message to a receiver application and continues
its processing before receiving a response. In other words, the sender application does not depend on
the receiver application to complete its processing. If multiple applications are being integrated in such
a fashion, the sender application can complete its processing even if other subprocesses have not
finished processing.
