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


## Kafka Internal Architecture
Kafka is a message broker. A broker is an intermediary that brings together two parties that don't necessarily know each other for a mutually beneficicial exchange or deal.

### Log
Is a file that Kafka appends incoming records to. A log is an append-only, totally ordered sequence of records ordered by time

![image](https://user-images.githubusercontent.com/100063114/155072172-c235d623-b15e-4cf0-80ad-984399b746a1.png)

Configuration setting log.dir, specifies where Kafka stores log data on disk.
![image](https://user-images.githubusercontent.com/100063114/155072218-89bb072a-02f6-41c1-a82d-65a07f49c8f0.png)


## Topics
Topics are logs that are seperated by topic name. Thinks topics as labeled logs. The closest analogies for a topic are a database table or a folder in a filesystem.
ALso, it can be treated as RDBMS table without any constrains.

Topic name examples:

orders
customers
paymments
To help manage the load of messages coming into a topic. Kafka use partitions

Topics are broken down into a number of **partitions.**

**Partitions are the way that Kafka provides redundancy and scalability.** Each partition can be hosted on a different server, which means that **a single topic can be scaled horizontally across multiple servers.**
![image](https://user-images.githubusercontent.com/100063114/155073260-740260cd-6363-4fa3-94f0-4e76ec0c7c08.png)

## Partitions
- Help increasing throughput
- Allows topic messages to be spread across several machines so that the capacity of a given topic isn't limited to the availble disk space one one server
![image](https://user-images.githubusercontent.com/100063114/155073310-aa0b03e5-02b0-4667-b866-4f0fcec280fc.png)

### Difference between Partition and Log?
At this time, you can come up with a question. Wait a minute, Aren't Log and Partition the same thing? At first glance, they seems to look the same, but here are the difference:

**Log:** physical part of a topic, where a topic is stored on the disk.
**Partition:** logical unit used to break down a topic into splits for redundancy and scalability. You can see Log stored on disk. But with Partition, you can't. Partition is handled logically.

### Important characteristics of Kafka
- Kafka stores and retrives message from topic. Doesn't keep any state of producers or consumers

- Messsages are written into Kafka in batches. A batch is just a collection of messages, all of which are being produced to the same topic and partition.

 
## Producers and Consumers
### Producer
**Producers create new messages.** In other publish/subscribe systems, these may be called publishers or writers. A message will be produced to a specific topic.

The producer does not care what partition a specifict message is written to and will balance messages over all partitions of a topic evenyly
In some case, the producer will direct messages to specific partitions using message key. Messages with a specified message key will be ensured to come in the right order in a partition.

![image](https://user-images.githubusercontent.com/100063114/155073725-3f5c4800-74a9-41b1-ab5a-fdf493eecdc6.png)

### Consumer
**Consumers read messages.** In other publish/subscribe systems, these may be called subscribers or readers.

The consumer subscribes to one or more topics and reads the messages in the order in which they were produced.
The consumer keeps track of which message it has already consumed by keeping track of the offset of messages.
The offset is a simple integer number that is used by Kafka to maintain the current position of a consumer.
![image](https://user-images.githubusercontent.com/100063114/155073838-c88b84f4-e452-4015-920d-0d3ac6f77dfa.png)

**Consumers work as part of a consumer group, which is one or more consumers that work together to consume a topic.** Group assures that each each partition is only consumed by one member. If a single consumer fails, the remaning members of group will rebalance the partitions being consumed to take over the missing member.

### Consumer group
Consumers groups used to read and process data in parallel.

### How consumers can read data in parallel without duplicate reads? Kafka provide a simple solution for this problem.

- A partition can only be consumed by one consumer at a time.
- But a consumer can consumer multiple partitions parallelly.
![image](https://user-images.githubusercontent.com/100063114/155074195-a59f7346-d061-4265-a678-2e4d3dc82ca5.png)
![image](https://user-images.githubusercontent.com/100063114/155074216-f6e09b7f-607d-4254-b6b5-578befe0d346.png)
![image](https://user-images.githubusercontent.com/100063114/155074229-0573d8ea-66a0-4be3-b56e-4a8aecbc7cf4.png)
![image](https://user-images.githubusercontent.com/100063114/155074244-5c876122-ae5b-43c9-af02-79dd3f0955c1.png)

### Flow of sending a message
- Create a ProducerRecord, which must include the topic we want to send the record to and a value. Optionally, we can also specify a key and/or a partition.
- Then Serialized the key and value objects to ByteArrays so they can be sent over the network
- Data is sent to a partitioner. The partition check if ProducerRecord has a specifed partition option. If yes, it doesn't do anything an reply the partition we specify. If not, the partitioner will choose a partition for us.
- Once a partition is selected, the producer then add the record to a batch of records that will also be sent to the same topic and partition.
- When broker receives the messages, it sends back a response.
  - If the messages were successfully writtent to Kafka, return a RecordMetatData object contains <topic, partition, offset>
  - If failed, the broker will return an error. The producer may retry sending the message a few more times before giving up and returning an error.

![image](https://user-images.githubusercontent.com/100063114/155076057-3e53de3c-5ff7-4164-9457-d16f84e54be0.png)

## Broker and Clusters
### Broker
**A single Kafka server is called a broker.** The broker receives messages from producers, assigns offsets to them and commits the messages to storage on disk.

Brokers are desigined to operate as part of a **cluster.**

### Cluster membership management with **Zookeeper**
Kafka uses Apache Zookeeper to maintain the list of brokers that are currently members of a cluster. ZooKeeper is a consistent file system for configuration information.

It acts as a centralized service and helps to keep track of the Kafka cluster nodes status, Kafka topics, and partitions.

### Cluster controller
In a cluster, one broker will also function as the cluster controller

A cluster controller is one of the kafka brokers that in addition to the usual broker functionality:

- administrative operations: assigning partitions to brokers and monitoring for broker failures
electing partition leaders(explained in the next section)
- Cluster only have one controller at a time
- The first broker that starts in the cluster becomes the controller.

### Replica
Replication is at the heart of Kafka's architecture. It guarantees availability and durability when individual nodes inevitably fail.

Each broker holds a number of partitions and each of these partitions can be either a leader or a replica for a topic

There are two types of replica:

### Leader replica
- Each partition has a single replica designated as the leader.
- All produce and consume requests go through the leader, in order to guarantee consistency.
- Each partition also have a prefered leader, the replica that was the leader when the topic is originally created.
### Follower replica
- All replicas for a partition that are not leaders are called followers
- Followers don't serve client requests
- Only replicate messages from the leader and stay up-to-date with the most recent message the leader has
- When a leader crashes, one of follower replica will be promoted to become the leader
- A Follower replica that catch up with the most recent messages of the leader are callled In-Sync replica
- Only in-sync replicas are eligible to be elected as partition leader in case the existing leader fail

### Partitions count, replication factor
The two most important parameters when creating a topic: Patition and replication factor They impact performance and durability of the system overall

## Patitions
- Each partition can handle a throughput of a few MB/s
- More patitions implies:
  - Better parallelism, better throughput
  - Ability to run more consumers in a group to scale
  - Ability to leverage more brokers if you have a large cluster
  - BUT more elections to perform for Zookeeper
  - BUT more files opened on Kafka
## Guidelines:

### Patitions per topic = MILLION DOLAR QUESTION
- Small cluster(<6 brokers>): #partitions per topic = 2 x number of brokers
- Big cluster(>12 brokers): 1 x # of brokers

### Replication: should be at least 2, usually 3, maximum 4
The higher the replication factor(N):

 - Better resilience of your system(N-1 brokers can fail)
 - But more replication (higher latency if acks=all)

### Paritions and Segments
- Partitions are made of ...segments(files) 
- Active segment means the segment are still being written to
- log.segment.bytes: the max side of a single segment in bytes
- log.segment.ms: the time Kafka will wait before comming the segment if not full
![image](https://user-images.githubusercontent.com/100063114/155077474-53272bc2-3659-462f-b5f6-bb11e78dadef.png)

### Kafka broker discovery
- Every Kafka broker is also called a "boostrap server"
- You only need to connect to one broker and you will be connected to the entire cluster
- Each broker knows about all brokers, topics and patitions

## Configure consumer
### How kafka handle consumers exit/enter groups?
![image](https://user-images.githubusercontent.com/100063114/155077880-593bb845-0af0-4252-bf72-c55851c253cc.png)
![image](https://user-images.githubusercontent.com/100063114/155077909-7843b902-e267-43ba-8fa0-2f2f1179f35f.png)

That's the job of a **group coordinator**
One of the kafka brokers get elected as a group coordinator. When a consumer want to join a group, it send a request to the group coordinator

The first consumer participating in a group is called a group leader.

During the rebalance activity, none of the consumers are allowed to read any messages.

rebalance: When a new consumer joins a consumer group the set of consumers attempt to "rebalance" the load to assign partitions to each consumer.

## Consumer offset
Kafka stores the offset at which a consumer group has been reading
The offsets are commited in a Kafka topic named __consumer__offsets
If a consumer dies, it will be able to read back from where it left off thanks to the commited consumer offset

