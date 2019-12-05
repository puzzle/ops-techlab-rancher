## Lab 1.4: Install Rancher with Helm

We now have a running kubernetes cluster and can deploy the Rancher Control Plane onto this cluster.

### Add the Helm Chart Repositories

We need `https://charts.jetstack.io` for cert-manager and `https://releases.rancher.com/server-charts/`.

```
$ helm repo add jetstack https://charts.jetstack.io
$ helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
$ helm repo update
```

### Install cert-manager

```
$ kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.9/deploy/manifests/00-crds.yam
$ kubectl create namespace cert-manager
$ kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true
$ helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.9.1 \
  jetstack/cert-manager
```

### Install Rancher Control Plane

```
$ helm upgrade --install  \
  --namespace cattle-system \
  --set hostname=[IP of userX-lb].xip.puzzle.ch \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=ops-techlab-rancher@puzzle.ch
  --version 2.3.2
  rancher
  rancher-latest/rancher:

```

Wait until rollout complets:

```
$ kubectl -n cattle-system rollout status deploy/rancher
```

### Initial Config of the Rancher Control Plane

Open a browser and go to https://[IP of userX-lb].xip.puzzle.ch and finish the installation.

* Verify the Rancher URL
* Set inital admin password

---

**End of Lab 1.4**

<p width="100px" align="right"><a href="15_install.md">1.5 Lifecycle Kubernetes cluster for Rancher HA installation →</a></p>

[← back to the Chapter Overview](10_rancher.md)
