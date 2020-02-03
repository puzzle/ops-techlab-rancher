## Lab 3.6: Persistent Storage

In this lab we are going to install [Longhorn](https://github.com/longhorn/longhorn), a Cloud-Native distributed block storage. Longhorn is a [CNCF Sandbox](https://thenewstack.io/rancher-donates-its-longhorn-kubernetes-persistent-storage-software-to-cncf/) Project, we do not yet recommend this in a production setup. For this lab purpose, its an easy solution to provide storage to your Pods.


## Install Project Longhorn using Rancher App Catalog

Go to your system project and open the `Apps` page and click `Launch`. Search for Longhorn and launch a new Longhorn instance. You can leave all settings on default.

![Project Longhorn](../resources/images/applonghorn.png)

As soon as your Longhorn instance is `Active`, you can open its WebGUI, by clicking on `/index.html` to see all nodes and volumes. There should be no volume at the moment.

![Project Longhorn - App](../resources/images/longhornready.png)

![Project Longhorn - Webgui](../resources/images/longhornwebgui.png)

## Create a new Persistent Volume Claim (PVC)

Longhorn does also create a new Storage Class and marks it as the default storage class.

![Storage Class](../resources/images/storageclasslonghorn.png)

For testing purposes, we are going to create a new Volume. In the Rancher WebGUI go to the "Default" Project and open the page `Resources/Workload`. Then switch to the `Volumes` tab and click on `Add Volume`.

![Add Volume Claim](../resources/images/addvolume.png)

Create a new `test` volume with 1 GiB Size. In the Longhorn Webgui you should see now a new Volume which is Detached (as we have not yet mounted the volume in a pod):

![Longhorn Volume](../resources/images/longhornvolume.png)

## Mount the volume inside a pod

You can now create a new Pod and mount the volume. Create a new Deployment inside your default Namespace. Give it a name `test` and use the `busybox` container image. You can now select the already existing PVC under Volumes (`Add Volume` / `Use existing persistent volume`) and select the previusly created PVC. You can mount the volume at `/mnt`. Launch de deployment and open a shell from the newly created pod to verify the mountpoint (e.g. `df -h`).

![Add Volume](../resources/images/mountvolume.png)

Inside the Longhorn Webgui, you should see that the volume is getting attached and as soon as it is attached you can verify all replicas for this volume.

![Attached Volume](../resources/images/attachedvolume.png)

**End of Lab 3.6**


<p width="100px" align="right"><a href="40_backuprestore.md">4 Backup and Restore→</a></p>

[← back to the Chapter Overview](10_rancher.md)
