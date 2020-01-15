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
$ kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.9/deploy/manifests/00-crds.yaml
$ kubectl create namespace cert-manager
$ kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true
$ helm install \
  cert-manager \
  --namespace cert-manager \
  --version v0.9.1 \
  jetstack/cert-manager
```

Verify with `kubectl -n cert-manager get all` that all the cert-manager pods are running and ready

```
$kubectl -n cert-manager get all
NAME                                           READY   STATUS    RESTARTS   AGE
pod/cert-manager-769498897f-2w7mz              1/1     Running   0          24s
pod/cert-manager-cainjector-6cc6c4d64d-chxxt   1/1     Running   0          24s
pod/cert-manager-webhook-75947c6ddc-wt5sg      1/1     Running   0          24s

NAME                           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/cert-manager-webhook   ClusterIP   10.43.158.235   <none>        443/TCP   24s

NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/cert-manager              1/1     1            1           24s
deployment.apps/cert-manager-cainjector   1/1     1            1           24s
deployment.apps/cert-manager-webhook      1/1     1            1           24s

NAME                                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/cert-manager-769498897f              1         1         1       24s
replicaset.apps/cert-manager-cainjector-6cc6c4d64d   1         1         1       24s
replicaset.apps/cert-manager-webhook-75947c6ddc      1         1         1       24s
```



### Install Rancher Control Plane

```
$ kubectl create namespace cattle-system
$ helm upgrade --install  \
  --namespace cattle-system \
  --set hostname=[IP of userX-lb].xip.puzzle.ch \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=ops-techlab-rancher@puzzle.ch \
  --version 2.3.2 \
  rancher \
  rancher-latest/rancher

```

Wait until rollout complets, this can take some minutes:

```
$ kubectl -n cattle-system rollout status deploy/rancher
Waiting for deployment "rancher" rollout to finish: 0 of 3 updated replicas are available...
Waiting for deployment "rancher" rollout to finish: 1 of 3 updated replicas are available...
Waiting for deployment "rancher" rollout to finish: 2 of 3 updated replicas are available...
deployment "rancher" successfully rolled out
```

Verify all Rancher pods are running and ready.

```
kubectl -n cattle-system get all
NAME                          READY   STATUS    RESTARTS   AGE
pod/rancher-c57789b58-x25hz   0/1     Running   2          81s
pod/rancher-c57789b58-xpqmm   1/1     Running   1          81s
pod/rancher-c57789b58-xqt7r   0/1     Running   2          81s

NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/rancher   ClusterIP   10.43.213.48   <none>        80/TCP    81s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/rancher   1/3     3            1           81s

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/rancher-c57789b58   3         3         1       81s
```

### Initial Config of the Rancher Control Plane

Open a browser and go to https://[IP of userX-lb].xip.puzzle.ch and finish the installation.

* Set inital admin password
* Verify the Rancher URL (should already be correct)


---

**End of Lab 1.4**

<p width="100px" align="right"><a href="15_lifecyclerke.md.md">1.5 Lifecycle Kubernetes cluster for Rancher HA installation →</a></p>

[← back to the Chapter Overview](10_rancher.md)
