# Sample Applications
## Performance Tests

Kafka includes a set of scripts to execute performance tests to analyze the performance
of the cluster and its topics.

### Producer Performance Test

The ```kafka-producer-perf-test.sh``` script executes performance tests as producer.

Sample command for producer testing in ```monitor.ocp.metrics``` topic:

```shell
oc run kafka-producer-perf-test-metrics -ti --image=quay.io/strimzi/kafka:latest-kafka-2.5.0 --rm=true --restart=Never -- /bin/bash -c "cat >/tmp/producer.properties <<EOF 
bootstrap.servers=event-bus-kafka-bootstrap:9092
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=admin-user-scram password=YZlv9tXGjYhF;
EOF
bin/kafka-producer-perf-test.sh --topic monitor.ocp.metrics --num-records 1000000 --throughput 5000 --record-size 2048 --print-metrics --producer.config=/tmp/producer.properties
"
```

Sample command for producer testing in ```monitor.ocp.logs``` topic:

```shell
oc run kafka-producer-perf-test-logs -ti --image=quay.io/strimzi/kafka:latest-kafka-2.5.0 --rm=true --restart=Never -- /bin/bash -c "cat >/tmp/producer.properties <<EOF 
bootstrap.servers=event-bus-kafka-bootstrap:9092
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=admin-user-scram password=YZlv9tXGjYhF;
EOF
bin/kafka-producer-perf-test.sh --topic monitor.ocp.logs --num-records 1000000 --throughput 5000 --record-size 2048 --print-metrics --producer.config=/tmp/producer.properties
"
```

The following arguments could be useful to set up the performance test:

* ```topic```: Identify the topic to send messages.
* ```num-records```: Number of records to produce
* ```record-size```: Size in bytes of each record
* ```throughput```: Ratio of messages per second to produce

### Consumer Performance Test

The ```kafka-consumer-perf-test.sh``` script executes performance tests as producer.

Sample command for consumer test in ```monitor.ocp.metrics``` topic:

```shell
oc run kafka-consumer-perf-test-metrics -ti --image=quay.io/strimzi/kafka:latest-kafka-2.5.0 --rm=true --restart=Never -- /bin/bash -c "cat >/tmp/consumer.properties <<EOF 
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=admin-user-scram password=YZlv9tXGjYhF;
EOF
bin/kafka-consumer-perf-test.sh --broker-list event-bus-kafka-bootstrap:9092 --topic monitor.ocp.metrics --consumer.config=/tmp/consumer.properties --group monitor-group --from-latest --messages 1000000 --reporting-interval 1000 --show-detailed-stats
"
```

Sample command for consumer test in ```monitor.ocp.logs``` topic:

```shell
oc run kafka-consumer-perf-test-logs -ti --image=quay.io/strimzi/kafka:latest-kafka-2.5.0 --rm=true --restart=Never -- /bin/bash -c "cat >/tmp/consumer.properties <<EOF 
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=admin-user-scram password=YZlv9tXGjYhF;
EOF
bin/kafka-consumer-perf-test.sh --broker-list event-bus-kafka-bootstrap:9092 --topic monitor.ocp.logs --consumer.config=/tmp/consumer.properties --group monitor-group --from-latest --messages 1000000 --reporting-interval 1000 --show-detailed-stats
"
```

The following arguments could be useful to set up the performance test:

* ```topic```: Identify the topic to send messages.
* ```messages```: Number of records to consume
* ```group```: Group id to identify this test
* ```reporting-interval```: Interval in milliseconds of the performance test results.

## Samples Application

This repo includes a set of sample applications to consumer and produce messages from and to the
```apps.samples.greetings``` and ```apps.samples.greetings.reversed``` topics.

These applications use a secured user with TLS certificates.

### Producer Application

This application generates `Hello World` messages into ```apps.samples.greetings``` topic. The
```sample-user-tls``` user will authenticate using TLS certificates.

```shell
oc apply -f 01-deployment-producer.yml
```

A sample log of this application:

