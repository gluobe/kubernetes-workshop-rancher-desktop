# Lab 01 - Rancher Desktop

For these labs we will use [Rancher Desktop](https://rancherdesktop.io/) as our Kubernetes environment. This is
going to run locally on a virtual machine on your laptop.

Rancher Desktop is a tool that makes it easy to run Kubernetes (and Docker) locally. Rancher Desktop runs a 
single-node Kubernetes cluster inside a VM on your laptop for users looking to 
try out Kubernetes or develop with it day-to-day.

## Task 1: Installing Rancher Desktop

Folllow the the Rancher Desktop installation instruction from the following website:

https://docs.rancherdesktop.io/getting-started/installation

Validate that Rancher Desktop has been installed succesfully and Kubernetes is running by issuesing the command below and validating your output looks similar:

```
kubectl get nodes

---

NAME                   STATUS   ROLES                  AGE   VERSION
lima-rancher-desktop   Ready    control-plane,master   44h   v1.22.6+k3s1
```

> NOTE: you exact output might differ a little, but important is that you see a single node and that it is in the `Ready` status, should it not yet be ready wait a minute and reissue the `kubectl get nodes` command