# Kafka Users
## Kafka Cluster Security

AMQ Streams supports authentication and authorization. Authentication can be configured independently
for each listener. Authorization is always configured for the whole Kafka cluster.

References:

* [Managing secure access to Kafka](https://strimzi.io/docs/operators/latest/using.html#assembly-securing-access-str)

## Kafka Users

Kafka users could be defined with ```KafkaUser``` definition. The user could includes
the authorization policies (ACLs) of the different resources in the Kafka cluster.

The following users could be defined:

* **admin-user-scram**: Super-user (using scram-sha-512 authentication) to administrate the Kafka
cluster. Definition [here](./users/admin-user-scram.yml).
* **admin-user-tls**: Super-user (using TLS authentication) to administrate the Kafka
cluster. Definition [here](./users/admin-user-tls.yml).
* **migration-user-tls**: Super-user (using TLS authentication) to migrate data of the Kafka
cluster. Definition [here](./users/migration-user-tls.yml).
* **sample-user-scram**: User (using scram-sha-512 authentication) to produce and consume records
into ```apps.samples.greetings``` topic. Definition [here](./users/sample-user-scram.yml).
* **sample-user-tls**: User (using TLS authentication) to produce and consume records
from ```apps.samples.greetings``` topic. Definition [here](./users/sample-user-tls.yml).
* **sample-streams-user-tls**: User to produce and consume records to and from ```app.samples.greetings.*``` topics.
Definition [here](./users/sample-streams-user-tls.yml).

To create the users:

```shell
oc apply -f ./users/
```

This command will show the status of the Kafka Users:

```shell
❯ oc get kafkausers
NAME                      CLUSTER     AUTHENTICATION   AUTHORIZATION   READY
admin-user-scram          event-bus   scram-sha-512                    True
admin-user-tls            event-bus   tls                              True
migration-user-tls        event-bus   tls                              True
sample-streams-user-tls   event-bus   tls              simple          True
sample-user-scram         event-bus   scram-sha-512    simple          True
sample-user-tls           event-bus   tls              simple          True
```

To describe a Kafka User:

```shell
oc get kafkauser sample-user-scram -o yaml
```

Each user will have its own secret with the credentials defined it:

```shell
❯ oc get secret sample-user-scram -o yaml
apiVersion: v1
data:
  password: ZHYwV1V5eUx6Y09x
kind: Secret
metadata:
  labels:
    app.kubernetes.io/instance: sample-user-scram
    app.kubernetes.io/managed-by: strimzi-user-operator
    app.kubernetes.io/name: strimzi-user-operator
    app.kubernetes.io/part-of: strimzi-sample-user-scram
    strimzi.io/cluster: event-bus
    strimzi.io/kind: KafkaUser
  name: sample-user-scram
  namespace: strimzi-migration
type: Opaque
```

To decrypt the password:

```shell
❯ oc get secret sample-user-scram -o jsonpath='{.data.password}' | base64 -d
JVDq4gwNjIeU
```

These users could be tested with the following sample:

* Sample consumer authenticated with the ```sample-user-scram``` user:

```shell
oc run kafka-consumer -ti --image=quay.io/strimzi/kafka:latest-kafka-2.5.0 --rm=true --restart=Never -- /bin/bash -c "cat >/tmp/consumer.properties <<EOF 
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=sample-user-scram password=JVDq4gwNjIeU;
EOF
bin/kafka-console-consumer.sh --bootstrap-server event-bus-kafka-bootstrap:9092 --topic apps.samples.greetings --consumer.config=/tmp/consumer.properties --group sample-group
"
```

* Sample producer authenticated with the ```sample-user-scram``` user:

```shell
oc run kafka-producer -ti --image=quay.io/strimzi/kafka:latest-kafka-2.5.0 --rm=true --restart=Never -- /bin/bash -c "cat >/tmp/producer.properties <<EOF 
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=sample-user-scram password=JVDq4gwNjIeU;
EOF
bin/kafka-console-producer.sh --broker-list event-bus-kafka-bootstrap:9092 --topic apps.samples.greetings --producer.config=/tmp/producer.properties
"
```

References:

* [Using the User Operator](https://strimzi.io/docs/operators/latest/using.html#assembly-using-the-user-operator-str)
