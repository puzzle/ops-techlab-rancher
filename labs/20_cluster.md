# Lab 2: Rancher managed Kubernetes Cluster

Rancher simplifies the creation of clusters by allowing you to create them through the Rancher UI rather than more complex alternatives. Rancher provides multiple options for launching a cluster. Use the option that best fits your use case.

This section assumes a basic familiarity with Docker and Kubernetes. For a brief explanation of how Kubernetes components work together, refer to the [concepts](https://rancher.com/docs/rancher/v2.x/en/overview/concepts/) page.

We are going to create a Rancher launched Kubernetes cluster.

The [Rancher Kubernetes Engine (RKE)](https://rancher.com/docs/rke/latest/en/) allows you to create a Kubernetes cluster on your own nodes. RKE is Rancher’s own lightweight Kubernetes installer.

In RKE clusters, Rancher manages the deployment of Kubernetes. These clusters can be deployed on any bare metal server, cloud provider, or virtualization platform.

These nodes can be dynamically provisioned through Rancher’s UI, which calls [Docker Machine](https://docs.docker.com/machine/) to launch nodes on various cloud providers.

If you already have a node that you want to add to a RKE cluster, you can add it to the cluster by running a Rancher agent container on it.

For more information, refer to the section on [RKE clusters](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/).


## Chapter Content

* [2.1: Provision a new Kubernetes cluster](21_provision.md)
* [2.2: Lifecycle Kubernetes Cluster](22_lifecyclerancher.md)


---

<p width="100px" align="right"><a href="21_provision.md">2.1 Provision a new Kubernetes cluster →</a></p>

[← back to the Labs Overview](../README.md)
