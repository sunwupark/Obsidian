
## Kafka Topics
```

kafka-topics.sh

# Create a topic (working)

kafka-topics.sh --bootstrap-server localhost:9092 --topic first_topic --create

kafka-topics.sh --bootstrap-server localhost:9092 --topic second_topic --create --partitions 3

kafka-topics.sh --bootstrap-server localhost:9092 --topic third_topic --create --partitions 3 --replication-factor 2

# List topics

kafka-topics.sh --bootstrap-server localhost:9092 --list

  
# Describe a topic

kafka-topics.sh --bootstrap-server localhost:9092 --topic first_topic --describe

  
# Delete a topic

kafka-topics.sh --bootstrap-server localhost:9092 --topic first_topic --delete

# (only works if delete.topic.enable=true)
```


## Producer
```
kafka-topics.sh --bootstrap-server localhost:9092 --topic first_topic --create --partitions 1


# producing

kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic

> Hello World

>My name is Conduktor

>I love Kafka

>^C (<- Ctrl + C is used to exit the producer)


# producing with properties

kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic --producer-property acks=all

> some message that is acked

> just for fun

> fun learning!


# producing to a non existing topic

kafka-console-producer.sh --bootstrap-server localhost:9092 --topic new_topic

> hello world!

# our new topic only has 1 partition
kafka-topics.sh --bootstrap-server localhost:9092 --list
kafka-topics.sh --bootstrap-server localhost:9092 --topic new_topic --describe


# edit config/server.properties or config/kraft/server.properties
# num.partitions=3
# produce against a non existing topic again

kafka-console-producer.sh --bootstrap-server localhost:9092 --topic new_topic_2
hello again!

# this time our topic has 3 partitions
kafka-topics.sh --bootstrap-server localhost:9092 --list
kafka-topics.sh --bootstrap-server localhost:9092 --topic new_topic_2 --describe

# overall, please create topics with the appropriate number of partitions before producing to them!

# produce with keys

kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic --property parse.key=true --property key.separator=:

>example key:example value

>name:Stephane
```


## Consumers
```
# create a topic with 3 partitions

kafka-topics.sh --bootstrap-server localhost:9092 --topic second_topic --create --partitions 3

# consuming

kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic second_topic

# other terminal

kafka-console-producer.sh --bootstrap-server localhost:9092 --producer-property partitioner.class=org.apache.kafka.clients.producer.RoundRobinPartitioner --topic second_topic

# consuming from beginning

kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic second_topic --from-beginning

# display key, values and timestamp in consumer

kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic second_topic --formatter kafka.tools.DefaultMessageFormatter --property print.timestamp=true --property print.key=true --property print.value=true --property print.partition=true --from-beginning
```

## Consumer Grousp
```
# create a topic with 3 partitions

kafka-topics.sh --bootstrap-server localhost:9092 --topic third_topic --create --partitions 3

  

# start one consumer

kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic third_topic --group my-first-application

  

# start one producer and start producing

kafka-console-producer.sh --bootstrap-server localhost:9092 --producer-property partitioner.class=org.apache.kafka.clients.producer.RoundRobinPartitioner --topic third_topic

  

# start another consumer part of the same group. See messages being spread

kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic third_topic --group my-first-application

  

# start another consumer part of a different group from beginning

kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic third_topic --group my-second-application --from-beginning
```

