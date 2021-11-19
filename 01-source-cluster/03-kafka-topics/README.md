# Kafka Topics

The Kafka Topics are described in the following files:

* ```topics/greetings-topic.yml```: Sample topic to be used by the sample application
* ```topics/greetings-reversed-topic.yml```: Sample topic to be used by the sample streams application
* ```topics/logs-topic.yml```: Topic to store logs
* ```topics/metrics-topic.yml```: Topic to store metrics

You can deploy or update the topics with the following command:

```shell
oc apply -f topics/
```

This command will show the status of the Kafka Topics:

```shell
❯ oc get kt
NAME                              CLUSTER     PARTITIONS   REPLICATION FACTOR   READY
apps.samples.greetings            event-bus   3            3                    True
apps.samples.greetings.reversed   event-bus   3            3                    True
monitor.ocp.logs                  event-bus   10           3                    True
monitor.ocp.metrics               event-bus   10           3                    True
```

To describe a KafkaTopic:

```shell
oc get kafkatopic monitor.ocp.metrics -o yaml
```

References:

* [Using the Topic Operator](https://strimzi.io/docs/operators/latest/using.html#using-the-topic-operator-str)
