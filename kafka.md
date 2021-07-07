
# Kafka:
- Created by Linkedin, now open source project mainly maintained by Confluent.
It is a distributed streaming platform.
OR
- It is a **distributed publish-subscribe** Messaging system.

        Messaging system has traditionally two models- 
    
        1. Queueing - In this pool of consumers may read from a server and each record only goes to one of them.
        2. publish-subscribe- while in this, the record is broadcast to consumers, so that multiple consumers can get the record.

- Kafka is distributed - means multiple machines running in parallel.
- Kafka is used for building real time data pipelines ans streaming apps. It is horizentaly scaleble, fault-tolerant,wicked fast(really fast than normal) and runs in production in thousands of company.

## Problem organisation faces with other architecture:
- If you have 4 source system and 6 target system, you need to write 24 integration.
- Each integration comes with difficulties arounf protcol, data format, data schemes etc.
- Each source systems will have inceased load from the connection. 

Solution:
![apache Kafka](https://github.com/Ashu-hub/All-About-MicroServices/blob/master/images/apacheKafka.jpg)

## Advantages of Kafka:
1. Distrbuted, resilient architecture, fault tolerant
2. Horizental scalebility:  
    - can scale to 100s of brokers.
    - can scale to millions of messages per second.
3. High Performance - latency of less than 10ms- real time.

eg:-
- Netflix uses kafka to apply recommendations in **real-time** while you are watching TV shows.
- Uber uses kafka extensively to gather user, taxi, trip data in **real time** to comute and forcast demands. etc
Remember that Kafka is only used as a transportation mechanism.

## Use Case of Apache Kafka:
- Messaging system
- Activity tracking
- Gather metrics from many different locations.
- Decoupling of system dependency.


## Kafka Ecosystem:

![App Screenshot](https://github.com/Ashu-hub/All-About-MicroServices/blob/master/images/Kafka-Architecture.png)


## Key terminology:
- Data pipelines:- Communication is required between different system in real time scenario, which is done using Data pipelines.
- Event streaming:- Event streaming is the practice of capturing data in real-time from event sources like databases, sensors, mobile devices, cloud services etc. 
- A streaming platform has 3 capablities:
        
        1. To **publish (write) and subscribe** to (read) streams of events, including continuous import/export of your data from other systems.
        2. To **store streams of events** durably and reliably for as long as you want.
        3. To **process streams of events** as they occur or retrospectively.
- **TOPIC** - Topics are category or feed name to which records are published. Topics are always multi- subscriber i.e. Topic can have 0, more or many consumers subscribe to a particular topic and consume the data.
    eg: you can have sales record getting published to Sales topic. product recod getting published to product topic etc. 

- **Partitions**- Topics are divided into number of Partitions.

- **Consumers**- Any applicaition that subscribe to a topic and consumes the messages.
- **Broker**- It is a single machine in kafka cluster.
- **ZooKeeper**- Is stores/maintain the metadata info related to kafka cluster like broker info, topic details etc.
- **Offset** - Each messages in a Partition is assigned a sequential id called an Offset
- **Configurable time** - Even of the record does not have any consumer, kafka will retain it. Default time of retention is 7 days.


## Kafka Components:

![Kafka Components](https://github.com/Ashu-hub/All-About-MicroServices/blob/master/images/KafkaComponents.png)

  

![TopicPartitionsReplicas](https://github.com/Ashu-hub/All-About-MicroServices/blob/master/images/TopicPartitionsReplicas.jpg)

  
  
## Difference between JMS and Kafka:-

Any additional information goes here

  