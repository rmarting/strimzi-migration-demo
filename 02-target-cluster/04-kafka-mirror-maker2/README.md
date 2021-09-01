# Apache Mirror Maker 2

Kafka MirrorMaker replicates data between Kafka clusters. This process is called mirroring to avoid
confusion with the Kafka partitions replication concept. MirrorMaker consumes messages from the
source cluster and republishes those messages to the target cluster.

From cluster source, it is needed to extract the Cluster CA Cert and the user credentials needed
to connect:

```shell
oc get secret event-bus-cluster-ca-cert -o yaml > source-secrets/event-bus-source-cluster-ca-cert.yaml
```

As there is a certificate in target cluster with the same name, we will create the new secret with a different name. Remove
the data not needed and clean the secret to be created in the target cluster.

```shell
oc get secret migration-user-tls -o yaml > source-secrets/migration-user-tls.yaml
```

Remove the data not needed and clean the secret to be created in the target cluster.

```shell
oc apply -f ./source-secrets/
```

Finally we deploy the Kafka MirrorMaker as:

```shell
oc apply -f event-bus-mirror-maker2.yml
```

To confirm the status of the Kafka MirrorMaker:

```shell
❯ oc get kafkamirrormaker2
NAME                     DESIRED REPLICAS   READY
event-bus-mm2-migrator   1                  True
```

Define a active (source) to passive (target) cluster.

This MirrorMaker will mirror messages coming from `apps.sample.greetings` and `apps.sample.greetings` topics
from the source to the target Kafka Cluster.

* [Deploy Kafka MirrorMaker](https://access.redhat.com/documentation/en-us/red_hat_amq/7.7/html-single/using_amq_streams_on_openshift/index#kafka-mirror-maker-str)
* [Kafka MirrorMaker Configuration](https://access.redhat.com/documentation/en-us/red_hat_amq/7.7/html-single/using_amq_streams_on_openshift/index#assembly-deployment-configuration-kafka-mirror-maker-str)
* [Kafka MirrorMaker 2.0 Configuration](https://access.redhat.com/documentation/en-us/red_hat_amq/7.7/html-single/using_amq_streams_on_openshift/index#assembly-mirrormaker-str)
