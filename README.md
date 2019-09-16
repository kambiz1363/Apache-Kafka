# Apache-Kafka
### What Is Kafka?
It is generally used as a publish/subscribe messaging system, It allows us to publish and subscribe to a stream of records that can be categorized. Kafka is written in Java. It is often used in real-time streaming data architectures to provide real-time analytics.<p> Since Kafka is a fast, scalable, durable, and *fault-tolerant publish-subscribe messaging system*.</p>
### kafka architecture
Kafka has four core APIs:

Producer API: This API enables the source or sender system to send data to the topics in Kafka cluster.

Consumer API: This API enables the receiving or consuming application to consume the data from Kafka cluster.

Streams API: This API enables transformation of incoming data; transformation may be simple mapping, filtering, aggregation etc.

Connector API: allows building and running reusable producers or consumers that connect Kafka topics to existing applications or data systems. For example, a connector to a relational database might capture every change to a table.


kafka consists of cluster, storage, log and so producer and consumer data. It is distributed store, receives and send records on different nodes that called *brokers*.  Brokers receive records from producers, assigns offsets to them, and commits them to storage. For this reason, it needed *Zookeeper*.

> notice:
Each partition is an ordered, immutable sequence of records that is continually appended to a structured commit log. The records in the partitions are each assigned a sequential id number called the offset that uniquely identifies each record within the partition.

#### What Is Zookeeper:
Zookeeper is a software project from the Apache Software Foundation that provides open source configuration services as well as synchronization services. Zookeeper Designed to build robust distributed systems so that programmers can meet their needs with a simple and understandable interface.
#### Zookeeper is used for:
##### 1. Collector ellection :
Zookeeper is the storage of the state of a Kafka cluster. It is used for the controller election either in the very beginning or when the current controller crashes. The controller is also responsible for telling other replicas to become partition leaders when the partition leader broker of a topic fails/crashes.

##### 2. Configuration of Topics :
which topics exist, how many partitions each has, where are the replicas, who is the preferred leader, what configuration overrides are set for each topic.

##### 3. Quotas and Access control list :
How much data is each client allowed to read and write and Who is allowed to read and write to which topic.
### How does it work?
Applications (*producers*) send messages (*records*) to a Kafka node (*broker*) and messages are processed by other applications called *consumers*. Messages get stored in a *topic* and consumers subscribe to the topic to receive new messages.
A topic is a category or feed name to which records are published. Topics in Kafka are always multi-subscriber; that is, a topic can have zero, one, or many consumers that subscribe to the data written to it.

![record](https://user-images.githubusercontent.com/36330171/64907248-1ba33580-d705-11e9-93a1-630cbeed5268.png)

Topic get split into partitions of a smaller size for better performance and scalability.
Kafka guarantees that all messages inside a partition are ordered in the sequence they came in. The way you distinct a specific message is through its offset, which you could look at as a normal array index, a sequence number which is incremented for each new message in a partition.

![topic](https://user-images.githubusercontent.com/36330171/64907375-cbc56e00-d706-11e9-809d-d56168d43536.png)

Kafka does not keep track of what records are read by the consumer and delete them but rather stores them a set amount of time or until some size threshold is met. Consumers themselves poll Kafka for new messages and say what records they want to read. This allows them to increment/decrement the offset they’re at as they wish, thus being able to replay and reprocess events.It is worth noting that consumers are actually [consumer groups](https://blog.cloudera.com/scalability-of-kafka-messaging-using-consumer-groups/) which have one or more consumer processes inside. In order to avoid two processes reading the same message twice, each partition is tied to only one consumer process per group.

![kafka](https://user-images.githubusercontent.com/36330171/64907522-f0224a00-d708-11e9-9a5d-bd5065d883f1.png)
>## Consumer Group Detail:
> In Kafka, each topic is divided into a set of logs known as partitions. Producers write to the tail of these logs and consumers read the logs at their own pace. Kafka scales topic consumption by distributing partitions among a consumer group, which is a set of consumers sharing a common group identifier. The diagram below shows a single topic with three partitions and a consumer group with two members. Each partition in the topic is assigned to exactly one member in the group.
> ![CG](https://user-images.githubusercontent.com/36330171/64948578-99de1400-d88c-11e9-81aa-8181d1b01742.png)
While the old consumer depended on Zookeeper for group management, the new consumer uses a group coordination protocol built into Kafka itself. For each group, one of the brokers is selected as the group coordinator. The coordinator is responsible for managing the state of the group. Its main job is to mediate partition assignment when new members arrive, old members depart, and when topic metadata changes. The act of reassigning partitions is known as rebalancing the group.
When a group is first initialized, the consumers typically begin reading from either the earliest or latest offset in each partition. The messages in each partition log are then read sequentially. As the consumer makes progress, it commits the offsets of messages it has successfully processed. For example, in the figure below, the consumer’s position is at offset 6 and its last committed offset is at offset 1.
![CG1](https://user-images.githubusercontent.com/36330171/64948955-651e8c80-d88d-11e9-932c-fb928da350f2.png)
> When a partition gets reassigned to another consumer in the group, the initial position is set to the last committed offset. If the consumer in the example above suddenly crashed, then the group member taking over the partition would begin consumption from offset 1. In that case, it would have to reprocess the messages up to the crashed consumer’s position of 6.
> The diagram also shows two other significant positions in the log. The log end offset is the offset of the last message written to the log. The high watermark is the offset of the last message that was successfully copied to all of the log’s replicas. From the perspective of the consumer, the main thing to know is that you can only read up to the high watermark. This prevents the consumer from reading unreplicated data which could later be lost.
## kafka Monitoring
 Kafka has grown considerably in terms of both volume and complexity, and being a crucial component in the IT infrastructure, it's necessary to implement a dedicated kafka monitor to track its operations and performance. Kafka monitoring tools like Applications Manager's Kafka monitoring tool collects all performance metrics that can help when troubleshooting Kafka issues, and it shows you which ones require corrective action.
 #### Important Kafka performance metrics to look for while performing Kafka monitoring include:
1. Resource utilization metrics
2. Kafka broker metrics
3. Kafka producer metrics
4. Kafka consumer metrics

you must automatically discover and monitor Kafka servers and track resource utilization details, such as memory, CPU, and disk growth, over time; this will ensure that you don't run out of resources.

