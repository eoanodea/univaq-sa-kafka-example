# Kafka Quick Start Example 🚀

This repository provides a quick example of how to set up and use Kafka with Docker Compose. This setup includes a Zookeeper and a Kafka broker. Based on a lesson for Software Architecture at Università degli Studi dell'Aquila.

Code taken from [Baeldung](https://www.baeldung.com/ops/kafka-new-topic-docker-compose) and modified.

## Prerequisites 📋

- Docker
- Docker Compose

## Getting Started 🏁

1. Clone the repository:

   ```sh
   git clone git@github.com:eoanodea/univaq-sa-kafka-example
   cd univaq-sa-kafka-example
   ```

2. Start the Kafka and Zookeeper services using Docker Compose:

   ```sh
   docker-compose up -d
   ```

3. Verify that the services are running:

   ```sh
   docker-compose ps
   ```

## Creating a Topic 📝

To create a new topic named orders, run the following command:

```sh
docker-compose exec kafka kafka-topics.sh \
  --create \
  --topic orders \
  --partitions 1 \
  --replication-factor 1 \
  --bootstrap-server kafka:9092
```

## Starting a consumer & Producing Messages 📡

To start a consumer that listens to the orders topic from the beginning, run:

```sh
docker-compose exec kafka kafka-console-consumer.sh \
    --topic orders \
    --from-beginning \
    --bootstrap-server kafka:9092
```

To publish messages to the orders topic, run (in another terminal):

```sh
docker-compose exec kafka kafka-console-producer.sh \
    --topic orders \
    --bootstrap-server kafka:9092
```

After running the producer command, you can type messages into the terminal, and they will be sent to the orders topic. The consumer terminal will display these messages.

After you are done, you can delete the topic by running:

```sh
docker-compose exec kafka kafka-topics.sh \
  --delete \
  --topic orders \
  --bootstrap-server kafka:9092
```

## Partitioning Example 📊

To create a topic with multiple partitions, run:

```sh
docker-compose exec kafka kafka-topics.sh \
  --create \
  --topic orders-partitioned \
  --partitions 3 \
  --replication-factor 1 \
  --bootstrap-server kafka:9092
```

Then, you can start some consumers for this topic:

First Consumer (Partition 0):

```sh
docker-compose exec kafka kafka-console-consumer.sh \
    --topic orders-partitioned \
    --from-beginning \
    --partition 0 \
    --bootstrap-server kafka:9092
```

Second Consumer (Partition 1):

```sh
docker-compose exec kafka kafka-console-consumer.sh \
    --topic orders-partitioned \
    --from-beginning \
    --partition 1 \
    --bootstrap-server kafka:9092
```

Now we can produce messages to the orders-partitioned topic:

```sh
docker-compose exec kafka kafka-console-producer.sh \
  --topic orders-partitioned \
  --bootstrap-server kafka:9092 \
  --property "parse.key=true" \
  --property "key.separator=:"
```

When producing messages, you can specify a key to determine the partition. For example:

```
customerA:order-1
customerA:order-2
customerB:order-3
```

Send multiple messages and observe how they are distributed across the partitions and consumed by the respective consumers.

After you are done, you can delete the partitioned topic by running:

```sh
docker-compose exec kafka kafka-topics.sh \
  --delete \
  --topic orders-partitioned \
  --bootstrap-server kafka:9092
```

## Consumer Groups 👥

Consumer groups allow multiple consumers to share the workload of consuming messages from topics. Each consumer in a group reads from a subset of the partitions.

To demonstrate consumer groups, let's start by creating the orders-partitioned topic again:

```sh
docker-compose exec kafka kafka-topics.sh \
  --create \
  --topic orders-partitioned \
  --partitions 3 \
  --replication-factor 1 \
  --bootstrap-server kafka:9092
```

Now, start two consumers that belong to the same consumer group (e.g., product-service):

First Consumer (Group: product-service):

```sh
docker-compose exec kafka kafka-console-consumer.sh \
  --topic orders-partitioned \
  --group product-service \
  --bootstrap-server kafka:9092
```

Second Consumer (Group: product-service):

```sh
docker-compose exec kafka kafka-console-consumer.sh \
  --topic orders-partitioned \
  --group product-service \
  --bootstrap-server kafka:9092
```

Now, let's produce some messages to the orders-partitioned:

```sh
for i in {1..50}; do echo "customer$((i%4)):order-$i"; done | \
docker-compose exec -T kafka kafka-console-producer.sh \
  --topic orders-partitioned \
  --bootstrap-server kafka:9092 \
  --property "parse.key=true" \
  --property "key.separator=:"
```

When you send messages, observe how they are distributed between the two consumers in the product-service group. Each consumer will read from different partitions, demonstrating load balancing within the consumer group.

## Stopping the Services 🛑

To stop the Kafka and Zookeeper services, run:

```sh
docker-compose down
```

## Additional Commands 🛠️

### List Topics

To list all the topics, run:

```sh
docker-compose exec kafka kafka-topics.sh \
  --list \
  --bootstrap-server kafka:9092
```

### Describe a Topic

To describe a topic, run:

```sh
docker-compose exec kafka kafka-topics.sh \
  --describe \
  --topic orders \
  --bootstrap-server kafka:9092
```

### Delete a Topic

To delete a topic, run:

```sh
docker-compose exec kafka kafka-topics.sh \
  --delete \
  --topic orders \
  --bootstrap-server kafka:9092
```
