---
layout: default
title: Basic concepts
nav_order: 2
permalink: /docs/basic-concepts
---

# Basic Concepts

Following are basic concepts from distributed systems. They form the foundation needed to understand and use **Imperium**.

## Replicas/Nodes

Replicas are individual servers/processes that are connected to other replicas.

## Messages

Replicas communicate with each other by sending messages

## Timers

Stopwatches that are running at each replica. Timers can be set or reset.

## Distributed protocol

An algorithm that is run at every replica. The algorithm defines the rules of engagement between replicas. Protocols define

- Replies for a message
- Rules to set a timer
- Rules to broadcast a message
- ...

## Events

Events represent a computation at each replica. During the execution of a protocol, replicas go through a sequence of events. Events can be timer start, timer end, message received ...

## Faults

Faults can be of two types. Benign or byzantine,

**Benign**: Unintentional faults on the network or on individual replicas. A replica can crash/stop, stop responding to messages. Similarly, network can drop messages, delay messages or duplicate messages.

**Byzantine**: Intentional faults by replicas. Deviations from the protocol. Replicas can lie, send different messages to different replicas or collude with other byzantine replicas.
