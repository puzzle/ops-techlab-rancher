## Lab 1.2: Host Preparation

Check [Node Requirements](https://rancher.com/docs/rancher/v2.x/en/installation/requirements/) and [Create Nodes and Load Balancer](https://rancher.com/docs/rancher/v2.x/en/installation/ha/create-nodes-lb/).

Our VMs already have Docker installed and the Loadalancer VM already have a running instance of nginx forwarding traffic on Port `TCP/80` and `TCP/443` to the 3 Rancher VMs `userX-rancher[1-3]` for the Rancher Control Plane.

### Download RKE, Helm, kubectl

For the installation of the Rancher Control Plane we need [RKE](https://rancher.com/docs/rke/latest/en/) and [Helm](https://helm.sh/). `kubectl` is used if you want to access your Kubernetes cluster from your console.


#### Download RKE

Open a Terminal in your web-based IDE, or SSH into your userX-controller VM. You can download the [RKE Binary](https://github.com/rancher/rke/releases/tag/v1.1.4) from Github.

Use the following commands:

```
wget https://github.com/rancher/rke/releases/download/v1.1.4/rke_linux-amd64
chmod +x ./rke_linux-amd64 
sudo mv rke_linux-amd64 /usr/local/bin/rke
rke --version
```

#### Download Helm

The [Helm binary](https://github.com/helm/helm/releases/tag/v3.2.4) can also be downloaded from Github:

Use the following commands:

```
wget https://get.helm.sh/helm-v3.2.4-linux-amd64.tar.gz
tar xzf helm-v3.2.4-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm
helm version
```
#### Download kubectl

And last, download the latest kubectl binary with the folloging command:

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

You are now ready to begin our Rancher Control Plane installation.

---

**End of Lab 1.2**

<p width="100px" align="right"><a href="13_deploywithrke.md">1.3 Deploy Kubernetes cluster for Rancher HA installation →</a></p>

[← back to the Chapter Overview](10_rancher.md)
