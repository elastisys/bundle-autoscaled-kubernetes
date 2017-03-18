# Autoscaled Kubernetes bundle

This bundle is a minimal deployment of an autoscaled Kubernetes using the
[Elastisys CharmScaler](https://jujucharms.com/u/elastisys/charmscaler/).

# Overview

The current version autoscales the number of k8s workers running in your
cluster depending on the avarage CPU usage over the worker machines.

# Getting started

Deploy the bundle

    juju deploy autoscaled-kubernetes

Configure the CharmScaler to give it access to the Juju model.

*For more details on the CharmScaler setup process,
[read this](https://github.com/elastisys/layer-charmscaler#quickstart)*

Minimal config.yaml example:

    charmscaler:
      juju_api_endpoint: "[API address]:17070"
      juju_model_uuid: "[uuid]"
      juju_username: "[username]"
      juju_password: "[password]"

Apply the configuration

    juju config charmscaler --file config.yaml

Wait for the deployment to settle

    watch -c juju status --color

Fetch the Kubernetes config and the `kubectl` binary

    mkdir ~/.kube
    juju scp kubernetes-master/0:config ~/.kube/config
    juju scp kubernetes-master/0:kubectl ./kubectl

Make sure things are running

    ./kubectl cluster-info
    ./kubectl get nodes

# Autoscale

As a simple proof of concept create the
[loadgenerator Replication Controller](https://cdn.rawgit.com/elastisys/bundle-autoscaled-kubernetes/master/loadgenerator.yaml).

    ./kubectl create -f https://cdn.rawgit.com/elastisys/bundle-autoscaled-kubernetes/master/loadgenerator.yaml

It will start off by deploying one pod which runs the
[stress tool](https://people.seas.harvard.edu/~apw/stress/).

To increase the load simply scale the replication controller. One pod consumes
one CPU core.

    ./kubectl scale rc loadgenerator --replicas X

By default the CharmScaler will scale the k8s worker at 80% CPU usage, you can
change this to, for example 50%, like this

    juju config charmscaler scaling_cpu_max=50

Wait a few minutes and watch your Kubernetes deployment grow!

-----------------------

For more details read the documentation of the
[Kubernetes core bundle](https://jujucharms.com/kubernetes-core/) and the
[CharmScaler](https://jujucharms.com/u/elastisys/charmscaler/).
