# Kafka Multi-Consumer Group Architecture

This repository documents the logic for scaling Kafka consumers 
using independent Consumer Groups while handling 7-day data retention.

## Architecture Overview

We utilize a single Kafka Topic with **3 Partitions**. This allows 
parallel processing where each consumer in a group handles 
exactly one partition.

### Current State
* **Producer:** Sending messages to 3 partitions.
* **Consumer Group A:** 3 consumers (1:1 mapping to partitions).

### Future State (Scaling)
Kafka supports **n number of independent consumer groups**. When 
**Consumer Group B** is introduced, it receives its own unique 
offsets. This allows it to read historical data without 
affecting Group A.

---

## Technical Q&A

**Question:** If a second consumer group with 3 consumers is added in 
the future, can Kafka handle this given a 7-day retention policy?

**Answer:** **Yes.** Kafka is designed for this "Publish-Subscribe" pattern.
1. **Independent Offsets:** Each group manages its own progress.
2. **Replayability:** Group B can start from the beginning of 
   the 7-day buffer to process historical data.
3. **Parallelism:** * Partition 0 → Group A (C1) AND Group B (C1)
   * Partition 1 → Group A (C2) AND Group B (C2)
   * Partition 2 → Group A (C3) AND Group B (C3)

---

## Configuration Requirements

To ensure a new group processes historical data (up to 7 days), 
apply these settings:

| Property | Value | Description |
| :--- | :--- | :--- |
| `group.id` | `consumer-group-2` | Unique ID for independent tracking. |
| `auto.offset.reset` | `earliest` | Starts from the oldest message. |

---

## Implementation Example (Java)

Formatted for narrow screens:

```java
Properties props = new Properties();

// Kafka Broker Address
props.put("bootstrap.servers", "localhost:9092");

// Unique Group ID
props.put("group.id", "consumer-group-2");

// Start from the beginning of the 7-day log
props.put("auto.offset.reset", "earliest");

props.put(
    "key.deserializer", 
    "org.apache.kafka.common.serialization.StringDeserializer"
);
props.put(
    "value.deserializer", 
    "org.apache.kafka.common.serialization.StringDeserializer"
);

KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
