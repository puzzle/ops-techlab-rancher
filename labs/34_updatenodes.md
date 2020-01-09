## Lab 3.4: Update Nodes

In order to update kubernetes node, we need to make sure that no application workload is on the node and kubnernetes does not schedule any new pods on this node. Afterwards, updating a node is really part of the kubernetes daily business and should be handled by your existing processes. All kubernetes related components (except for the container runtime) is handled by Rancher.

### Cordon a node

Cordon a node makes the node unscheduable for kubernetes. You can do this inside your Rancher WebGUI or with kubectl.

![Cordon Node](../resources/images/cordonnode.png)

Select the node(s) and then click on de "Cordon" button.

With kubectl use the following command to cordon the node

```
kubectl cordon userX-k8snode3
```

### Drain a node

### Upgrade node

### Uncordon node


**End of Lab 3.4**

<p width="100px" align="right"><a href="40_backuprestore.md"> Backup and Restore→</a></p>

[← back to the Chapter Overview](10_rancher.md)
