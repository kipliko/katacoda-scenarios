Search zookeeper image
`docker search zookeeper`{{execute}}

Create the Docker network that is used to run the Confluent containers.
`docker network create confluent`{{execute}}

Start ZooKeeper and keep this service running.
`docker run -d --net=confluent --name=zookeeper -e ZOOKEEPER_CLIENT_PORT=2181 confluentinc/cp-zookeeper:5.0.0`{{execute}}
This command instructs Docker to launch an instance of the confluentinc/cp-zookeeper:5.0.0 container and name it zookeeper. Also, the Docker network confluent and the required ZooKeeper parameter ZOOKEEPER_CLIENT_PORT are specified.

Start Kafka.		
`docker run -d --net=confluent --name=kafka -e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://kafka:9092 -e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1 confluentinc/cp-kafka:5.0.0`{{execute}}
The KAFKA_ADVERTISED_LISTENERS variable is set to kafka:9092. This will make Kafka accessible to other containers by advertising it’s location on the Docker network. The same ZooKeeper port is specified here as the previous container.
The KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR is set to 1 for a single-node cluster. If you have three or more nodes, you do not need to change this from the default.

Create a topic. You’ll name it foo and keep things simple by just giving it one partition and only one replica.
`docker run --net=confluent --rm confluentinc/cp-kafka:5.0.0 kafka-topics --create --topic foo --partitions 1 --replication-factor 1 --if-not-exists --zookeeper zookeeper:2181`{{execute}}

Verify that the topic was successfully created.
`docker run --net=confluent --rm confluentinc/cp-kafka:5.0.0 kafka-topics --describe --topic foo --zookeeper zookeeper:2181`{{execute}}

You should see the following:
Topic:foo   PartitionCount:1    ReplicationFactor:1 Configs:
Topic:foo   Partition: 0        Leader: 1001    Replicas: 1001  Isr: 1001
