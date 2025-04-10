
# Kafka Basics

**What is Kafka?**
Kafka is a system designed for processing real-time data streams.

What is an Event?
An event is a piece of information captured at a specific moment.
Examples include:
- A record of a product being bought, saved in JSON format.
- A full snapshot of a database entry after an update (CDC).
- A simple log message from an app.

## 1. Understanding Event Streaming?
Event streaming means constantly sending out and receiving events as they happen — like a real-time flow of updates between services or systems.

```mermaid
flowchart LR
    A[Order Service]:::producer -->|order_placed| K[Event Streaming Platform]:::kafka
    B[Warehouse Service]:::consumer --> K
    K --> C[Email Service]:::consumer
    K --> B

    classDef producer fill:#FFD700,stroke:#333,stroke-width:1px;
    classDef consumer fill:#90EE90,stroke:#333,stroke-width:1px;
    classDef kafka fill:#87CEFA,stroke:#333,stroke-width:1px;
```

## 2. Kafka Topic with Message Offsets
```mermaid
flowchart LR
    subgraph Kafka
        T[orders topic]:::topic
        T -->|0| M1[order_placed 1]:::message
        T -->|1| M2[order_placed 2]:::message
    end
    A[Order Service]:::producer --> T
    B[Warehouse Service]:::producer --> T
    T --> C[Email Service]:::consumer

    classDef producer fill:#FFD700,stroke:#333;
    classDef consumer fill:#90EE90,stroke:#333;
    classDef topic fill:#DDA0DD,stroke:#333;
    classDef message fill:#FFA07A,stroke:#333;
```

## 3. Kafka Cluster with Partitions
```mermaid
flowchart TB
    subgraph Kafka Cluster
        N0[Node 0]:::node --> P0[Partition 0]:::partition
        N1[Node 1]:::node --> P1[Partition 1]:::partition
        N2[Node 2]:::node --> P2[Partition 2]:::partition
    end
    A[Order Service]:::producer --> P0
    A --> P1
    A --> P2

    classDef node fill:#E6E6FA,stroke:#333;
    classDef partition fill:#F5DEB3,stroke:#333;
    classDef producer fill:#FFD700,stroke:#333;
```

## 4. Consumer Polling and Offset Commit
```mermaid
sequenceDiagram
    participant EC as EmailConsumer
    participant K as Kafka
    EC->>K: Poll for new messages
    K-->>EC: order_placed 1, 2
    EC->>K: Commit offset 2
```

## 5. Replication for Durability
```mermaid
flowchart TD
    subgraph Kafka Cluster
        N0[Node 0]:::node
        N1[Node 1]:::node
        N2[Node 2]:::node
    end

    N0 -->|Partition 1 - Replica 0| P1R0:::replica
    N0 -->|Partition 2 - Replica 1| P2R1:::replica
    N1 -->|Partition 0 - Replica 0| P0R0:::replica
    N1 -->|Partition 1 - Replica 1| P1R1:::replica
    N2 -->|Partition 0 - Replica 1| P0R1:::replica
    N2 -->|Partition 2 - Replica 0| P2R0:::replica

    classDef node fill:#ADD8E6,stroke:#333;
    classDef replica fill:#FFE4B5,stroke:#333;
```
