# Apache Kafka

## Install
```sh
# Download binary release
$ curl -O http://mirror.apache-kr.org/kafka/2.6.0/kafka_2.13-2.6.0.tgz

# Extract
$ tar -xzf kafka_2.13-2.6.0.tgz
$ cd kafka_2.13-2.6.0

# Install
$ ./gradlew jar 
```

## Zookeeper
```sh
$ bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
```

## Kafka Server
```sh
$ bin/kafka-server-start.sh -daemon config/server.properties
```

## Kafka Producer

## Kafka Consumer

