# Kafka Users

## Kafka Users

TODO Define a superuser

Kafka users could be defined with ```KafkaUser``` definition. The user could includes
the authorization policies (ACLs) of the different resources in the Kafka cluster.

The following users could be defined:

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
sample-streams-user-tls   event-bus   tls              simple          True
sample-user-tls           event-bus   tls              simple          True
```

To describe the Kafka User:

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
  namespace: amq-streams-migration
type: Opaque
```

To decrypt the password:

```shell
❯ oc get secret sample-user-scram -o jsonpath='{.data.password}' | base64 -d
PIPgj8f11S98
```

These users could be tested with the following sample:

* Sample consumer authenticated with the ```sample-user-scram``` user:

```shell
oc run kafka-consumer -n amq-streams-reg1-workshop -ti --image=registry.redhat.io/amq7/amq-streams-kafka-28-rhel7:1.8.0 --rm=true --restart=Never -- /bin/bash -c "cat >/tmp/consumer.properties <<EOF 
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=sample-user-scram password=PIPgj8f11S98;
EOF
bin/kafka-console-consumer.sh --bootstrap-server event-bus-reg1-kafka-bootstrap:9092 --topic apps.samples.greetings --consumer.config=/tmp/consumer.properties --group sample-group
"
```

* Sample producer authenticated with the ```sample-user-scram``` user:

```shell
oc run kafka-producer -n amq-streams-reg1-workshop -ti --image=registry.redhat.io/amq7/amq-streams-kafka-28-rhel7:1.8.0 --rm=true --restart=Never -- /bin/bash -c "cat >/tmp/producer.properties <<EOF 
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=sample-user-scram password=PIPgj8f11S98;
EOF
bin/kafka-console-producer.sh --broker-list event-bus-reg1-kafka-bootstrap:9092 --topic apps.samples.greetings --producer.config=/tmp/producer.properties
"
```

References:

* [Using the User Operator](https://access.redhat.com/documentation/en-us/red_hat_amq/2020.q4/html-single/using_amq_streams_on_openshift/index#assembly-using-the-user-operator-str)
