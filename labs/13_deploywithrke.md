## Lab 1.3: Deploy Kubernetes cluster for Rancher HA installation

The Rancher Control Plane itself runs on a Kubernetes Cluster. The first step therefore is creating a Kubernetes Cluster on the 3 `userX-rancher[1-3]` VMs.

In this lab we are following the steps in [2. Install Kubernetes with RKE](https://rancher.com/docs/rancher/v2.x/en/installation/k8s-install/kubernetes-rke/).


### Create `rancher-cluster.yml`

In your `/home/ansible/techlab/` directory (on the controller vm), there is a `rancher-cluster.yml` File. The file is already prepared. Check the file and make sure it looks similar (obviously with the correct ips) as this:

```yaml
nodes:
  - address: ip-ranchernode1.xip.puzzle.ch
    user: ansible
    role: [controlplane,worker,etcd]
  - address: ip-ranchernode2.xip.puzzle.ch
    user: ansible
    role: [controlplane,worker,etcd]
  - address: ip-ranchernode3.xip.puzzle.ch
    user: ansible
    role: [controlplane,worker,etcd]

kubernetes_version: "v1.17.9-rancher1-1"
```

**Note:** Check `info.yml` in your techlab folder for the hostnames and IP's of your Rancher node.

### Create the Kubernetes Cluster

Run `rke up` with the `rancher-cluster.yml` file and wait until the command is done:

```
rke up --config ./rancher-cluster.yml
```

RKE should have created a file `kube_config_rancher-cluster.yml` with the credentials for `kubectl` and `helm`. 

Verify the Kubernetes Cluster.

```bash
export KUBECONFIG=$(pwd)/kube_config_rancher-cluster.yml
kubectl get nodes
```

this should give you an output similar to:

```bash
$ kubectl get nodes
NAME                          STATUS    ROLES                      AGE       VERSION
userX-rancher1.xip.puzzle.ch  Ready     controlplane,etcd,worker   11m       v1.17.9
userX-rancher2.xip.puzzle.ch  Ready     controlplane,etcd,worker   11m       v1.17.9
userX-rancher3.xip.puzzle.ch  Ready     controlplane,etcd,worker   11m       v1.17.9
``` 

Make sure you keep a copy of the following files

* `rancher-cluster.yml`: The RKE cluster configuration file.
* `kube_config_rancher-cluster.yml`: The Kubeconfig file for the cluster, this file contains credentials for full access to the cluster.
* `rancher-cluster.rkestate`: The Kubernetes Cluster State file, this file contains credentials for full access to the cluster.

The files mentioned below are needed to maintain, troubleshoot and upgrade your cluster.

Check that all pods are running and ready:

```bash
$ kubectl get pod --all-namespaces
NAMESPACE       NAME                                      READY   STATUS      RESTARTS   AGE
ingress-nginx   default-http-backend-67cf578fc4-h6dfb     1/1     Running     0          2m55s
ingress-nginx   nginx-ingress-controller-brqp2            1/1     Running     0          2m55s
ingress-nginx   nginx-ingress-controller-gd8vm            1/1     Running     0          2m55s
ingress-nginx   nginx-ingress-controller-q55jg            1/1     Running     0          2m55s
kube-system     canal-82dkf                               2/2     Running     0          3m19s
kube-system     canal-h8p26                               2/2     Running     0          3m19s
kube-system     canal-xr65n                               2/2     Running     0          3m19s
kube-system     coredns-7c5566588d-4js7x                  1/1     Running     0          2m17s
kube-system     coredns-7c5566588d-hzkpv                  1/1     Running     0          3m12s
kube-system     coredns-autoscaler-65bfc8d47d-4vm9x       1/1     Running     0          3m10s
kube-system     metrics-server-6b55c64f86-4rrt4           1/1     Running     0          3m5s
kube-system     rke-coredns-addon-deploy-job-7556p        0/1     Completed   0          3m14s
kube-system     rke-ingress-controller-deploy-job-sjq5b   0/1     Completed   0          2m59s
kube-system     rke-metrics-addon-deploy-job-zlfft        0/1     Completed   0          3m9s
kube-system     rke-network-plugin-deploy-job-z8x8r       0/1     Completed   0          3m24s
```

---

**End of Lab 1.3**

<p width="100px" align="right"><a href="14_install.md">1.4 Install Rancher with Helm →</a></p>

[← back to the Chapter Overview](10_rancher.md)
