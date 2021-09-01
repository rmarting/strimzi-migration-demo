# Red Hat AMQ Streams Migration Demo

This repo includes a set of resources to demonstrate how Apache MirrorMaker2 could
help in a migration from a source Apache Kafka cluster to another Apache Kafka cluster.

The scenario covered in this repo is to have an Active-Passive deployment of Apache Kafka clusters
deployed in different OpenShift clusters.

This repo was tested in the following environments:

* Source Environment:
  * Red Hat OpenShift Container Platform 4.5.18
  * Red Hat AMQ Streams Operators 1.6.3

* Target Environment:
  * Red Hat OpenShift Container Platform 4.8.5
  * Red Hat AMQ Streams Operators 1.8.0


**NOTE**: This repo has been tested in Red Hat OpenShift Container Platform 4.6 version.

# Deploying Source Environment

As a normal user (non ```cluster-admin```) in your OpenShift cluster, create the following namespaces:

```shell
❯ oc login -u user
❯ oc new-project amq-streams-migration
```

### Deploying Red Hat AMQ Streams Operators

Follow [the instructions](./01-source-cluster/01-amq-streams-operator/README.md)

### Deploying Red Hat AMQ Streams

Follow [the instructions](./01-source-cluster/02-kafka/README.md)

### Deploying Topics

Follow [the instructions](./01-source-cluster/03-kafka-topics/README.md)

### Deploying Users

Follow [the instructions](./01-source-cluster/04-kafka-users/README.md)

### Deploying Sample Applications

Follow [the instructions](./01-source-cluster/05-sample-apps/README.md)

# Deploying Target Environment

**NOTE**: This part of the demo must done in the target environment. Check that you are
connected into OpenShift target cluster.

As a normal user (non ```cluster-admin```) in your OpenShift cluster, create the following namespaces:

```shell
❯ oc login -u user
❯ oc new-project amq-streams-migration
```

### Deploying Red Hat AMQ Streams Operators

Follow [the instructions](./02-target-cluster/01-amq-streams-operator/README.md)

### Deploying Red Hat AMQ Streams

Follow [the instructions](./02-target-cluster/02-kafka/README.md)

### Deploying Users

Follow [the instructions](./02-target-cluster/03-kafka-users/README.md)

### Deploying Sample Applications

Follow [the instructions](./02-target-cluster/05-sample-apps/README.md)

## Migration Process

If everything was fine you will have the original topics created in the source cluster
in your target cluster and with the same data:

```shell
❯ oc get kt
NAME                                    CLUSTER     PARTITIONS   REPLICATION FACTOR   READY
apps.samples.greetings                  event-bus   3            3                    True
apps.samples.greetings.reversed         event-bus   3            3                    True
heartbeats                              event-bus   1            3                    True
mirrormaker2-cluster-configs            event-bus   1            3                    True
mirrormaker2-cluster-offsets            event-bus   25           3                    True
mirrormaker2-cluster-status             event-bus   5            3                    True
monitor.ocp.logs                        event-bus   10           3                    True
monitor.ocp.metrics                     event-bus   10           3                    True
my-source-cluster.checkpoints.internal  event-bus   1            3                    True
```

## References

* [Red Hat AMQ Product Documentation](https://access.redhat.com/documentation/en-us/red_hat_amq/7.7/)
* [AMQ Streams on OCP Overview](https://access.redhat.com/documentation/en-us/red_hat_amq/7.7/html-single/amq_streams_on_openshift_overview/index)
* [Using AMQ Streams on OCP](https://access.redhat.com/documentation/en-us/red_hat_amq/7.7/html-single/using_amq_streams_on_openshift/index)
* [Red Hat AMQ 7 Component Details Page](https://access.redhat.com/articles/3188232)
* [Red Hat AMQ 7 Supported Configurations](https://access.redhat.com/articles/2791941)
* [Strimzi Documentation](https://strimzi.io/docs/latest/)
* [Strimzi Overview](https://strimzi.io/docs/overview/latest/)
* [Using Strimzi](https://strimzi.io/docs/operators/latest/using.html)
* [Deploying Strimzi](https://strimzi.io/docs/operators/latest/deploying.html)
