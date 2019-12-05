## Lab 1.6: Lifecycle Rancher


### Upgrade Rancher Control Plane 

Initially we installed the Rancher Control Plane in Version 2.3.2 and we wan't to upgrade it now to version 2.3.3

The upgrade is done using the same `helm` command as used during the installation of Rancher


```
$ helm upgrade --install  \
  --namespace cattle-system \
  --set hostname=[IP of userX-lb].xip.puzzle.ch \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=ops-techlab-rancher@puzzle.ch
  --version 2.3.3
  rancher
  rancher-latest/rancher:

```

Wait until rollout complets:

```
$ kubectl -n cattle-system rollout status deploy/rancher
```

Open the Rancher Control Plane Url in your Browser and verify that it was upgraded to the new version.

---

**End of Lab 1.6**

<p width="100px" align="right"><a href="20_cluster.md">2. Rancher managed Kubernetes Cluster →</a></p>

[← back to the Chapter Overview](10_rancher.md)
