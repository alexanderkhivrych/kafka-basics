
# Kafka Basics

**What is Kafka?**
Kafka is a system designed for processing real-time data streams.

What is an Event?
An event is a piece of information captured at a specific moment.
Examples include:
- A record of a product being bought, saved in JSON format.
- A full snapshot of a database entry after an update (CDC).
- A simple log message from an app.

## Understanding Event Streaming?
Event streaming means constantly sending out and receiving events as they happen — like a real-time flow of updates between services or systems.

```mermaid
flowchart LR
    OrderService[Order Service]
    EmailService[Email Service]
    WarehouseService[Warehouse Service]
    ESP[Event Streaming Platform]

    OrderService -->|order_placed| ESP
    ESP --> EmailService
    ESP --> WarehouseService
```


## Events as Messages

When an event is passed from one place to another, it is a message.  
Kafka not only facilitates the storage of events but also the passing of events between services.  
*Terminology note:* In Kafka, events are called “messages.”


## How Does Kafka Facilitate Event Streaming?

Kafka enables event streaming by:

- Storing durable and scalable logs of messages.
- Allowing applications to produce messages to the log.
- Allowing applications to consume messages from the log.
- Permitting messages to be stored for as long as required.

## Event Streaming in Kafka (Topics and Offsets)

Kafka stores logs of messages in what are called topics. For example, the "orders topic" holds messages with sequential offsets. One diagram on this page shows a message at offset 0 (with subsequent offsets 1, 2, etc.).

```mermaid
flowchart TD
    Producer[Order Service]
    Topic[Kafka: orders topic]
    Message0[Message - offset 0]
    Message1[Message - offset 1]
    Message2[Message - offset 2]
    
    Producer -->|order_placed| Topic
    Topic --> Message0
    Message0 --> Message1
    Message1 --> Message2
```


```mermaid
flowchart LR
    Order[Order Service]
    ESP[Event Streaming Platform]
    Email[Email Service]
    Warehouse[Warehouse Service]
    
    Order -->|order_placed| ESP
    ESP -->|order_placed| Email
    ESP -->|order_placed| Warehouse
```

## Event Streaming in Kafka (Consumers with IDs)

Kafka tracks which offset a given consumer has processed. In this diagram, each consumer (with an associated ID) consumes messages from the topic.

```mermaid
flowchart LR
    Producer[Order Service]
    Topic[Kafka: orders topic]
    Consumer1[Email Service email_consumer]
    Consumer2[Warehouse Service warehouse_consumer]
    
    Producer -->|order_placed| Topic
    Topic --> Consumer1
    Topic --> Consumer2
```

## Event Streaming in Kafka (Polling)

Consumers poll Kafka for new messages. The diagram (similar to previous ones) indicates that a consumer repeatedly checks the topic, receives new messages, and then processes them.

```mermaid
flowchart LR
    PollingConsumer[Consumer Polling]
    Topic[Kafka: orders topic]
    NewMsg[New messages?]
    
    PollingConsumer --> Topic
    Topic --> NewMsg
```


## Event Streaming in Kafka (Committing Offsets)

After consuming messages, consumers tell Kafka to update (or commit) the offset. This ensures Kafka knows which messages have been processed.

```mermaid
flowchart LR
    Consumer[Consumer]
    Topic[Kafka: orders topic]
    Commit[Commit Offset]
    
    Consumer --> Topic
    Consumer -- commits --> Commit
```

## Event Streaming in Kafka (Multiple Events)

Consumers can process multiple messages per poll. This page illustrates sequential consumption of several messages.

```mermaid
flowchart TD
    subgraph Topic[Kafka: orders topic]
        M1[Message 1]
        M2[Message 2]
    end
    Consumer[Consumer]
    
    Consumer -->|poll| M1
    Consumer -->|poll| M2
```

## Durable and Scalable

Kafka’s design ensures that:
- **Durable:** Stored messages are resilient to failures.
- **Scalable:** The system can handle millions of messages per second and will not lose data even if a machine fails.



## Kafka Clusters

Kafka is designed to run as a cluster—a group of machines that coordinate with one another.

```mermaid
graph LR
    Cluster[(Kafka Cluster)]
    Node0[Node 0]
    Node1[Node 1]
    Node2[Node 2]
    
    Cluster --> Node0
    Cluster --> Node1
    Cluster --> Node2
```


## Partitions

Every topic in Kafka consists of one or more partitions. This enables the topic’s data to be split and distributed across different nodes.
Partitions allow a topic to be split between multiple nodes in a Kafka cluster.


## Partitions (Scalability)
- Messages sent to a topic are distributed across partitions.
- Each message is assigned to a partition based on its key — this process is deterministic.
- If multiple messages have the same key, they will always go to the same partition.
- This ensures that message ordering is preserved within a partition.
- Partitions allow Kafka to scale:
  - They distribute load across multiple nodes.
  - Consumers can be parallelized — one per partition, for better throughput.
  - 

```mermaid
flowchart LR
    Node0[Node 0]
    Node1[Node 1]
    Node2[Node 2]
    Part0[orders topic - partition 0]
    Part1[orders topic - partition 1]
    Part2[orders topic - partition 2]
    
    Node0 --> Part1
    Node1 --> Part0
    Node2 --> Part2
```

## Partitions (Consumer Parallelism)

```mermaid
flowchart LR
    Part1[Partition 1]
    Part2[Partition 2]
    Part0[Partition 0]
    ConsumerA[Email Service Instance 0]
    ConsumerB[Email Service Instance 1]
    
    Part1 --> ConsumerA
    Part2 --> ConsumerA
    Part0 --> ConsumerB
```


## Partitions: Durability and Replication

- Each partition is replicated across multiple nodes in the Kafka cluster.
- This replication provides fault tolerance.
- If a node goes down, Kafka can continue operating using the replicas on other nodes

```mermaid
flowchart LR
    Node0[Node 0]
    Node1[Node 1]
    Node2[Node 2]
    
    P1r0[orders topic - partition 1, replica 0]
    P2r1[orders topic - partition 2, replica 1]
    P0r0[orders topic - partition 0, replica 0]
    P1r1[orders topic - partition 1, replica 1]
    P0r1[orders topic - partition 0, replica 1]
    P2r0[orders topic - partition 2, replica 0]
    
    Node0 --> P1r0
    Node0 --> P2r1
    Node1 --> P0r0
    Node1 --> P1r1
    Node2 --> P0r1
    Node2 --> P2r0
```
