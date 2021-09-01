# Kafka Users

## Kafka Cluster Security

AMQ Streams supports authentication and authorization. Authentication can be configured independently
for each listener. Authorization is always configured for the whole Kafka cluster.

References:

* [Managing secure access to Kafka](https://access.redhat.com/documentation/en-us/red_hat_amq/2021.q3/html-single/using_amq_streams_on_openshift/index#assembly-securing-access-str)

## Kafka Users

Kafka users could be defined with ```KafkaUser``` definition. The user could includes
the authorization policies (ACLs) of the different resources in the Kafka cluster.

The ```sampleuser-user.yml``` file describe a user with some policies.

The following users could be defined:

* **sample-user-scram**: User (using scram-sha-512 authentication) to produce and consume records
into ```apps.samples.greetings``` topic. Definition [here](./users/sampleuser-user.yml).
* **sample-user-tls**: User (using TLS authentication) to produce and consume records
from ```apps.samples.greetings``` topic. Definition [here](./users/sampleusertls-user.yml).
* **sample-streams-user-tls**: User to produce and consume records to and from ```app.samples.greetings.*``` topics.
Definition [here](./users/streamsusertls-user.yml).

To create the users:

```shell
oc apply -f ./users/
```

This command will show the status of the Kafka Users:

```shell
❯ oc get kafkausers
NAME                      CLUSTER     AUTHENTICATION   AUTHORIZATION
admin-user-scram          event-bus   scram-sha-512    
admin-user-tls            event-bus   tls              
migration-user-tls        event-bus   tls              
sample-streams-user-tls   event-bus   tls              simple
sample-user-scram         event-bus   scram-sha-512    simple
sample-user-tls           event-bus   tls              simple
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
oc run kafka-consumer -n amq-streams-reg1-workshop -ti --image=registry.redhat.io/amq7/amq-streams-kafka-25-rhel7:1.5.0 --rm=true --restart=Never -- /bin/bash -c "cat >/tmp/consumer.properties <<EOF 
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=sample-user-scram password=PIPgj8f11S98;
EOF
bin/kafka-console-consumer.sh --bootstrap-server event-bus-reg1-kafka-bootstrap:9092 --topic apps.samples.greetings --consumer.config=/tmp/consumer.properties --group sample-group
"
```

* Sample producer authenticated with the ```sample-user-scram``` user:

```shell
oc run kafka-producer -n amq-streams-reg1-workshop -ti --image=registry.redhat.io/amq7/amq-streams-kafka-25-rhel7:1.5.0 --rm=true --restart=Never -- /bin/bash -c "cat >/tmp/producer.properties <<EOF 
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=sample-user-scram password=PIPgj8f11S98;
EOF
bin/kafka-console-producer.sh --broker-list event-bus-reg1-kafka-bootstrap:9092 --topic apps.samples.greetings --producer.config=/tmp/producer.properties
"
```

References:

* [Using the User Operator](https://access.redhat.com/documentation/en-us/red_hat_amq/2020.q4/html-single/using_amq_streams_on_openshift/index#assembly-using-the-user-operator-str)
