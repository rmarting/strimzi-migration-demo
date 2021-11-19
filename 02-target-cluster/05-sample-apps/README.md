# Verifying MirrorMaker2 

To verify that messages are stored in the target Apache Kafka cluster, we will deploy
a set of applications to check that we have data coming from the source cluster:

```shell
oc apply -f check-apps/
```

These applications are deployed as:

```shell
❯ oc get deployment
NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
hello-world-consumer-check-greetings            1/1     1            1           61m
hello-world-consumer-check-greetings-reversed   1/1     1            1           61m
```

Checking the logs of the pods created we could confirm that we are consuming data in the target cluster
with data produced in the source cluster.

# Migration Consumers

Now we could stop de original consumer from source cluster and start a new one in the target. As the consumers offset
are sync, the new instances only process the latest records not processed in the source cluster.

Execute in the source cluster:

```shell
oc scale --replicas=0 deployment/hello-world-streams
oc scale --replicas=0 deployment/hello-world-consumer
```

Deploy in the target cluster:

```shell
oc apply -f ./consumer-apps/
```

Now you could check that the new consumers in the target cluster are consuming the data starting from the latest
offset processed in the source cluster.

Now, the last step is move your producer apps to this new Kafka cluster.
