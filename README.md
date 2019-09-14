# Apache-Kafka
### What Is Kafka?
It is generally used as a publish/subscribe messaging system, It allows us to publish and subscribe to a stream of records that can be categorized. Kafka is written in Java. It is often used in real-time streaming data architectures to provide real-time analytics.<p> Since Kafka is a fast, scalable, durable, and *fault-tolerant publish-subscribe messaging system*.</p>
### How does it work?
Applications (*producers*) send messages (*records*) to a Kafka node (*broker*) and messages are processed by other applications called *consumers*. Messages get stored in a *topic* and consumers subscribe to the topic to receive new messages.
A topic is a category or feed name to which records are published. Topics in Kafka are always multi-subscriber; that is, a topic can have zero, one, or many consumers that subscribe to the data written to it.

![record](https://user-images.githubusercontent.com/36330171/64907248-1ba33580-d705-11e9-93a1-630cbeed5268.png)

Topic get split into partitions of a smaller size for better performance and scalability.
Kafka guarantees that all messages inside a partition are ordered in the sequence they came in. The way you distinct a specific message is through its offset, which you could look at as a normal array index, a sequence number which is incremented for each new message in a partition.

![topic](https://user-images.githubusercontent.com/36330171/64907375-cbc56e00-d706-11e9-809d-d56168d43536.png)

Kafka does not keep track of what records are read by the consumer and delete them but rather stores them a set amount of time or until some size threshold is met. Consumers themselves poll Kafka for new messages and say what records they want to read. This allows them to increment/decrement the offset they’re at as they wish, thus being able to replay and reprocess events.It is worth noting that consumers are actually consumer groups which have one or more consumer processes inside. In order to avoid two processes reading the same message twice, each partition is tied to only one consumer process per group.
![kafka](https://user-images.githubusercontent.com/36330171/64907522-f0224a00-d708-11e9-9a5d-bd5065d883f1.png)
