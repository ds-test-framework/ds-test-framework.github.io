---
layout: default
title: Writing tests
parent: Unit testing
---

# Unit Tests

A unit test is represented by a *TestCase* object. Instantiate a `TestCase` object by specifying a `Name`, `Timeout` and a `HandlerCascade`

- `Name` is unit for a particular test scenario
- `Timeout` is the maximum duration after which the test is terminated
- `HandlerCascade` implements an interface for handling each event.

Additionally a `SetupFunc` can be specified which will can make initialization calls to the replicas. This can be used to prep the replicas for the test scenario being executed.

When running a unit test, **Imperium** waits for replicas to be ready and then invokes the handler for every event received from the replicas. Each invocation of `HandlerCascade` returns a list of `Message`s which are delivered to the intended replica. Refer [Data structures]({% link docs/framework/data_structs.md %}) for documentation about data structure definitions used in this document.

## Context

Handlers are invoked with an `Event` and a `Context` object which is meant for handlers to share information between successive calls. `Context` contains references to the `MessagePool`, `ReplicaStore`, `EventDAG` and `VarSet` which can be used when handling events.

- `MessagePool` contains a reference to the pool of messages received by **Imperium** indexed by Message ID
- `ReplicaStore` contains information about the replicas that are connected to **Imperium**. This can be used to fetch private key information when the handler intends to change a message.
- `EventDAG` contains a history of all the events that have been encountered as a Directed acyclic graph
- `VarSet` is a key value store that can be used to store information between success calls to the handlers.

## HandlerCascade

`HandlerCascade` allows you to specify a sequence of `HandlerFunc`s to handle events. Each `HandlerFunc` returns a list of messages and a boolean value. The boolean value should be `true` if the `HandlerFunc` handled the `Event` and `false` otherwise.

`HandlerCascade` should be invoked with a `StateMachine` object and has the following primitives,

- `HandleEvent(*Event, *Context) []*Message`: Invoked by `testlib` for every event that **Imperium** receives.
- `AddHandler(HandlerFunc)`: Adds a `HandlerFunc` to the cascade

The cascade works as follows. For each event, the sequence of `HandlerFunc` is called until it returns `true` in the second return value. There is a default `HandlerFunc` that handles events of type `MessageSend` and returns the corresponding `Message` referenced by the event. This means that when none of the `HandlerFunc`s return `true` the message is delivered. If you use `HandlerCascade` without specifying any handlers, then all messages are delivered by default.

Use `WithDefault` option when creating a `HandlerCascade` instance to specify a different default handlers.

Refer [State Machine]({% link docs/unit_testing/statemachine.md %}) for documentation related to specifying state machines
