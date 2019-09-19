# Apache-Kafka
### What Is Kafka?
It is generally used as a publish/subscribe messaging system, It allows us to publish and subscribe to a stream of records that can be categorized. Kafka is written in Java. It is often used in real-time streaming data architectures to provide real-time analytics.<p> Since Kafka is a fast, scalable, cluster, durable, [storage](https://github.com/kambiz1363/Apache-Kafka/blob/master/README.md#kafka-storage-internals) and *fault-tolerant publish-subscribe messaging system*.It is distributed store, receives and send records on different nodes that called *brokers*.  Brokers receive records from producers, assigns [offsets](https://github.com/kambiz1363/Apache-Kafka/blob/master/README.md#offset) to them, and commits them to storage. For this reason, it needed [Zookeeper](https://github.com/kambiz1363/Apache-Kafka/blob/master/README.md#what-is-zookeeper). </p>
### kafka architecture
Kafka has four core APIs:

* **Producer API**: 
This API enables the source or sender system to send data to the topics in Kafka cluster.
* **Consumer API**: 
This API enables the receiving or consuming application to consume the data from Kafka cluster.

* **Streams API**: 
This API enables transformation of incoming data; transformation may be simple mapping, filtering, aggregation etc.In Kafka a stream processor is anything that takes continual streams of data from input topics, performs some processing on this input, and produces continual streams of data to output topics.
For example, a retail application might take in input streams of sales and shipments, and output a stream of reorders and price adjustments computed off this data.

details about Stream Processing:
In Kafka a stream processor is anything that takes continual streams of data from input topics, performs some processing on this input, and produces continual streams of data to output topics.
For example, a retail application might take in input streams of sales and shipments, and output a stream of reorders and price adjustments computed off this data.
It is possible to do simple processing directly using the producer and consumer APIs. However for more complex transformations Kafka provides a fully integrated Streams API. This allows building applications that do non-trivial processing that compute aggregations off of streams or join streams together.
This facility helps solve the hard problems this type of application faces: handling out-of-order data, reprocessing input as code changes, performing stateful computations, etc.
The streams API builds on the core primitives Kafka provides: it uses the producer and consumer APIs for input, uses Kafka for stateful storage, and uses the same group mechanism for fault tolerance among the stream processor instances.

* **Connector API**: 
allows building and running reusable producers or consumers that connect Kafka topics to existing applications or data systems. For example, a connector to a relational database might capture every change to a table.

##### offset:
Each partition is an ordered, immutable sequence of records that is continually appended to a structured commit log. The records in the partitions are each assigned a sequential id number called the offset that uniquely identifies each record within the partition.

### What Is Zookeeper:
Zookeeper is a software project from the Apache Software Foundation that provides open source configuration services as well as synchronization services. Zookeeper Designed to build robust distributed systems so that programmers can meet their needs with a simple and understandable interface.
#### Zookeeper is used for:
##### 1. Collector ellection :
Zookeeper is the storage of the state of a Kafka cluster. It is used for the controller election either in the very beginning or when the current controller crashes. The controller is also responsible for telling other replicas to become partition leaders when the partition leader broker of a topic fails/crashes.

##### 2. Configuration of Topics :
which topics exist, how many partitions each has, where are the replicas, who is the preferred leader, what configuration overrides are set for each topic.

##### 3. Quotas and Access control list :
How much data is each client allowed to read and write and Who is allowed to read and write to which topic.
### How does it work(kafka)?
Applications (*producers*) send messages (*records*) to a Kafka node (*broker*) and messages are processed by other applications called *consumers*. Messages get stored in a *topic* and consumers subscribe to the topic to receive new messages.
A topic is a category or feed name to which records are published. Topics in Kafka are always multi-subscriber; that is, a topic can have zero, one, or many consumers that subscribe to the data written to it.

![record](https://user-images.githubusercontent.com/36330171/64907248-1ba33580-d705-11e9-93a1-630cbeed5268.png)

Topic get split into partitions of a smaller size for better performance and scalability.
Kafka guarantees that all messages inside a partition are ordered in the sequence they came in. The way you distinct a specific message is through its offset, which you could look at as a normal array index, a sequence number which is incremented for each new message in a partition.

![topic](https://user-images.githubusercontent.com/36330171/64907375-cbc56e00-d706-11e9-809d-d56168d43536.png)

Kafka does not keep track of what records are read by the consumer and delete them but rather stores them a set amount of time or until some size threshold is met. Consumers themselves poll Kafka for new messages and say what records they want to read. This allows them to increment/decrement the offset they’re at as they wish, thus being able to replay and reprocess events.It is worth noting that consumers are actually consumer groups which have one or more consumer processes inside. In order to avoid two processes reading the same message twice, each partition is tied to only one consumer process per group.

![kafka](https://user-images.githubusercontent.com/36330171/64907522-f0224a00-d708-11e9-9a5d-bd5065d883f1.png)
##### Consumer Group Detail:
In Kafka, each topic is divided into a set of logs known as partitions. Producers write to the tail of these logs and consumers read the logs at their own pace. Kafka scales topic consumption by distributing partitions among a consumer group, which is a set of consumers sharing a common group identifier. The diagram below shows a single topic with three partitions and a consumer group with two members. Each partition in the topic is assigned to exactly one member in the group.

![CG](https://user-images.githubusercontent.com/36330171/64948578-99de1400-d88c-11e9-81aa-8181d1b01742.png)

While the old consumer depended on Zookeeper for group management, the new consumer uses a group coordination protocol built into Kafka itself. For each group, one of the brokers is selected as the group coordinator. The coordinator is responsible for managing the state of the group. Its main job is to mediate partition assignment when new members arrive, old members depart, and when topic metadata changes. The act of reassigning partitions is known as rebalancing the group.
When a group is first initialized, the consumers typically begin reading from either the earliest or latest offset in each partition. The messages in each partition log are then read sequentially. As the consumer makes progress, it commits the offsets of messages it has successfully processed. For example, in the figure below, the consumer’s position is at offset 6 and its last committed offset is at offset 1.

![CG1](https://user-images.githubusercontent.com/36330171/64948955-651e8c80-d88d-11e9-932c-fb928da350f2.png)

When a partition gets reassigned to another consumer in the group, the initial position is set to the last committed offset. If the consumer in the example above suddenly crashed, then the group member taking over the partition would begin consumption from offset 1. In that case, it would have to reprocess the messages up to the crashed consumer’s position of 6.
The diagram also shows two other significant positions in the log. The log end offset is the offset of the last message written to the log. The high watermark is the offset of the last message that was successfully copied to all of the log’s replicas. From the perspective of the consumer, the main thing to know is that you can only read up to the high watermark. This prevents the consumer from reading unreplicated data which could later be lost.
## More concepts
#### Kafka Storage Internals
Data in Kafka is stored in *topics* and topics are *partitioned*. Each partition is further divided into *segments* and Each segment has a log file to store the actual message and an index file to store the position of the messages in the log file.
Various partitions of a topic can be on different brokers but a partition is always tied to a single broker.
With the basic stuff out of our way, let’s understand these concepts better by working with Kafka.
I am going to start by creating a topic in Kafka with three partitions. If you want to follow along, the command looks like this for a local Kafka setup on Ubuntu
```
$ bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic FirstTopic
```
If I go into Kafka’s log directory, I see three directories created as follows.
```
$ tree FirstTopic-*
FirstTopic-0
├── 00000000000000000000.index
├── 00000000000000000000.log
├── 00000000000000000000.timeindex
└── leader-epoch-checkpoint
FirstTopic-1
├── 00000000000000000000.index
├── 00000000000000000000.log
├── 00000000000000000000.timeindex
└── leader-epoch-checkpoint
FirstTopic-2
├── 00000000000000000000.index
├── 00000000000000000000.log
├── 00000000000000000000.timeindex
└── leader-epoch-checkpoint

0 directories, 12 files
```
We have three directories created because we’ve given three partitions for our topic, which means that each partition gets a directory on the file system. You also see some files like index, log etc.
One more thing that you should be able to see from here is that in Kafka, the topic is more of a logical grouping than anything else and that the Partition is the actual unit of storage in Kafka. That is what is physically stored on the disk. Let’s understand partitions in some more detail.
##### Partitions
A partition, in theory, can be described as an immutable collection (or sequence) of messages. Produser can only append messages to a partition but cannot delete from it. Now we’ll send some messages into the topic.
```
$ ls -lash FirstTopic-0
total 12K
4.0K drwxr-xr-x 2 kafka kafka 4.0K Sep 18 04:49 .
4.0K drwxr-xr-x 5 kafka kafka 4.0K Sep 18 04:54 ..
   0 -rw-r--r-- 1 kafka kafka  10M Sep 18 04:49 00000000000000000000.index
   0 -rw-r--r-- 1 kafka kafka    0 Sep 18 04:49 00000000000000000000.log
   0 -rw-r--r-- 1 kafka kafka  10M Sep 18 04:49 00000000000000000000.timeindex
4.0K -rw-r--r-- 1 kafka kafka    8 Sep 18 04:49 leader-epoch-checkpoint
```
You see the index files combined are about 20M in size while the log file is completely empty. This is the same case with FirstTopic-1 and First-Topic-2 folders. Now let us send a couple of messages and see what happens. To send the messages I’m using the console producer as follows:
```
$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic FirstTopic
```
I have sent two messages, first a customary "hello world" and "amazon"
```
$ ls -lash FirstTopic-*
FirstTopic-0:
total 12K
4.0K drwxr-xr-x 2 kafka kafka 4.0K Sep 18 05:05 .
4.0K drwxr-xr-x 5 kafka kafka 4.0K Sep 18 05:10 ..
   0 -rw-r--r-- 1 kafka kafka  10M Sep 18 05:05 00000000000000000000.index
   0 -rw-r--r-- 1 kafka kafka    0 Sep 18 05:05 00000000000000000000.log
   0 -rw-r--r-- 1 kafka kafka  10M Sep 18 05:05 00000000000000000000.timeindex
4.0K -rw-r--r-- 1 kafka kafka    8 Sep 18 05:05 leader-epoch-checkpoint

FirstTopic-1:
total 16K
4.0K drwxr-xr-x 2 kafka kafka 4.0K Sep 18 05:05 .
4.0K drwxr-xr-x 5 kafka kafka 4.0K Sep 18 05:10 ..
   0 -rw-r--r-- 1 kafka kafka  10M Sep 18 05:05 00000000000000000000.index
4.0K -rw-r--r-- 1 kafka kafka   79 Sep 18 05:08 00000000000000000000.log
   0 -rw-r--r-- 1 kafka kafka  10M Sep 18 05:05 00000000000000000000.timeindex
4.0K -rw-r--r-- 1 kafka kafka    8 Sep 18 05:05 leader-epoch-checkpoint

FirstTopic-2:
total 16K
4.0K drwxr-xr-x 2 kafka kafka 4.0K Sep 18 05:05 .
4.0K drwxr-xr-x 5 kafka kafka 4.0K Sep 18 05:10 ..
   0 -rw-r--r-- 1 kafka kafka  10M Sep 18 05:05 00000000000000000000.index
4.0K -rw-r--r-- 1 kafka kafka   74 Sep 18 05:09 00000000000000000000.log
   0 -rw-r--r-- 1 kafka kafka  10M Sep 18 05:05 00000000000000000000.timeindex
4.0K -rw-r--r-- 1 kafka kafka    8 Sep 18 05:05 leader-epoch-checkpoint
```
Out two messages went into two of the partitions where you can see that the log files have a non zero size. This is because the messages in the partition are stored in the ‘xxxx.log’ file. To confirm that the messages are indeed stored in the log file, we can just see what’s inside that log file.
```
$ cat FirstTopic-1/*.log
CbZ�%mBőKmBőK��������������"hello world
```
The file format of the ‘log’ file is not one that is conducive for textual representation but nevertheless, you should see the ‘Hello World’ at the end indicating that this file got updated when we have sent the message into the topic. The second message we have sent went into the other partition.
Notice that the first message we sent, went into the third partition (FirstTopic-1) and the second message went into the second partition (FirstTopic-2). This is because Kafka arbitrarily picks the partition for the first message and then distributes the messages to partitions in a round robin fashion. If a third message comes now, it would go into FirstTopic-0 and this order of partition continues for any new message that comes in. We can also make Kafka choose the same partition for our messages by adding a key to the message. Kafka stores all the messages with the same key into a single partition.
Each new message in the partition gets an Id which is one more than the previous Id number. This Id number is also called as the [Offset](https://github.com/kambiz1363/Apache-Kafka/blob/master/README.md#offset). So, the first message is at ‘offset’ 0, the second message is at offset 1 and so on. These offset Id’s are always incremented from the previous value.
![Offset](https://user-images.githubusercontent.com/36330171/65115853-81d9d200-d9fd-11e9-8d6d-35de7738fb66.png)

#### Multi-tenancy
You can deploy Kafka as a multi-tenant solution. Multi-tenancy is enabled by configuring which topics can produce or consume data. There is also operations support for quotas. Administrators can define and enforce quotas on requests to control the broker resources that are used by clients.
#### Kafka as a Messaging System
Messaging traditionally has two models: queuing and publish-subscribe. In a queue, a pool of consumers may read from a server and each record goes to one of them; in publish-subscribe the record is broadcast to all consumers. Each of these two models has a strength and a weakness.The strength of queuing is that it allows you to divide up the processing of data over multiple consumer instances, which lets you scale your processing. Unfortunately, queues aren't multi-subscriber—once one process reads the data it's gone. Publish-subscribe allows you broadcast data to multiple processes, but has no way of scaling processing since every message goes to every subscriber.
![SU](https://user-images.githubusercontent.com/36330171/64955244-f9dcb680-d89c-11e9-85a0-d6317778625c.png)

The consumer group concept in Kafka generalizes these two concepts. As with a queue the consumer group allows you to divide up processing over a collection of processes (the members of the consumer group). As with publish-subscribe, Kafka allows you to broadcast messages to multiple consumer groups.
#### Offline Data Load
Scalable persistence allows for the possibility of consumers that only periodically consume such as batch data loads that periodically bulk-load data into an offline system such as Hadoop or a relational data warehouse.
In the case of Hadoop we parallelize the data load by splitting the load over individual map tasks, one for each node/topic/partition combination, allowing full parallelism in the loading. Hadoop provides the task management, and tasks which fail can restart without danger of duplicate data—they simply restart from their original position.

## kafka Monitoring
 Kafka has grown considerably in terms of both volume and complexity, and being a crucial component in the IT infrastructure, it's necessary to implement a dedicated kafka monitor to track its operations and performance. Kafka monitoring tools like Applications Manager's Kafka monitoring tool collects all performance metrics that can help when troubleshooting Kafka issues, and it shows you which ones require corrective action.
The easiest way to see the available metrics is to fire up jconsole and point it at a running kafka client or server; this will allow browsing all metrics with JMX.
 #### Important Kafka performance metrics to look for while performing Kafka monitoring include:
1. Resource utilization metrics
2. Kafka broker metrics
3. Kafka producer metrics
4. Kafka consumer metrics

Kafka uses Yammer Metrics for metrics reporting in the server. The Java clients use Kafka Metrics, a built-in metrics registry that minimizes transitive dependencies pulled into client applications. Both expose metrics via JMX and can be configured to report stats using pluggable stats reporters to hook up to your monitoring system.
All Kafka rate metrics have a corresponding cumulative count metric with suffix ```-total```. For example, ```records-consumed-rate``` has a corresponding metric named ```records-consumed-total```.
##### Security Considerations for Remote Monitoring using JMX
Apache Kafka disables remote JMX by default. You can enable remote monitoring using JMX by setting the environment variable JMX_PORT for processes started using the CLI or standard Java system properties to enable remote JMX programmatically. You must enable security when enabling remote JMX in production scenarios to ensure that unauthorized users cannot monitor or control your broker or application as well as the platform on which these are running. Note that authentication is disabled for JMX by default in Kafka and security configs must be overridden for production deployments by setting the environment variable ```KAFKA_JMX_OPTS``` for processes started using the CLI or by setting appropriate Java system properties.
##### Common monitoring metrics for producer/consumer/connect/streams example:
The following metrics are available on producer/consumer/connector/streams instances. For specific metrics, please see following example:

![monitoring](https://user-images.githubusercontent.com/36330171/65037056-1b53a600-d962-11e9-8e82-4b0dafb89d87.png)

You must automatically discover and monitor Kafka servers and track resource utilization details, such as memory, CPU, and disk growth, over time; this will ensure that you don't run out of resources.

-------------
Resources
* [kafka website](https://kafka.apache.org)
* [A Practical Introduction to Kafka Storage Internals](https://medium.com/@durgaswaroop/a-practical-introduction-to-kafka-storage-internals-d5b544f6925f)
* [Scalability of Kafka Messaging using Consumer Groups](https://blog.cloudera.com/scalability-of-kafka-messaging-using-consumer-groups)
* [Introducing the Kafka Consumer](https://www.confluent.io/blog/tutorial-getting-started-with-the-new-apache-kafka-0-9-consumer-client/)
* [zookeeper website](https://zookeeper.apache.org/)
