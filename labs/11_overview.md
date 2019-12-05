## Lab 1.1: Architectural Overview

### Overview
The figure below illustrates the high-level architecture of Rancher 2.x. The figure depicts a Rancher Server installation that manages two Kubernetes clusters: one created by Rancher Kubernetes Engine (RKE) and another created by Amazon EKS (Elastic Kubernetes Service).

![Rancher Overview](../resources/images/rancher-architecture.svg)

Check https://rancher.com/docs/rancher/v2.x/en/overview/architecture/ for more details.

### Lab Setup
In this lab we are going to install Rancher based on the [High Availability (HA) install](https://rancher.com/docs/rancher/v2.x/en/installation/ha/) Guide.

For this we need 3 VMs for Rancher and 3 VMs for a Kubernetes Cluster we want to provision. So your lab enviornments looks like this

* VM: userX-lb
* VM: userX-rancher[1-3]
* VM: userX-k8snode[1-3]

Make sure you can login into all of the VMs. All the VMs are provisioned on cloudscale.ch and are based on CentOS.




---

**End of Lab 1.1**

<p width="100px" align="right"><a href="12_hostpreparation.md">1.2 Host Preparation →</a></p>

[← back to the Chapter Overview](10_rancher.md)
