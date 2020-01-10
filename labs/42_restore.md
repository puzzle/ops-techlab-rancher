# Lab 4.2: Rancher Server Restoration

With the previously created etcd snapshot you can fully restore your Rancher Control Plane. Check the [Rancher Documentation](https://rancher.com/docs/rancher/v2.x/en/backups/restorations/ha-restoration/) for detailed explenation and a full disaster recovery.


### Restore an etcd Snapshot


The etcd snapshot is restored again with `rke`:

```
rke etcd snapshot-restore --name <snapshot>.db --config ./rancher-cluster-restore.yml
```


---

<p width="100px" align="right"><a href="43_backupcluster.md">4.3 Backing up a Rancher Launched Kubernetes Cluster →</a></p>

[← back to the Labs Overview](../README.md)
