# Introduction to Kafka

## What is Kafka?

Each minute, billions of transactions happen in systems like Paytm.
How does Paytm process those transactions using Kafka?
By sending a continuous stream of data from Paytm to Kafka servers, we
create a real-time stream of data for processing.

Kafka is a **distributed event streaming platform**.
We can also distribute Kafka servers across regions. For example, 3
Kafka servers may run in 3 different regions.
If one server goes down, another server will pick up the traffic to
avoid downtime.

## Why we need Kafka?

Example: A postman delivers a letter to a home. If I am not at home, I
may lose the letter.
Here, Kafka works like a **postbox**. Even if we are not available, the
postman leaves the letter in the postbox.
This ensures **no data loss**.

### Real-time Example:

-   App1 sends messages to App2.
-   If App2 is down and Kafka is not used, App2 will lose the data.
-   If Kafka is used, the message is stored until App2 is up, and then
    App2 consumes the message.

### Additional Benefits:

-   **Data Format**: Kafka itself doesn't enforce a format (it just
    transports bytes), but if all producers and consumers agree on a
    common serialization format (like Avro, JSON, or Protobuf), we
    reduce mismatches.
-   **Connection Type**: Without Kafka, producers must connect directly
    to each consumer. With Kafka, all apps just connect to Kafka
    brokers. This standardizes communication.
-   **Number of Connections**: Without Kafka → N×M direct connections.
    With Kafka → each app connects only to Kafka cluster, reducing
    connections and improving scalability.

## How does Kafka Work?

Kafka uses a **Pub/Sub Model**:
- **Publisher (Producer)** publishes messages/events to Kafka broker.
- **Subscriber (Consumer)** listens to Kafka broker and consumes
messages.

------------------------------------------------------------------------

# Kafka Architecture and Components

### Producer

The source of data that publishes messages to Kafka.

### Consumer

The receiver application that subscribes and consumes messages from
Kafka.

### Broker

A Kafka broker is a **server** that stores messages.
Producers publish messages to brokers, and consumers fetch messages from
brokers.

### Cluster

A group of brokers working together forms a Kafka cluster.
Usually, 3 brokers are configured for fault tolerance.

### Topic

Topics are like **tables** inside Kafka.
Example:
- *Booking Topic* → Booking-related messages
- *Payment Topic* → Payment-related messages
- *Insurance Topic* → Insurance-related messages

A topic is a category or classification of messages. Consumers subscribe
to the topics they need.

### Partitions

Kafka splits topics into partitions for scalability.
If 1 billion records arrive per second, a single machine cannot handle
it.\
So Kafka distributes data across partitions, often using
**round-robin**.

### Offset

Each message in a partition has a unique **sequence number** called an
offset.
Offsets help consumers track where they left off.
Example:
Partition 0 → messages with offsets [0,1,2,3,4,5,6\]
If a consumer reads up to offset 3 and goes down, when it comes back it
will continue from offset 4.

### Consumer Groups

If there are 3 partitions but only 1 consumer, performance will be
poor.
To increase throughput, we create a **consumer group** (multiple
consumers under one group name).
Kafka assigns partitions across consumers in the group.

Example:
- 3 partitions → 3 consumers → each consumer handles 1 partition.
- If a 4th consumer joins, it remains idle until one consumer goes
down.
- If a consumer fails, Kafka rebalances partitions automatically.

### Zookeeper

Zookeeper is used by Kafka for cluster coordination.
It tracks Kafka brokers, topics, partitions, and offsets.

------------------------------------------------------------------------

# Kafka Installation

-   **Open source**: https://kafka.apache.org/downloads
-   **Commercial distribution**: https://www.confluent.io/get-started/?product=self-managed Register and download
-   **Managed service**: Confluent Cloud, AWS MSK

### Tools

-   Kafka Offset Explorer (formerly Kafka Tool) for monitoring
	https://www.kafkatool.com/download.html

### Default Ports

-   **Zookeeper**: 2181
-   **Kafka Server**: 9092


## Open Source Kafka Startup in local ##

1. Start Zookeeper Server in Windows PowerShall

    ```.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties ```

2. Start Kafka Server / Broker

    ``` .\bin\windows\kafka-server-start.bat .\config\server.properties ```

3. Create topic

    ```kafka_2.12-3.9.1>bin\windows\kafka-topics.bat --bootstrap-server localhost:9092 --create --topic vmc-topic -partitions 3 --replication-factor 1 ```

4. list out all topic names

    ``` kafka_2.12-3.9.1>bin\windows\kafka-topics.bat --bootstrap-server localhost:9092 --list ```

5. Describe topics
  
    ``` kafka-topics.bat --bootstrap-server localhost:9092 --describe --topic vmc-topic ```

6. Produce message

    ```kafka_2.12-3.9.1>bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic vmc-topic```


7. consume message

    ``` kafka_2.12-3.9.1>bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic vmc-topic --from-beginning ```


