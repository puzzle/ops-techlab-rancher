# Lab 4.5: Backup Volumes

Inside your etcd snapshots all your Kubernetes objects are stored. This allows you to recreate all deployments etc. But it does not contain any data from your volumes! Persistent storage is not backed up with the previously explained methods.

For the backup of your persistent volumes you have to rely on your storage integration. Alternatively you can use solutions like [Velero](https://velero.io/)

Velero can use [restic](https://restic.net/) to create backups from your Peristent Volumes in cases where Velero does not have a supported storage provider. As we are using Longhorn in this lab setup, we are using Restic for this Velero installation.


## Install Minio as a S3 Object store for your Backup

In order to use Velero with a S3 Backup, we first are going to install [Minio](https://min.io/). This can be installed via official [Minio Helm chart](https://github.com/minio/charts). For Minio to be available inside the Rancher Catalog, we have to add the Minio Helm chart. Go to your Global Rancher View -> Tools -> Catalogs and click on "Add Calalog". Choose `Minio` as "Name", `https://helm.min.io/` as "Catalog URL" and `Helm v3` as "Helm Version".

![Minio Helm Chart](../resources/images/miniohelmchart.png)

Rancher does then sync all the Helm charts. This might take a few minutes.

![Sync Rancher App Catalog](../resources/images/syncrancherappcatalog.png)

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
persistence:
  size: 5Gi
resources:
  requests:
    memory: 2Gi
```

**Note:** this is far away from a production setup! Don't use this in production!

## Install Velero

Download the `velero` binary.

```
wget https://github.com/vmware-tanzu/velero/releases/download/v1.4.2/velero-v1.4.2-linux-amd64.tar.gz
tar xzf velero-v1.4.2-linux-amd64.tar.gz
sudo mv velero-v1.4.2-linux-amd64/velero /usr/local/bin/
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

![Create Namespace](../resources/images/getopstechlabkubeconfigfile.png)

Save this content to a file called "kube.config" and then use `export KUBECONFIG=kube.config` to configure your kubeconfig for `kubectl`. Remember, it was previously set to the one from the Rancher Control Plane. Check if your `kubectl` points to your `ops-techlab` cluster:

```
$ kubectl get nodes
NAME             STATUS   ROLES                      AGE    VERSION
user1-k8snode1   Ready    controlplane,etcd,worker   170m   v1.18.6
user1-k8snode2   Ready    controlplane,etcd,worker   165m   v1.18.6
user1-k8snode3   Ready    worker                     145m   v1.18.6
```

Then you can install Velero on your Kubernetes cluster with the following command:

```bash 
velero install --provider aws --plugins velero/velero-plugin-for-aws:v1.1.0 --bucket velero --secret-file ./credentials-velero --backup-location-config region=us-east-1,s3Url=http://minio.minio.svc.cluster.local:9000,s3ForcePathStyle=true,publicUrl=http://minio.[ip of k8snode1].xip.puzzle.ch --use-restic --use-volume-snapshots=false
```

In your `velero` namespace you should now see a `velero` deployment and a `restic` daemonset.

![Velero](../resources/images/verifyvelero.png)

Velero is now ready to create backup of all your Kubernetes objects and also all your Persistent Volumes.


## Create a backup

If you just use the command `velero backup create <mybackup>` Velero creates a backup from every Kubernetes resource object which is currently known by the Kubernetes API server. Nevertheless, it's also possible to only backup specific resources by using parameters like `--selector` or `--include-namespaces` for example. Here's an example to only backup the nginx application: `velero backup create nginx-backup --selector app=nginx`

In order to also backup persistent volumes claims you have to annotate the Pod/Deployment with the regarding volume to be backuped with the following annoation:

```
backup.velero.io/backup-volumes=my-volume
```

Then the volume with name `my-volume` will be backuped with restic.

Create an example Pod with the annotation for Velero:

```yaml
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: velero-app-example-pvc
spec:
  storageClassName: longhorn
  resources:
    requests:
      storage: 1Gi
  accessModes:
    - ReadWriteOnce
---
apiVersion: v1
kind: Pod
metadata:
  name: velero-app-example-pod
  namespace: default
  annotations:
    backup.velero.io/backup-volumes: cache-volume
spec:
  containers:
  - image: nginx
    name: test-webserver
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    persistentVolumeClaim:
      claimName: velero-app-example-pvc
```

Enter the nginx example pod using `kubectl exec -it velero-app-example-pod -- bash` and create a file called `/cache/test.txt` with the content `Version 1` using the command: `echo "Version 1" > /cache/test.txt`

You can now create a one-time backup using the `velero` cli:

```
velero backup create mybackup
```

You can verify your backup with `velero backup get` or show details with `velero backup describe mybackup --details`. Wait until the "Status" shows `Completed`.

## Restore from a backup

To simulate the unintentional deletion of an application and its data:
```bash
$ kubectl delete pod velero-app-example-pod
$ kubectl delete pvc velero-app-example-pvc
```

Now it's time to test the restore functionality of Velero. Start a full restore:

```bash
velero restore create myrestore --from-backup mybackup
```

You can verify your restore with `velero restore get` or show details with `velero restore describe myrestore`/`velero restore logs myrestore`. Wait until the "Status" shows `Completed`.

Finally check if the restore was successful:
```bash
$ kubectl get pods
$ kubectl get pvc
$ kubectl exec -it velero-app-example-pod -- bash
$ cat /cache/test.txt
$ exit
```

**End of Lab 4.5**

---

<p width="100px" align="right"><a href="50_monitoringlogging.md">5 Monitoring and Logging →</a></p>

[← back to the Labs Overview](../README.md)