```log
❯ oc logs -f hello-world-producer-777b876976-hh5cf
...
2021-11-19 10:44:10 INFO  KafkaProducerExample:69 - Sending messages "Hello world - 359"
2021-11-19 10:44:10 INFO  KafkaProducerExample:69 - Sending messages "Hello world - 360"
2021-11-19 10:44:11 INFO  KafkaProducerExample:69 - Sending messages "Hello world - 361"
...
```

### Streaming Application

This application consumes messages from ```apps.samples.greetings``` topic and reverses the content
into the ```apps.samples.greetings.reversed``` topic.

The ```streams-user-tls``` user will authenticate using TLS certificates.

```shell
oc apply -f 02-deployment-streams.yml
```

A sample log of this application:

```log
❯ oc logs -f hello-world-streams-788d49c5c-mfq42
...
21729 [sample-streams-group-d7cc0a0a-184e-489e-a23e-c33919e59341-StreamThread-1] WARN org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=sample-streams-group-d7cc0a0a-184e-489e-a23e-c33919e59341-StreamThread-1-consumer, groupId=sample-streams-group] Offset commit failed on partition apps.samples.greetings-0 at offset 102: This is not the correct coordinator.
21729 [sample-streams-group-d7cc0a0a-184e-489e-a23e-c33919e59341-StreamThread-1] INFO org.apache.kafka.clients.consumer.internals.AbstractCoordinator - [Consumer clientId=sample-streams-group-d7cc0a0a-184e-489e-a23e-c33919e59341-StreamThread-1-consumer, groupId=sample-streams-group] Group coordinator event-bus-kafka-1.event-bus-kafka-brokers.amq-streams-demo.svc:9093 (id: 2147483646 rack: null) is unavailable or invalid, will attempt rediscovery
21830 [sample-streams-group-d7cc0a0a-184e-489e-a23e-c33919e59341-StreamThread-1] INFO org.apache.kafka.clients.consumer.internals.AbstractCoordinator - [Consumer clientId=sample-streams-group-d7cc0a0a-184e-489e-a23e-c33919e59341-StreamThread-1-consumer, groupId=sample-streams-group] Discovered group coordinator event-bus-kafka-0.event-bus-kafka-brokers.amq-streams-demo.svc:9093 (id: 2147483647 rack: null)
...
```

### Consumer Application

This application consumes messages from ```apps.samples.greetings.reversed``` topic.

The ```sample-user-tls``` user will authenticate using TLS certificates.

```shell
oc apply -f 03-deployment-consumer.yml
```

A sample log of this application:

```log
❯ oc logs -f hello-world-consumer-54bb9d7775-6dwbw
...
2021-11-19 10:43:40 INFO  KafkaConsumerExample:47 - Received message:
2021-11-19 10:43:40 INFO  KafkaConsumerExample:48 - 	partition: 1
2021-11-19 10:43:40 INFO  KafkaConsumerExample:49 - 	offset: 65
2021-11-19 10:43:40 INFO  KafkaConsumerExample:50 - 	value: "003 - dlrow olleH"
2021-11-19 10:43:40 INFO  KafkaConsumerExample:52 - 	headers: 
2021-11-19 10:43:41 INFO  KafkaConsumerExample:47 - Received message:
2021-11-19 10:43:41 INFO  KafkaConsumerExample:48 - 	partition: 2
2021-11-19 10:43:41 INFO  KafkaConsumerExample:49 - 	offset: 171
2021-11-19 10:43:41 INFO  KafkaConsumerExample:50 - 	value: "103 - dlrow olleH"
2021-11-19 10:43:41 INFO  KafkaConsumerExample:52 - 	headers: 
2021-11-19 10:43:41 INFO  KafkaConsumerExample:47 - Received message:
2021-11-19 10:43:41 INFO  KafkaConsumerExample:48 - 	partition: 0
2021-11-19 10:43:41 INFO  KafkaConsumerExample:49 - 	offset: 70
2021-11-19 10:43:41 INFO  KafkaConsumerExample:50 - 	value: "203 - dlrow olleH"
2021-11-19 10:43:41 INFO  KafkaConsumerExample:52 - 	headers: 
```
