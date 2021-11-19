# Strimzi Operator

To deploy the Strimzi Operators we need to use an user with ```cluster-admin``` role.

```shell
oc login -u admin-user
```

Deploy the Strimzi Operators with a Subscription from the Operator Hub:

```shell
❯ oc apply -f strimzi-migration-og.yml
❯ oc apply -f strimzi-subscription.yml
```

**NOTE**: This is a *namespaced* installation of the operator.

You could check the status of the subscription with the following commands:

```shell
❯ oc get csv
NAME                               DISPLAY   VERSION   REPLACES   PHASE
strimzi-cluster-operator.v0.22.1   Strimzi   0.22.1               Succeeded
```

[Adding Operators to a cluster](https://docs.openshift.com/container-platform/4.5/operators/olm-adding-operators-to-cluster.html) article
describes deeply how to install Operators using OperatorHub
