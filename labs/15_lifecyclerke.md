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

The update can take some minutes, so wait until you can access your Rancher cluster again. This update does also update the canal SDN. You make also sure, that these pods have been redeployed and are running and ready again:

```
kubectl get pod --all-namespaces 
NAMESPACE       NAME                                       READY   STATUS      RESTARTS   AGE
cattle-system   cattle-cluster-agent-c99d657f-7wmk2        1/1     Running     6          18m
cattle-system   cattle-node-agent-267bp                    1/1     Running     4          18m
cattle-system   cattle-node-agent-2qnnc                    1/1     Running     4          18m
cattle-system   cattle-node-agent-jcq5t                    1/1     Running     5          18m
cattle-system   rancher-c57789b58-x25hz                    1/1     Running     3          21m
cattle-system   rancher-c57789b58-xpqmm                    1/1     Running     3          21m
cattle-system   rancher-c57789b58-xqt7r                    1/1     Running     3          21m
cert-manager    cert-manager-769498897f-2w7mz              1/1     Running     1          24m
cert-manager    cert-manager-cainjector-6cc6c4d64d-chxxt   1/1     Running     3          24m
cert-manager    cert-manager-webhook-75947c6ddc-wt5sg      1/1     Running     1          24m
ingress-nginx   default-http-backend-6ffd4cbc89-nl9zx      1/1     Running     1          29m
ingress-nginx   nginx-ingress-controller-bcjjx             1/1     Running     1          29m
ingress-nginx   nginx-ingress-controller-f96wm             1/1     Running     1          29m
ingress-nginx   nginx-ingress-controller-hwhkd             1/1     Running     1          29m
kube-system     canal-l4x9x                                2/2     Running     0          3m45s
kube-system     canal-pdnh9                                1/2     Running     0          79s
kube-system     canal-xrx87                                2/2     Running     0          2m25s
kube-system     coredns-5c59fd465f-4ctxk                   1/1     Running     0          3m30s
kube-system     coredns-autoscaler-d765c8497-jzrwl         1/1     Running     0          3m29s
kube-system     metrics-server-64f6dffb84-6pvg9            1/1     Running     0          3m12s
kube-system     rke-coredns-addon-deploy-job-qv5hm         0/1     Completed   0          3m35s
kube-system     rke-ingress-controller-deploy-job-x6wbb    0/1     Completed   0          29m
kube-system     rke-metrics-addon-deploy-job-pjbgt         0/1     Completed   0          3m19s
kube-system     rke-network-plugin-deploy-job-bvqsg        0/1     Completed   0          3m51s
```

---

**End of Lab 1.5**

<p width="100px" align="right"><a href="16_lifecyclerancher.md">1.6 Lifecycle Rancher →</a></p>

[← back to the Chapter Overview](10_rancher.md)
