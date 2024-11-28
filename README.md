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
