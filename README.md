# Kafka
Apache Kafka is publish-subscribe based fault tolerant messaging system. It is fast, scalable and distributed by design.

## Why use Kafka?
- Multiple producers and consumers at any given time without interfering with each other. This is in contrast to many queuing system where once a message is consumed by one client
- Disk-Based retention:
  - Consumers do not always need to work in real time. Messages are commited to disk and stay the for some period of time.
  - There is no danger of losing data.
- Fast: Kafka is a good solution for applications that require a high througput, low latency messaging solution. Kafka can write up to 2 million requests per second
- Scalable:
- Expansions can be performed while the cluster is online, with no impact on the availability of the system as a whole.
- High Performance: Excellent performance under high load.

## How kafka works internally
