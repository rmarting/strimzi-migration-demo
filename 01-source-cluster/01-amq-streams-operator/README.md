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

**NOTE**: This is a *namespaced* installation of the operator.

You could check the status of the subscription with the following commands:

```shell
❯ oc get csv
NAME                DISPLAY                             VERSION   REPLACES   PHASE
amqstreams.v1.6.3   Red Hat Integration - AMQ Streams   1.6.3                Succeeded
```

[Adding Operators to a cluster](https://docs.openshift.com/container-platform/4.5/operators/olm-adding-operators-to-cluster.html) article
describes deeply how to install Operators using OperatorHub
