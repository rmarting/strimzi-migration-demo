# Red Hat AMQ Streams Operator

To deploy the Red Hat AMQ Streams Operators we need to use an user with ```cluster-admin``` role.

```shell
oc login -u admin-user
```

Deploy the Red Hat AMQ Streams Operators with a Subscription from the Operator Hub:

```shell
❯ oc apply -f amq-streams-migration-og.yml
❯ oc apply -f amq-streams-migration-subscription.yml
```

You could check the status of the subscription with the following commands:

```shell
❯ oc get csv
NAME                DISPLAY                             VERSION   REPLACES            PHASE
amqstreams.v1.8.0   Red Hat Integration - AMQ Streams   1.8.0     amqstreams.v1.7.3   Succeeded
```

[Adding Operators to a cluster](https://docs.openshift.com/container-platform/4.8/operators/admin/olm-adding-operators-to-cluster.html) article
describes deeply how to install Operators using OperatorHub
