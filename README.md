
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


### Phase 1: One event is created
```mermaid
flowchart LR
    Order[Order Service]:::producer -->|order_placed| Platform[Event Platform]:::kafka

    classDef producer fill:#FFD700,color:#111,stroke:#333;
    classDef kafka fill:#87CEFA,color:#111,stroke:#333;
```

---

### Phase 2: Event flow initiated
```mermaid
flowchart LR
    Order[Order Service]:::producer --> Platform[Event Platform]:::kafka
    Platform --> Email[Email Service]:::consumer

    classDef producer fill:#FFD700,color:#111,stroke:#333;
    classDef consumer fill:#90EE90,color:#111,stroke:#333;
    classDef kafka fill:#87CEFA,color:#111,stroke:#333;
```

---

### Phase 3: Multiple consumers receive the same event
```mermaid
flowchart LR
    Order[Order Service]:::producer --> Platform[Event Platform]:::kafka
    Platform --> Email[Email Service]:::consumer
    Platform --> Warehouse[Warehouse Service]:::consumer

    classDef producer fill:#FFD700,color:#111,stroke:#333;
    classDef consumer fill:#90EE90,color:#111,stroke:#333;
    classDef kafka fill:#87CEFA,color:#111,stroke:#333;
```

---

### Phase 4: Kafka stores messages in topics
```mermaid
flowchart TB
    Kafka[Kafka]:::kafka --> Topic[orders topic]:::topic
    Topic --> Msg[order_placed 1]:::message

    classDef kafka fill:#87CEFA,color:#111,stroke:#333;
    classDef topic fill:#DDA0DD,color:#111,stroke:#333;
    classDef message fill:#FFA07A,color:#111,stroke:#333;
```

---

### Phase 5: Topic with offsets
```mermaid
flowchart TB
    Topic[orders topic]:::topic --> M0[0: order_placed 1]:::message
    Topic --> M1[1: order_placed 2]:::message
    Topic --> M2[2: order_placed 3]:::message

    classDef topic fill:#DDA0DD,color:#111,stroke:#333;
    classDef message fill:#FFA07A,color:#111,stroke:#333;
```

---

### Phase 6: Consumers with IDs
```mermaid
flowchart TB
    Topic[orders topic]:::topic --> M0[0: order_placed 1]:::message
    M0 --> Email[email_consumer]:::consumer
    M0 --> Warehouse[warehouse_consumer]:::consumer

    classDef topic fill:#DDA0DD,color:#111,stroke:#333;
    classDef message fill:#FFA07A,color:#111,stroke:#333;
    classDef consumer fill:#90EE90,color:#111,stroke:#333;
```

---

### Phase 7: Polling new messages
```mermaid
sequenceDiagram
    participant Email as email_consumer
    participant Kafka as Kafka
    Email->>Kafka: Poll for new messages
    Kafka-->>Email: order_placed 1
```

---

### Phase 8: Commit offset
```mermaid
sequenceDiagram
    participant Email as email_consumer
    participant Kafka as Kafka
    Email->>Kafka: Commit offset 1
    Kafka-->>Email: Ok
```

---

### Phase 9: Consume multiple messages

```mermaid
flowchart TB
    Topic[orders topic]:::topic --> M0[0: order_placed 1]:::message
    Topic --> M1[1: order_placed 2]:::message
    Email[email_consumer]:::consumer --> M0
    Email --> M1

    classDef topic fill:#DDA0DD,color:#111,stroke:#333;
    classDef message fill:#FFA07A,color:#111,stroke:#333;
    classDef consumer fill:#90EE90,color:#111,stroke:#333;
```

## Events as Messages
When we pass an event from one place to another, it is a message.
Kafka not only facilitates the storage of events, but also the passing of events from
one place to another.

Therefore, Kafka uses the term “messages” for events.

# How Does Kafka Facilitate Event Streaming?
Kafka stores durable and scalable logs of messages.
- It allows applications to produce messages to the log.
- It allows applications to consume messages from the log.
- It allows the messages to be stored for as long as required

## Kafka Topic with Message Offsets
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
