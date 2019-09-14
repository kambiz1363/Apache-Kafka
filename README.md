# Apache-Kafka
### What Is Kafka?
It is generally used as a publish/subscribe messaging system, It allows us to publish and subscribe to a stream of records that can be categorized. Kafka is written in Java. It is often used in real-time streaming data architectures to provide real-time analytics.<p> Since Kafka is a fast, scalable, durable, and *fault-tolerant publish-subscribe messaging system*.</p>
### How does it work?
Applications (producers) send messages (records) to a Kafka node (broker) and messages are processed by other applications called consumers.Messages get stored in a topic and consumers subscribe to the topic to receive new messages.
![record](https://user-images.githubusercontent.com/36330171/64907248-1ba33580-d705-11e9-93a1-630cbeed5268.png)
Topic get split into partitions of a smaller size for better performance and scalability.
