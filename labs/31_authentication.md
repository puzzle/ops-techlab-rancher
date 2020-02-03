## Lab 3.1: Authentication

One of the key features that Rancher adds to Kubernetes is centralized user authentication. This feature allows your users to use one set of credentials to authenticate with any of your Kubernetes clusters.

This centralized user authentication is accomplished using the Rancher authentication proxy, which is installed along with the rest of Rancher. This proxy authenticates your users and forwards their requests to your Kubernetes clusters using a service account.

Check the [Rancher documentation](https://rancher.com/docs/rancher/v2.x/en/admin-settings/authentication/) for more details.

### Optional: Try an external Authentication

You can try to enable [GitHub Authentication](https://rancher.com/docs/rancher/v2.x/en/admin-settings/authentication/github/). Follow the documentation from Rancher.


**End of Lab 3.1**

<p width="100px" align="right"><a href="32_usermanagement.md"> 3.2 User Management →</a></p>

[← back to the Chapter Overview](10_rancher.md)
