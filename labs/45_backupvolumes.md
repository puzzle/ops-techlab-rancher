# Lab 4.5: Backup Volumes

Inside your etcd snapshots all your Kubernetes object are store. This allows you to recreate all deployments etc. But it does not contain any data from your volumes! Persistent storage is not backuped with the previously explained methods.

For the backup of your persistent volumes you have to rely on your storage integration. Alternativly you can use solutions like [Velero](https://velero.io/)


## Install Minio as a S3 Object store for your Backup

## Install Velero

## Create a backup

## Restore from a backup

---

<p width="100px" align="right"><a href="50_monitoringlogging.md">5 Monitoring and Logging →</a></p>

[← back to the Labs Overview](../README.md)
