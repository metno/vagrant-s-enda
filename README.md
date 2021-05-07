# vagrant-s-enda

Starts the S-ENDA specific software in a one node Kubernetes cluster based on [`k3s`](https://github.com/k3s-io/k3s).

This is meant for testing ingestion of metadata locally. More information about the S-ENDA project on the documentation pages [Welcome to S-ENDA’s documentation!](https://s-enda-documentation.readthedocs.io/en/latest/).

## Getting started

To start the S-ENDA services. Just start the VM as follows.

```bash
vagrant up
```

You have now started a VM with services that you can access from you local machine on the IP `10.10.10.10`. This is a private address and not accessible from  other machines.

### Endpoints

These are the endpoint you have access to.

* [`10.10.10.10`](http://10.10.10.10) --- PyCSW API endpoint

TODO:

* [`10.10.10.10:8080`](http://10.10.10.10:8080) --- MMS API endpoint
* `10.10.10.10:4222 --- MMS NATS server endpoint

### Commands available inside VM

All commands can be run from the `vagrant` user inside the VM. Access the VM by entering it with `vagrant ssh`.

* [`docker`](https://docs.docker.com/engine/reference/commandline/cli/) --- used as backend for `k3s`.
* [`kubectl`](https://github.com/kubernetes/kubectl) --- to interact with Kubernets.
* [`k9s`](https://github.com/derailed/k9s) --- for a terminal menu based interaction with Kubernetes.

## Usage examples

TODO


<!---
vim: set spell spelllang=en:
-->
