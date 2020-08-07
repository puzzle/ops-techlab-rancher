## Lab 1.5: Lifecycle Kubernetes cluster for Rancher HA installation

Upgrade of a Kubernetes Cluster created by `rke` is as easy as changing the `kubernetes_version` in your `rancher-cluster.yml` file and rerun `rke up`


### Upgrade your Rancher Control Plane Kubernetes Cluster

We want to upgrade our existing Kubernetes which currently is on version v1.17.9 to version v1.18.6.

Change your `rancher-cluster.yml` file to:


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

kubernetes_version: "v1.18.6-rancher1-1"
```

and then run `rke up` again:

```bash
$ rke up --config ./rancher-cluster.yml
```

After `rke` is done, verify your cluster:

```bash
$ kubectl get nodes
NAME                          STATUS    ROLES                      AGE       VERSION
userX-rancher1.xip.puzzle.ch  Ready     controlplane,etcd,worker   11m       v1.18.6
userX-rancher2.xip.puzzle.ch  Ready     controlplane,etcd,worker   11m       v1.18.6
userX-rancher3.xip.puzzle.ch  Ready     controlplane,etcd,worker   11m       v1.18.6
```

The update can take some minutes, so wait until you can access your Rancher cluster again. This update does also update the canal SDN. Make also sure, that these pods have been redeployed and are running and ready again:

```bash
$ kubectl get pod --all-namespaces
NAMESPACE       NAME                                       READY   STATUS      RESTARTS   AGE
cattle-system   cattle-cluster-agent-86485cbffb-zqwtc      1/1     Running     0          8m16s
cattle-system   cattle-node-agent-csk92                    1/1     Running     0          8m15s
cattle-system   cattle-node-agent-ftskx                    1/1     Running     0          8m15s
cattle-system   cattle-node-agent-jhxxm                    1/1     Running     0          8m15s
cattle-system   rancher-767cc6f696-c9bts                   1/1     Running     0          10m
cattle-system   rancher-767cc6f696-h5g6w                   1/1     Running     0          10m
cattle-system   rancher-767cc6f696-s94tc                   1/1     Running     0          10m
cert-manager    cert-manager-69779b98cd-w7fgh              1/1     Running     0          12m
cert-manager    cert-manager-cainjector-7c4c4bbbb9-qjr5x   1/1     Running     0          12m
cert-manager    cert-manager-webhook-6496b996cb-tq87c      1/1     Running     0          12m
ingress-nginx   default-http-backend-67cf578fc4-h6dfb      1/1     Running     0          16h
ingress-nginx   nginx-ingress-controller-brqp2             1/1     Running     0          16h
ingress-nginx   nginx-ingress-controller-gd8vm             1/1     Running     0          16h
ingress-nginx   nginx-ingress-controller-q55jg             1/1     Running     0          16h
kube-system     canal-82dkf                                2/2     Running     0          16h
kube-system     canal-h8p26                                2/2     Running     0          16h
kube-system     canal-xr65n                                2/2     Running     0          16h
kube-system     coredns-849545576b-q8k9z                   1/1     Running     0          2m
kube-system     coredns-849545576b-x4k8f                   1/1     Running     0          112s
kube-system     coredns-autoscaler-65bfc8d47d-4vm9x        1/1     Running     0          16h
kube-system     metrics-server-6b55c64f86-4rrt4            1/1     Running     0          16h
kube-system     rke-coredns-addon-deploy-job-fcnfm         0/1     Completed   0          2m3s
kube-system     rke-ingress-controller-deploy-job-sjq5b    0/1     Completed   0          16h
kube-system     rke-metrics-addon-deploy-job-zlfft         0/1     Completed   0          16h
kube-system     rke-network-plugin-deploy-job-z8x8r        0/1     Completed   0          16h
```

---

**End of Lab 1.5**

<p width="100px" align="right"><a href="16_lifecyclerancher.md">1.6 Lifecycle Rancher →</a></p>

[← back to the Chapter Overview](10_rancher.md)
