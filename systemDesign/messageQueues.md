# Messaging system

## Why message queue?

- Decoupling & Asynchronous Processing: Producers and consumers (different parts of an application) operate independently, improving flexibility. A service sends a message and moves on, rather than waiting for processing, boosting system responsiveness.

- Reliability & Fault Tolerance: Messages are durable and stored until processed, preventing data loss if a service crashes. Queues handle retries and ensure delivery.

- Scalability & Load Balancing: Easily scale by adding more consumers to process messages from the queue, distributing work and handling traffic surges effectively.

- Throttling & Buffering: Act as a buffer, smoothing out peaks in demand, preventing consumers from being overwhelmed by sudden spikes in requests.

- Background Processing: Offload time-consuming tasks (like video transcoding, report generation, or sending bulk emails) to background workers, keeping user-facing applications fast.

- Clearer Architecture: Define clear boundaries between microservices, making systems easier to develop, test, and maintain. 

## Key points
- Kafka vs AWS SQS vs Redis+Celery vs RabbitMQ vs Redis Stream

- stream processing (Flink)
- Batch processing (spark)

- Event loop (python and nodejs's event loop?)

- Consuming message: Push vs Pull
    - Push: the server sends messages to subscribers once it is available
    - Pull: the client pull messages from server. Http long polling can be used here to improve performance.


## Questions to answer about a message queue:

1. How does it make sure messages are not lost?

1. At least once or at most once?

1. How does it distribute messages?
Multiple worker consume same queue to distribute the load?
Fan out messages: each consumer receive replica of messages?

## Kafka

### References
[Official documentation](https://kafka.apache.org/41/getting-started/introduction/)

### Questions:
1. How Kafka handle offset?
    
    [Link](#kafka-consumer-offset)

2. Kafka is disk based? How does it make it fast?

    [Link](#kafka-is-disk-based---link)

3. Zookeeper used in Kafka?

    [Link](#zookeeper-in-kafka)

4. Pull or push model?

    [Link](#push-vs-pull-model)

5. Livesite issues encountered with Kafka clusters
    - Hard to manage SSL certificates in master and worker nodes. Need a fully managed cluster
    - Need to have separate Zookeeper clusters for older version Kafka clusters
    - No built-in dead letter queue (DLQ).

### Concepts:
- **Event**: somethign happened. An event has a key, value, timestamp, and optional metadata. Kafka is a event queue

- **Producer** and **Consumer**: Producers are those client applications that publish (write) events to Kafka, and consumers are those that subscribe to (read and process) these events.

- **Topics**: Events are stored in topics. Topics are like folders and events are files in the folder. Events in a topic can be read as often as needed—unlike traditional messaging systems, events are not deleted after consumption

- **Broker**: Some of these servers from the storage layer, called the brokers. (Kafka server side)

- **Partition**: Topics are partitioned, meaning a topic is spread over a number of "buckets" located on different Kafka brokers. Events with the same event key (e.g., a customer or vehicle ID) are written to the same partition, and Kafka guarantees that any consumer of a given topic-partition will always read that partition's events in exactly the same order as they were written.

    **Event order is only guaranteed in partition level.**

- **consumer group**: In Kafka, a consumer group is a set of consumers from the same application that work together to consume and process messages from one or more topics. Remember that each topic is divided into a set of ordered partitions. Each partition is consumed by **exactly one** consumer within each consumer group at any given time.

### Kafka is disk based - [link](https://docs.confluent.io/kafka/design/file-system-constant-time.html)
Disk read/write are much faster for linear read/write, comparing to random read/write. Instead of keeping data in memory and flush to disk when running out of space, Kafka immediately write data to a persistent log on the filesystem without necessarily flushing to disk. In effect this just means that it is transferred into the kernel's pagecache.

### Kafka consumer offset:
References:
https://kafka.apache.org/documentation/#theconsumer
https://kafka.apache.org/22/javadoc/org/apache/kafka/clients/consumer/KafkaConsumer.html

Consumer needs to commit the offset (committed position), which will be used to tell where the consumer should recover to once it is getting restarted. The consumer application need not use Kafka's built-in offset storage, it can store offsets in a store of its own choosing.

Kafka does not have explicit retry logic: when a consumer fails to process a message, it does not commit the offset within the timeout. The message is not considered consumed and will be delivered to next consumer when it reconnects to the partition

### Push vs pull model
Kafka queue use pull stratgy (consumer pull messages from server), reference: https://docs.confluent.io/kafka/design/consumer-design.html#push-versus-pull-design



### Kafka partition rebalancing
Rebalancing comes into play in Kafka when consumers join or leave a consumer group. In either case, there is a different number of consumers over which to distribute the partitions from the topic(s), and, so, they must be redistributed and rebalanced.

[Useful video](https://www.youtube.com/watch?v=ovdSOIXSyzI&t=160s)

- Kafka group leader (one of the consumer in the consumer group) is elected by Kafka group coordinator (one Kafka broker)
- Kafka group leader is responsible for calculating the partition assignment, rather than the broker (group coordinator). It makes the partition assignment pluggable (customizable by user, since some consumer may be more powerful than others)

#### Kafka rebalancing logic

[Reference](https://www.confluent.io/blog/debug-apache-kafka-pt-3/#:~:text=Rebalancing%20comes%20into%20play%20in,must%20be%20redistributed%20and%20rebalanced)

- Stop the world rebalance

    It is like every consumer should stop the work (being revoked the partition), send join group request, and only start work when the partition assignment is complete.

    - Issues:
        - consumers need to wrap up their work and if they are assigned to the same partition as before, then the wrap up work is wasted.
        - Work has been completely stoped during the rebalancing phrase. Sometimes the rebalancing takes longer when all consumers are restarted in scenarios like an update. In this case, multiple join group request can be sent and takes longer to complete the rebalancing


- Incremental Cooperative rebalance
    
    Instead of revoking all partition assignment for all consumers, they continue to work. And only the changed partition assignment is sent back to consumers. If consumers have same partition assignment as before, the process is still going.

- Static group membership (avoid rebalancing)

    Assign a static group member id to each consumer in the group. If a consumer is restarted, the rebalance won't happen unless the session is timed out.


### Zookeeper in Kafka
Zookeepr is used to manage metadata in Kafka.

ZooKeeper provides several fundamental services for distributed systems, including primary server election, group membership management, configuration information storage, naming, and synchronization at scale. Its primary aim is to make distributed systems like Kafka more straightforward to operate by providing improved and reliable change propagation between replicas in the system. [references](https://github.com/AutoMQ/automq/wiki/What-is-the-Zookeeper-in-Kafka-All-You-Need-to-Know)

Zoopkeeper is not involved in the message processing part in the broker (more like a control plane).

Kafka community is moving to KRaft from Zookeeper. Starting from Kafka version 4.0, Zookeeper is removed and KRaft is the exclusive protocol.

- Zookeeper vs KRaft performance comparison

    [Partitions and Data Performance, part 1](https://www.instaclustr.com/blog/apache-kafka-kraft-abandons-the-zookeeper-part-1-partitions-and-data-performance/)
    
    [Partitions and Data Performance, part 2](https://www.instaclustr.com/blog/apache-kafka-kraft-abandons-the-zookeeper-part-2-partitions-and-meta-data-performance/)



## RabbitMQ

## Redis Stream
- Reference: https://redis.io/docs/latest/develop/data-types/streams/

- Each entry (or message) is identified by an ID that is generated by Redis by default. The [entry id](https://redis.io/docs/latest/develop/data-types/streams/#entry-ids) format is <millisecondsTime>-<sequenceNumber>

- Adding an entry to a stream is O(1). Accessing any single entry is O(n), where n is the length of the ID. Since stream IDs are typically short and of a fixed length, this effectively reduces to a constant time lookup. For details on why, note that streams are implemented as **radix trees**.


### Redis stream support 3 types of [queries](https://redis.io/docs/latest/develop/data-types/streams/#getting-data-from-streams): querying by range, listening for new items, consumer groups
- Query by range:
XRANGE complexity is O(log(N)) to seek, and then O(M) to return M elements

- Listen for new item: XREAD, it replicates messages to all consumers (fan-out)
A stream can have multiple clients (consumers) waiting for data. Every new item, by default, will be delivered to every consumer that is waiting for data in a given stream. This behavior is different than blocking lists, where each consumer will get a different element. However, the ability to fan out to multiple consumers is similar to Pub/Sub.

- Consumer group: it is completely different from the concept of Kafka consumer group.

    In a single Redis stream, multi coonsumers in a consumer group can receive message out of order. To achieve Kafka partitions, you have to use multiple keys and some sharding system such as Redis Cluster or some other application-specific sharding system.

    Basically Kafka partitions are more similar to using N different Redis keys, while Redis consumer groups are a server-side load balancing system of messages from a given stream to N different consumers.