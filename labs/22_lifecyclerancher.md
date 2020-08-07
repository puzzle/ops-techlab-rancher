## Lab 2.2: Lifecycle Kubernetes Cluster

In this lab we are going to upgrade our recently created Kubernetes cluster to a newer Kubernetes version. See also the official [documentation](https://rancher.com/docs/rancher/v2.x/en/cluster-admin/upgrading-kubernetes/) from Rancher for more details.

## Upgrade Cluster

Go to your cluster dashboard inside the Rancher web UI and edit the cluster.

![Edit Cluster](../resources/images/editcluster.png)

From there, in the `Cluster Options` / `Kubernetes Options` select the new Kubernetes version you want to upgrade to (`v1.18.6`) and then click `Save`.

![Edit Cluster](../resources/images/changeclusterversion.png)

Rancher does start the upgrade process and will replace all the Kubernetes componets with the updated version.

**Note:** Upgrading a Kubernetes version can also lead to the upgrade of addons like CNI, DNS etc. Always check the release notes and the [System Images](https://rancher.com/docs/rke/latest/en/config-options/system-images/) page or better [k8s_rke_system_images.go](https://github.com/rancher/kontainer-driver-metadata/blob/master/rke/k8s_rke_system_images.go) for details.


![Cluster updating](../resources/images/waitclusterupdate.png)


Wait again, until your cluster update is done.



**End of Lab 2.2**

<p width="100px" align="right"><a href="30_dailybusiness.md"> 3. Daily Business →</a></p>

[← back to the Chapter Overview](10_rancher.md)
