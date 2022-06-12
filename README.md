# neoKafka

Barebone kafka with Spring Boot

## The Primers Before Everything:

:point_right: :link: [Kafka Introduction](https://kafka.apache.org/intro "Kafka Introduction")

### :large_blue_circle: Event streaming

Event streaming is the digital equivalent of the human body's central nervous system. Event streaming ensures a continuous flow and interpretation of data so that the right information is at the right place, at the right time.

Technically speaking,
- It is the practice of **capturing data** in real-time from event sources in the form of streams of _events_;
- **Storing** these event streams durably for later _retrieval_;
- **Manipulating, processing, and reacting** to the event streams in real-time as well as retrospectively;
- **Routing** the event streams to different _destination_ technologies as needed.

### :large_blue_circle: Apache Kafka®

Kafka combines three key capabilities for event streaming end-to-end with a single battle-tested solution:
- To **publish** (_write_) and **subscribe** to (_read_) streams of events, including continuous import/export of your data from other systems.
- To **store** streams of events durably and reliably for as long as you want.
- To **process** streams of events as they occur or retrospectively.

Above functionalities are provided in a **distributed**, **highly scalable**, **elastic**, **fault-tolerant**, and **secure** manner.
Kafka can be deployed on **bare-metal hardware**, **virtual machines**, **containers**, **on-premises** as well as in the **cloud**.

### :large_blue_circle: How does Kafka work in a nutshell?

This is a **distributed system** consisting of **servers** and **clients** that _communicate_ via a high-performance **TCP network protocol** .
* Servers : 
  * Run as a **cluster** of **one or more servers**. 
  * Some of these _servers form the storage layer_, called the **brokers**.
  * Other **servers** run **Kafka Connect** to _continuously import and export data_ as event streams.
  * A **Kafka cluster** is _highly scalable_ and _fault-tolerant_. if any of its **servers fails**, the **other servers will take over** their work to ensure continuous operations without any data loss.
* Clients : 
This allow us to **write** " _distributed applications and microservices that read, write, and process streams of events in parallel, at scale, and in a fault-tolerant manner_" even in the case of network problems or machine failures.

### :large_blue_circle: Why is Kafka fast?

:point_right: :link: [Alex Xu | bytebytego ]( https://blog.bytebytego.com/p/why-is-kafka-fast?s=r "Why is Kafka fast")

![5 2](https://user-images.githubusercontent.com/75577090/173240588-17bc7583-fae8-4cc8-8b56-c683580fecce.jpg)


### :red_circle: When NOT to use Apache Kafka?

Apache Kafka is the de facto standard for event streaming to process data in motion. 

##### Kafka is NOT…

- :x: A **proxy for millions of clients** (like mobile apps) – but Kafka-native proxies (like REST or MQTT) exist for some use cases.
- :x: An **API Management platform** – but these tools are usually complementary and used for the creation, life cycle management, or the monetization of Kafka APIs.
- :x: A **database** for complex queries and batch analytics workloads – but good enough for transactional queries and relatively simple aggregations (especially with ksqlDB).
- :x: An **IoT platform** with features such as device management  – but direct Kafka-native integration with (some) IoT protocols such as MQTT or OPC-UA is possible and the appropriate approach for (some) use cases.
- :x: A **technology for hard real-time applications** such as safety-critical or deterministic systems – but that’s true for any other IT framework, too. Embedded systems are a different software!

:white_check_mark: For these reasons, **Kafka is complementary, not competitive**, to these other technologies.

This blog post explores when NOT to use Apache Kafka.

:point_right: :link: [When NOT to use Apache Kafka](https://www.kai-waehner.de/blog/2022/01/04/when-not-to-use-apache-kafka/ "When NOT to use Apache Kafka")

### :large_blue_circle: Concepts and Terminology

_Event, Producers, Consumers, Topics, Partition, Replication_

##### :one: Event :

- An event records the fact that "something happened". Also called record or message in the documentation. **When we read or write data to Kafka, we do this in the form of events**. 
- Conceptually, an event has a key, value, timestamp, and optional metadata headers. 
- <details>
    <summary>Example Event</summary>
    <p>- Event key: "Alice"</p>
    <p>- Event value: "Made a payment of $200 to Bob"</p>
    <p>- Event timestamp: "Jun. 25, 2020 at 2:06 p.m."</p>
  </details>

##### :two: Producers & Consumers

- **Producers** are those client applications that _**publish** (write) events to Kafka_ 
- **Consumers** are those that _**subscribe to** (read and process) these events_. 
- In Kafka, producers and consumers are **fully decoupled and agnostic** of each other, which is a **key design element** to achieve the **high scalability** that Kafka is known for. 
- For example, _producers never need to wait for consumers_. Kafka provides various guarantees such as the ability to process events **exactly-once**.

##### :three: Topics

- **Events** are organized and durably **stored** in **topics**. 
- Very simplified, _a topic is similar to a folder in a filesystem, and the events are the files in that folder_. 
- An example topic name could be "payments".
- **Topics** in Kafka are always **multi-producer and multi-subscriber**: _a topic can have zero, one, or many producers_ that write events to it, as well as _zero, one, or many consumers that subscribe_ to these events. 
- Events in a topic can be read as often as needed—unlike traditional messaging systems, e**vents are not deleted after consumption**. Instead, we can **define** for how long Kafka should retain your events through a **per-topic configuration setting**, after which old events will be discarded. 
- Kafka's **performance** is **effectively constant** with **respect to data size**, so _storing data for a long time is perfectly fine_.

##### :four: Partition

- **Topics** are **partitioned**, meaning ___a topic is spread over a number of "buckets" located on different Kafka brokers___. 
- This _distributed placement_ of the data is vital for _scalability_ because it allows client applications to both read and write the data from/to many brokers at the same time. 
- When a **new event** is **published** to a **topic**,
  -  it is actually **appended** to one of the **topic's partitions**. 
  -  events with the **same event key** (e.g., a customer or vehicle ID) are **written** to the **same partition**, and 
  -  Kafka **guarantees** that any **consumer of a given topic-partition** will always **read** that **partition's events** in **exactly the same order** as they were written.


:link: ![Topic_Partition](https://kafka.apache.org/images/streams-and-tables-p1_p4.png "Topic & Partirioned Explained")

1. This example topic has four partitions P1–P4.
    1. Two different producer clients are publishing, independently from each other,
    2. new events to the topic by writing events over the network to the topic's partitions.
2. Events with the same key (denoted by their color in the figure) are written to the same partition.
3. Note that both producers can write to the same partition if appropriate.


##### :five: Replication

To make our data **fault-tolerant** and **highly-available**, 
- every topic can be replicated, 
- even across geo-regions or datacenters, so that there are **always multiple brokers** that have _a copy of the data just in case things go wrong_, or maintenance required on the brokers, and so on. 
- A common production setting is a **replication factor of 3**, i.e., there will always be three copies of your data. ___This replication is performed at the level of topic-partitions___.


### :large_blue_circle: Kafka APIs

Kafka has **five core APIs** for **Java and Scala**:

##### :one: The Admin API 

To ___manage and inspect___ topics, brokers, and other Kafka objects.

##### :two: The Producer API 

To ___publish (write)___ a stream of events to one or more Kafka topics.

##### :three: The Consumer API 

To ___subscribe to (read)___ one or more topics and ___to process___ the stream of events produced to them.

##### :four: The Kafka Streams API 

To ___implement stream processing applications and microservices___. 

It provides higher-level functions to process event streams, including transformations, stateful operations like aggregations and joins, windowing, processing based on event-time, and more. 

Input is read from one or more topics in order to generate output to one or more topics, effectively transforming the input streams to output streams.

##### :five: The Kafka Connect API 

To ___build and run reusable data import/export connectors___ that consume (read) or produce (write) streams of events from and to external systems and applications so they can integrate with Kafka. 

For example, a connector to a relational database like PostgreSQL might capture every change to a set of tables. However, in practice, you typically don't need to implement your own connectors because the Kafka community already provides hundreds of ready-to-use connectors.
  
### :large_blue_circle: Kafka maven dependency for Java

##### Producer API

```
<dependency>
	<groupId>org.apache.kafka</groupId>
	<artifactId>kafka-clients</artifactId>
	<version>3.2.0</version>
</dependency>
```
##### Consumer API

```
<dependency>
	<groupId>org.apache.kafka</groupId>
	<artifactId>kafka-clients</artifactId>
	<version>3.2.0</version>
</dependency>
```

##### Streams API

```
<dependency>
	<groupId>org.apache.kafka</groupId>
	<artifactId>kafka-streams</artifactId>
	<version>3.2.0</version>
</dependency>
```

##### Admin API

```
<dependency>
	<groupId>org.apache.kafka</groupId>
	<artifactId>kafka-clients</artifactId>
	<version>3.2.0</version>
</dependency>
```

## :large_orange_diamond: Github

### :point_right:  Spring for Apache Kafka :

:link: [Github | Spring]( https://github.com/spring-projects/spring-kafka "spring-projects/spring-kafka ")

### :point_right: Confluent Inc.

:link: [Github | Confluent Inc.](https://github.com/confluentinc "Confluent Inc.")
