# Lab 4.5: Backup Volumes

Inside your etcd snapshots all your Kubernetes object are store. This allows you to recreate all deployments etc. But it does not contain any data from your volumes! Persistent storage is not backuped with the previously explained methods.

For the backup of your persistent volumes you have to rely on your storage integration. Alternativly you can use solutions like [Velero](https://velero.io/)

Velero can use [restic](https://restic.net/) to create backups from your Peristent Volumes in cases where velero does not have a supported storage provider. As we are using Longhorn in this lab setup, we are using Restic for this Velero installation.


## Install Minio as a S3 Object store for your Backup

In order to use velero with a S3 Backup, we first are going to install [Minio](https://min.io/). We are going to install Minio via the Rancher App Catalog. Go to the App Catalog in your Kubernetes cluster and launch a new App. Search for minio.

![Minio App](../resources/images/minioapp.png)

Use the following anwers for your App:

```yaml
#ingress:
#  enabled: true
#  hosts: tbd!!
replicas: 1
accessKey: AKIAIOSFODNN7EXAMPLE
secretKey: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
buckets:
- velero

```

**Note:** this is far away from a production setup! Don't use this in production!

## Install Velero

The `velero` binary is already included on your vm's. Lets start with creating a new Namespace inside your default Rancher project. The name of the namespace should be `velero`:

![Add Namespace](../resources/images/addnamespace.png)

Click on "Add Namespace" and type `velero` name and then click on the "Create" Button.

![Create Namespace](../resources/images/createnamespace.png)



Then we have to create a file named `credentials-velero` with the following content:

```
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

this allows velero to store the backups in your Minio Installation.

Then you can install velero on your kubernetes cluster with the following command:

```bash 
velero install --provider aws --plugins velero/velero-plugin-for-aws:v1.0.0 --bucket pitc-k8s-staging-velero --secret-file ./credentials-velero --backup-location-config region=us-east-1,s3Url=https://TBD!!!! --use-restic --use-volume-snapshots=false
```

In your `velero` namespace you should now see a `velero` deployment and a `restic` daemonset.

![Velero](../resources/images/verifyvelero.png)

Velero is now ready to create backup of all your Kubernetes objects and also all your Persistent Volumes.





## Create a backup

You can now create a one-time backup using the `velero` cli:

```
velero backup create mybackup
```

You can verify your backup with `velero backup get` or show details with `velero backup describe mybackup --details`

This backup did not yet include any persistent volumes. You have to annotate all pods with volumes to be backuped with the following:

```
backup.velero.io/backup-volumes=pvc-volume
```

then the volume with name `pvc-volume` will be backuped with restic.

Example Pod with annotation for velero:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample
  namespace: foo
  annotations:
    backup.velero.io/backup-volumes=pvc-volume
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-webserver
    volumeMounts:
    - name: pvc-volume
      mountPath: /volume-1
  volumes:
  - name: pvc-volume
    persistentVolumeClaim:
      claimName: test-volume-claim

```

## Restore from a backup

**End of Lab 4.5**

---

<p width="100px" align="right"><a href="50_monitoringlogging.md">5 Monitoring and Logging →</a></p>

[← back to the Labs Overview](../README.md)
