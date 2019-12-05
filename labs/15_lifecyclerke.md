## Lab 1.5: Lifecycle Kubernetes cluster for Rancher HA installation

Upgrade of a Kubernetes Cluster created by `rke` is as easy as changing the `kubernetes_version` in your `rancher-cluster.yml` file and rerun `rke up`


### Upgrade your Rancher Control Plane Kubernetes Cluster

We want to upgrade our existing Kubernetes which currently is on version v1.14.9 to version v1.16.3.

Change your `rancher-cluster.yml` file to:

```
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

kubernetes_version: "v1.16.3-rancher1-1"

services:
  etcd:
    snapshot: true
    creation: 6h
    retention: 24h
```

and then run `rke up` again:

```
$ rke up --config ./rancher-cluster.yml
```

After `rke` is done, verify your cluster:

```
$ kubectl get nodes
NAME                          STATUS    ROLES                      AGE       VERSION
userX-rancher1.xip.puzzle.ch                Ready     controlplane,etcd,worker   11m       v1.16.3
userX-rancher2.xip.puzzle.ch              Ready     controlplane,etcd,worker   11m       v1.16.3
userX-rancher3.xip.puzzle.ch               Ready     controlplane,etcd,worker   11m       v1.16.3
```

---

**End of Lab 1.5**

<p width="100px" align="right"><a href="16_lifecyclerancher.md">1.6 Lifecycle Rancher →</a></p>

[← back to the Chapter Overview](10_rancher.md)
