## Lab 1.3: Deploy Kubernetes cluster for Rancher HA installation

The Rancher Control Plane itself runs on a Kubernetes Cluster. The first step therefore is creating a Kubernetes Cluster on the 3 `userX-rancher[1-3]` VMs.

In this lab we are following the steps in [2. Install Kubernetes with RKE](https://rancher.com/docs/rancher/v2.x/en/installation/ha/kubernetes-rke/).


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
userX-rancher1.xip.puzzle.ch                Ready     controlplane,etcd,worker   11m       v1.14.9
userX-rancher2.xip.puzzle.ch              Ready     controlplane,etcd,worker   11m       v1.14.9
userX-rancher3.xip.puzzle.ch               Ready     controlplane,etcd,worker   11m       v1.14.9
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
ingress-nginx   default-http-backend-6ffd4cbc89-ndrnf     1/1     Running     0          41s
ingress-nginx   nginx-ingress-controller-jknpr            1/1     Running     0          41s
ingress-nginx   nginx-ingress-controller-tr7mt            1/1     Running     0          41s
ingress-nginx   nginx-ingress-controller-x5rnx            1/1     Running     0          41s
kube-system     canal-qsphv                               2/2     Running     0          58s
kube-system     canal-svclr                               2/2     Running     0          58s
kube-system     canal-z9hcp                               2/2     Running     0          58s
kube-system     coredns-6998d84bf5-8gcb5                  1/1     Running     0          52s
kube-system     coredns-autoscaler-f97674f9f-tx845        1/1     Running     0          51s
kube-system     metrics-server-766f4fdf8d-8nmvq           1/1     Running     0          47s
kube-system     rke-coredns-addon-deploy-job-xvvsw        0/1     Completed   0          54s
kube-system     rke-ingress-controller-deploy-job-xmqtb   0/1     Completed   0          43s
kube-system     rke-metrics-addon-deploy-job-885v7        0/1     Completed   0          49s
kube-system     rke-network-plugin-deploy-job-kvhvp       0/1     Completed   0          64s
```

---

**End of Lab 1.3**

<p width="100px" align="right"><a href="14_install.md">1.4 Install Rancher with Helm →</a></p>

[← back to the Chapter Overview](10_rancher.md)
