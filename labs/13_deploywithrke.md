## Lab 1.3: Deploy Kubernetes cluster for Rancher HA installation

The Rancher Control Plane itself runs on a Kubernetes Cluster. The first step therefore is creating a Kubernetes Cluster on the 3 `userX-rancher[1-3]` VMs.

In this lab we are following the steps in [2. Install Kubernetes with RKE](https://rancher.com/docs/rancher/v2.x/en/installation/ha/kubernetes-rke/).


### Create `rancher-cluster.yml`

Create the file `rancher-cluster.yml` with the following content:

```yaml
nodes:
  - address: userX-rancher1.xip.puzzle.ch
    user: centos
    role: [controlplane,worker,etcd]
  - address: userX-rancher2.xip.puzzle.ch
    user: centos
    role: [controlplane,worker,etcd]
  - address: userX-rancher3.xip.puzzle.ch
    user: centos
    role: [controlplane,worker,etcd]

kubernetes_version: "v1.14.9-rancher1-1"

services:
  etcd:
    snapshot: true
    creation: 6h
    retention: 24h
```

### Create the Kubernetes Cluster

Run `rke up` with the `rancher-cluster.yml` file and wait until the command is done.

```
rke up --config ./rancher-cluster.yml
```

RKE should have created a file `kube_config_rancher-cluster.yml` with the credentials for `kubectl` and helm. 

Verify the Kubernetes Cluster.

```
export KUBECONFIG=$(pwd)/kube_config_rancher-cluster.yml
kubectl get nodes
```

this should give you an output similar to: 

```
NAME                          STATUS    ROLES                      AGE       VERSION
userX-rancher1.xip.puzzle.ch                Ready     controlplane,etcd,worker   11m       v1.14.9
userX-rancher2.xip.puzzle.ch              Ready     controlplane,etcd,worker   11m       v1.14.9
userX-rancher3.xip.puzzle.ch               Ready     controlplane,etcd,worker   11m       v1.14.9
``` 

Make sure you keep a copy of the following files:

* rancher-cluster.yml: The RKE cluster configuration file.
* kube_config_rancher-cluster.yml: The Kubeconfig file for the cluster, this file contains credentials for full access to the cluster.
* rancher-cluster.rkestate: The Kubernetes Cluster State file, this file contains credentials for full access to the cluster.




---

**End of Lab 1.3**

<p width="100px" align="right"><a href="14_install.md">1.4 Install Rancher with Helm →</a></p>

[← back to the Chapter Overview](10_rancher.md)
