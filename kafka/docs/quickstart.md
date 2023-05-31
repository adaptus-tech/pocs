# Quickstart (Kafka)

This quickstart is a simple example usage using kafka CLI, using Docker environment kafka brokers.

## Requirements

- Docker

## Steps

### 1. Setup Docker Compose

Create [docker-compose.yaml](../docker-compose.yaml) file with the above content.

```yaml
---
version: '3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.3.2
    container_name: zookeeper
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000

  broker:
    image: confluentinc/cp-kafka:7.3.2
    container_name: broker
    ports:
      - "9092:9092"
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_INTERNAL:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092,PLAINTEXT_INTERNAL://broker:29092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
```
And run in terminal 

```bash
docker-compose up -d
```

So, you need see some args like that in response when execute `docker ps`.

```
$ docker ps
CONTAINER ID   IMAGE                             COMMAND                  CREATED         STATUS         PORTS                          NAMES
7cd7c0708833   confluentinc/cp-kafka:7.3.2       "/etc/confluent/dock…"   5 minutes ago   Up 5 minutes   0.0.0.0:9092->9092/tcp         broker
72bbb4718d53   confluentinc/cp-zookeeper:7.3.2   "/etc/confluent/dock…"   5 minutes ago   Up 5 minutes   2181/tcp, 2888/tcp, 3888/tcp   zookeeper
```

### 2. Creating the topic

Run the following command:

```bash
docker exec broker \
    kafka-topics --bootstrap-server broker:9092 \
        --create \
        --topic quickstart
```

### 3. Producing info into topic

Running the following command in your terminal, will open a terminal and each
new line means a new event in topic.

```bash
docker exec --interactive --tty broker \
kafka-console-producer --bootstrap-server broker:9092 \
                       --topic quickstart
```

### 4. Reading info produced in topic\

With the following command, you will be able to read all events from topics from the start

```bash
docker exec --interactive --tty broker \
kafka-console-consumer --bootstrap-server broker:9092 \
                       --topic quickstart \
                       --from-beginning
```