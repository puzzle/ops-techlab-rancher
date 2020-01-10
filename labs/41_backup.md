# Lab 4.1: Rancher Server Backup

To backup your Rancher installation you have to take snapshots of your etcd database. You can use these snapshots later to recover from a disaster scenario. There are two ways to take snapshots: recurringly, or as a one-off. Each option is better suited to a specific use case. Read the short description below each link to know when to use each option.

* Option A: Recurring Snapshots
After you stand up a high-availability Rancher install, we recommend configuring RKE to automatically take recurring snapshots so that you always have a safe restoration point available.

* Option B: One-Time Snapshots
We advise taking one-time snapshots before events like upgrades or restoration of another snapshot.


## Enable recurring snapshots

We are going to enable recuring snapshots on our kubernetes cluster. For this, change your `rancher-cluster.yml ` file and add the snapshot configuration. See [Rancher documentation](https://rancher.com/docs/rancher/v2.x/en/backups/backups/ha-backups/#option-a-recurring-snapshots) for more details.

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

kubernetes_version: "v1.14.9-rancher1-1"

services:
  etcd:
    snapshot: true
    interval_hours: 6h
    retention: 24h
```

Run `rke up` to reconfigure your cluster.

```
rke up --config ./rancher-cluster.yml
```
Rancher does now create a etcd snapshot every 6 hours and saves it locally in the following directory: `/opt/rke/etcd-snapshots/`. From there you can use your existing backup solution. You can also configure Rancher to upload the etcd snapshots directly to an S3 Bucket.


## One-Time Snapshots

When you’re about to upgrade Rancher or restore it to a previous snapshot, you should snapshot your live image so that you have a backup of etcd in its last known state.


The one-time snapshot is created with `rke`. Run the following command to create a new etcd snapshot:

```
rke etcd snapshot-save --name <SNAPSHOT.db> --config rancher-cluster.yml
```

Make sure to run the command in the same directory as your `rancher-cluster.yml` file.



---

<p width="100px" align="right"><a href="41_restore.md">4.2 Rancher Server Restoration →</a></p>

[← back to the Labs Overview](../README.md)
