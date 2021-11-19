# Apache Mirror Maker 2

Kafka MirrorMaker2 replicates data between Kafka clusters. This process is called mirroring to avoid
confusion with the Kafka partitions replication concept. MirrorMaker2 consumes messages from the
source cluster and republishes those messages to the target cluster.

From cluster source, it is needed to extract the Cluster CA Cert and the user credentials needed
to connect:

```shell
oc get secret event-bus-cluster-ca-cert -o yaml > source-secrets/event-bus-source-cluster-ca-cert.yaml
```

As there is already a certificate in target cluster with the same name (because both cluster have the
same name), we need to create the new secret with a different name. Remove the data not needed and
clean the secret to be created in the target cluster.

```shell
oc get secret migration-user-tls -o yaml > source-secrets/migration-user-tls.yaml
```

Remove the data not needed and clean the secret to be created in the target cluster, and rename the
source secret name to `event-bus-source-cluster-ca-cert`:

```shell
oc apply -f ./source-secrets/
```

Finally we deploy the MirrorMaker2 as:

```shell
oc apply -f event-bus-mirror-maker2.yml
```

To confirm the status of the MirrorMaker2:

```shell
❯ oc get kafkamirrormaker2
NAME                     DESIRED REPLICAS   READY
event-bus-mm2-migrator   1                  True
```

Define a active (source) to passive (target) cluster.

This MirrorMaker will mirror messages coming from `apps.sample.greetings` and `apps.sample.greetings` topics
from the source to the target Kafka Cluster. In general any topic and data created in the source Kafka cluster
will be migrated into the target Kafka cluster.

* [Kafka MirrorMaker 2.0 Configuration](https://strimzi.io/docs/operators/latest/using.html#assembly-mirrormaker-str)
