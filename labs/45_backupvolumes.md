# Lab 4.5: Backup Volumes

Inside your etcd snapshots all your Kubernetes objects are stored. This allows you to recreate all deployments etc. But it does not contain any data from your volumes! Persistent storage is not backed up with the previously explained methods.

For the backup of your persistent volumes you have to rely on your storage integration. Alternatively you can use solutions like [Velero](https://velero.io/)

Velero can use [restic](https://restic.net/) to create backups from your Peristent Volumes in cases where Velero does not have a supported storage provider. As we are using Longhorn in this lab setup, we are using Restic for this Velero installation.


## Install Minio as a S3 Object store for your Backup

In order to use Velero with a S3 Backup, we first are going to install [Minio](https://min.io/). This can be installed via the Rancher App Catalog. For Minio to be available inside the Rancher App Catalog, we have to enable the Helm catalog. Go to your Global Rancher View -> Tools -> Catalogs and enable the `helm` catalog. Rancher does then sync all the Helm charts. This might take a few minutes.

![Sync  Rancher App Catalog](../resources/images/syncrancherappcatalog.png)

Go to the App Catalog in your Kubernetes cluster and launch a new App. Search for Minio.

![Minio App](../resources/images/minioapp.png)

Use the following anwers for your App:

```yaml
ingress:
  enabled: true
  hosts: 
  - minio.[ip of k8snode1].xip.puzzle.ch
replicas: 1
accessKey: AKIAIOSFODNN7EXAMPLE
secretKey: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
defaultBucket:
  enabled: true
  name: velero
  policy: none
  purge: false
```

**Note:** this is far away from a production setup! Don't use this in production!

## Install Velero

Download the `velero` binary.

```
wget https://github.com/vmware-tanzu/velero/releases/download/v1.2.0/velero-v1.2.0-linux-amd64.tar.gz
tar xzf velero-v1.2.0-linux-amd64.tar.gz
sudo mv velero-v1.2.0-linux-amd64/velero /usr/local/bin/
velero version --client-only
```


Lets start with creating a new Namespace inside your default Rancher project. The name of the namespace should be `velero`:

![Add Namespace](../resources/images/addnamespace.png)

Click on `Add Namespace` and type `velero` name and then click on the `Create` Button.

![Create Namespace](../resources/images/createnamespace.png)



Then we have to create a file named `credentials-velero` with the following content:

```
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

This allows Velero to store the backups in your Minio Installation.

In order to work with `kubectl` on your `ops-techlab` Kubernetes cluster, you have to download your `kube.config` file from the cluster dashboard.

Then use `export KUBECONFIG=kube.config` to configure your kubeconfig for `kubectl`. Remember, it was previously set to the one from the Rancher Control Plane. Now you can work from your controller VM on your `ops-techlab` cluster:

```
kubectl get nodes
NAME               STATUS   ROLES                      AGE   VERSION
rancher-k8snode1   Ready    controlplane,etcd,worker   46m   v1.16.3
rancher-k8snode2   Ready    controlplane,etcd,worker   42m   v1.16.3
rancher-k8snode3   Ready    controlplane,etcd,worker   42m   v1.16.3
```


Then you can install Velero on your Kubernetes cluster with the following command:

```bash 
velero install --provider aws --plugins velero/velero-plugin-for-aws:v1.0.0 --bucket velero --secret-file ./credentials-velero --backup-location-config region=us-east-1,s3Url=http://minio.minio.svc.cluster.local,s3ForcePathStyle=true,publicUrl=http://minio.[ip of k8snode1].xip.puzzle.ch --use-restic --use-volume-snapshots=false
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

Example Pod with annotation for Velero:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample
  namespace: foo
  annotations:
    backup.velero.io/backup-volumes: pvc-volume
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

Try creating a Deployment with a PVC and make a backup of it.

## Restore from a backup

TODO!

**End of Lab 4.5**

---

<p width="100px" align="right"><a href="50_monitoringlogging.md">5 Monitoring and Logging →</a></p>

[← back to the Labs Overview](../README.md)
