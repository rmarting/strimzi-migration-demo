# Apache Kafka Cluster

Login as normal user:

```shell
oc login -u user
```

The Kafka Cluster is described in the ```event-bus-kafka.yml``` file.

You can deploy or update the Kafka cluster with the following command:

```shell
oc apply -f ./configmap/
oc apply -f event-bus-kafka.yml
```

This cluster is available with the following services:

```shell
$ oc get svc
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                               AGE
event-bus-kafka-bootstrap                 ClusterIP   172.30.220.206   <none>        9091/TCP,9092/TCP,9093/TCP            85m
event-bus-kafka-brokers                   ClusterIP   None             <none>        9090/TCP,9091/TCP,9092/TCP,9093/TCP   85m
event-bus-zookeeper-client                ClusterIP   172.30.116.168   <none>        2181/TCP                              87m
event-bus-zookeeper-nodes                 ClusterIP   None             <none>        2181/TCP,2888/TCP,3888/TCP            87m
```

The ```event-bus-kafka-bootstrap``` service is used to connect the producers and consumers with this cluster.

* Port ```9092```: Unsecured port
* Port ```9093```: Secured port
* Port ```9404```: Metrics port

We could check the status of this Apache Kafka cluster with:

```shell
❯ oc get kafka
NAME        DESIRED KAFKA REPLICAS   DESIRED ZK REPLICAS   READY   WARNINGS
event-bus   3                        3                     True    
```

To describe the Kafka:

```shell
oc get kafka event-bus -o yaml
```

The following pods will be deployed:

```shell
❯ oc get pod
NAME                                                    READY   STATUS    RESTARTS   AGE
amq-streams-cluster-operator-v1.8.0-66df9f665f-vlvhh    1/1     Running   0          89m
event-bus-entity-operator-845cb76bdf-qh95r              3/3     Running   0          84m
event-bus-kafka-0                                       1/1     Running   0          86m
event-bus-kafka-1                                       1/1     Running   0          86m
event-bus-kafka-2                                       1/1     Running   0          86m
event-bus-kafka-exporter-5d75b58fc4-v6cnp               1/1     Running   0          83m
event-bus-zookeeper-0                                   1/1     Running   0          88m
event-bus-zookeeper-1                                   1/1     Running   0          88m
event-bus-zookeeper-2                                   1/1     Running   0          88m
```

References:

* [Kafka Cluster Configuration](https://access.redhat.com/documentation/en-us/red_hat_amq/2021.q3/html-single/using_amq_streams_on_openshift/index#assembly-config-kafka-str)
