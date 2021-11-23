# Strimzi Migration Demo

This repo includes a set of resources to demonstrate how Apache MirrorMaker2 could
help in a migration from a source Apache Kafka cluster to another Apache Kafka cluster :rocket:.

For more context, please review our [blog post](https://strimzi.io/blog/2021/11/22/migrating-kafka-with-mirror-maker2/).

The scenario covered in this repo is to have an Active-Passive deployment of Apache Kafka clusters
deployed in different OpenShift clusters.

This repo was tested :sparkles: in:

* Source Environment:
  * Red Hat OpenShift Container Platform 4.5.18
  * Strimzi Operators 0.22.1 (Apache Kafka 2.5)

* Target Environment:
  * Red Hat OpenShift Container Platform 4.9.0
  * Strimzi Operators 0.26.0 (Apache Kafka 2.8)

**NOTE**: To follow this demo you should have two different OpenShift clusters available
following the versions described above.

:rotating_light: **WARN**: This repo is not defined to be a production-ready implementation but it could be used
as a base line to design and develop your specific use case. Use carefully and by own your responsibility.

## Migration Process Overview

This migration process covers the scenario when you have an Apache Kafka cluster deployed
in an OpenShift platform (source) and you need to migrate the data and your applications to
a new OpenShift platform (target). This process could be done using other tools or processes
however in this repo we focused on using one of the tools provided by Apache Kafka ecosystem: Mirror Maker 2.

The migration process could be summarized as:

1. An Apache Kafka cluster up and running in the source OpenShift platform. This platform already has
some producer and consumer applications running successfully.
2. Expose the source Apache Kafka cluster to external clients of OpenShift.
3. Extract from the source Apache Kafka cluster the certificates and credentials needed to allow
authenticated and authorized connections from outside of OpenShift.
4. Deploy a new Apache Kafka cluster in the target OpenShift platform. This new Apache Kafka cluster
could be a higher version of the original one (as we did in this repo) but it is needed to have a
similar deployment topology as the source one.
5. Deploy and create the secrets with the credentials coming from the source Kafka cluster needed to
be used by the applications. Create the users definitions in the target Kafka cluster.
6. Set up MirrorMaker2 with the source and target Kafka clusters (connection string, users, ...)
7. Deploy MirrorMaker2 in the target OpenShift platform.
8. Verify the topics are created in the target Kafka. This process could also sync the consumer groups offsets
from the source Kafka cluster.
9. Stop consumers in the source OpenShift platform.
10. Deploy and start consumers in the target OpenShift platform.
11. Stop producers in the source OpenShift platform.
12. Deploy and start producers in the target OpenShift platform.

This process started with an Active(source)-Passive(target) deployment, but when it is completed the target platform
will be the new active one and we could stop and remove the source platform.

**NOTE:** MirrorMaker2 helps to cover other kinds of topologies to migrate or replicate data between different
Apache Kafka clusters (multi-cloud, different data-flows, back-ups, ...), all of them out of the scope
of this repo.

Both Apache Kafka deployments are managed and operated by the Strimzi operators.

# Deploying Source Environment

As a normal user (non ```cluster-admin```) in your source OpenShift cluster, create the following namespace:

```shell
❯ oc login -u user
❯ oc new-project strimzi-migration
```

### Deploying Strimzi Operators

Follow [the instructions](./01-source-cluster/01-strimzi-operator/README.md)

### Deploying Apache Kafka

Follow [the instructions](./01-source-cluster/02-kafka/README.md)

### Deploying Topics

Follow [the instructions](./01-source-cluster/03-kafka-topics/README.md)

### Deploying Users

Follow [the instructions](./01-source-cluster/04-kafka-users/README.md)

### Deploying Sample Applications

Follow [the instructions](./01-source-cluster/05-sample-apps/README.md)

# Deploying Target Environment

**NOTE**: This part of the demo must done in the target environment. Check that you are
connected into the target OpenShift cluster.

As a normal user (non ```cluster-admin```) in your OpenShift cluster, create the following namespaces:

```shell
❯ oc login -u user
❯ oc new-project strimzi-migration
```

### Deploying Strimzi Operators

Follow [the instructions](./02-target-cluster/01-strimzi-operator/README.md)

### Deploying Apache Kafka

Follow [the instructions](./02-target-cluster/02-kafka/README.md)

### Deploying Users

Follow [the instructions](./02-target-cluster/03-kafka-users/README.md)

### Deploying MirrorMaker2

Follow [the instructions](./02-target-cluster/04-kafka-mirror-maker2/README.md)

### Deploying Sample Applications

Follow [the instructions](./02-target-cluster/05-sample-apps/README.md)

## Migration Process

If everything was fine you will have the original topics created from the source cluster
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

* [Migrating Kafka clusters with MirrorMaker2 and Strimzi](https://blog.jromanmartin.io/2021/11/19/migrating-kafka-with-mirror-maker2.html)
* [Strimzi Documentation](https://strimzi.io/docs/latest/)
* [Strimzi Overview](https://strimzi.io/docs/overview/latest/)
* [Using Strimzi](https://strimzi.io/docs/operators/latest/using.html)
* [Deploying Strimzi](https://strimzi.io/docs/operators/latest/deploying.html)
