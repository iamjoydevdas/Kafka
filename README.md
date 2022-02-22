I have collected the concepts from various books and github pages and collected it here.

# Kafka
Apache Kafka is publish-subscribe based fault tolerant messaging system. It is fast, scalable and distributed by design. 

## A real life problem
- You need a way to send data to a central storage quickly
- Because machines frequently fail, you also need the ability to have your data replicated, so those inevitable failures don't cause downtime and data loss

In this scenario, kafka can be saviour.

## Why use Kafka?
- Multiple producers and consumers at any given time without interfering with each other. This is in contrast to many queuing system where once a message is consumed by one client
- Disk-Based retention:
  - Consumers do not always need to work in real time. Messages are commited to disk and stay the for some period of time.
  - There is no danger of losing data.
- Fast: Kafka is a good solution for applications that require a high througput, low latency messaging solution. Kafka can write up to 2 million requests per second
- Scalable:
- Expansions can be performed while the cluster is online, with no impact on the availability of the system as a whole.
- High Performance: Excellent performance under high load.

## Why is Kafka fast?
- Zero Copy: Basically Kafka calls the OS kernal directly rather than at the application layer to move data fast.-
- Batch data in chunks: Kafka is all about batching the data into chunks. This minimises cross machine latency with all the buffering/copying that accompanies this.
- Avoids Random Disk Access: Kafka is designed to access the disk in sequential manner. This enables it to get similar speeds from a physical disk compared with memory.
- Can scale Horizontally: The ability to have thousands of partitions for a single topic spread among thousands of machines means Kafka can handle huge loads.
Compared to other message queue systems


## Use Cases
Activity tracking: The original use case for Kafka, designed at LinkedIn, is that of user activity tracking.

Messaging: wher applications need to send notifications to users. Those can produce messages without needing to be concerend about formatting. Then an other applicatoin can read all the messages and handle them consistently

Metrics and logging

Commit log: Database changes can be published to Kafka and applications can easily monitor this stream to receive live updates as they happen.

Stream processing: Kafka is extremely good for streaming and processing huge datasets.

## Kafka VS Other MQs
![image](https://user-images.githubusercontent.com/100063114/155071857-e29bbcec-8109-4c79-afbb-73ce4dd32a3b.png)


## How kafka works internally
Kafka is a message broker. A broker is an intermediary that brings together two parties that don't necessarily know each other for a mutually beneficicial exchange or deal.

**Log**
Is a file that Kafka appends incoming records to. A log is an append-only, totally ordered sequence of records ordered by time

![image](https://user-images.githubusercontent.com/100063114/155072172-c235d623-b15e-4cf0-80ad-984399b746a1.png)

Configuration setting log.dir, specifies where Kafka stores log data on disk.
![image](https://user-images.githubusercontent.com/100063114/155072218-89bb072a-02f6-41c1-a82d-65a07f49c8f0.png)


