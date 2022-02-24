# Lab 03 - Namespaces

Kubernetes supports multiple virtual clusters backed by the same physical
cluster. These virtual clusters are called namespaces.

Namespaces make it possible to run different environments on a single Kubernetes
cluster, such as DEV, TST and UAT.  Namespace can also be used to set different
access controls per namespace.

Most objects in Kubernetes can be namespaced (pods, services, pvc,...), keep in
mind however that some objects however cannot be namespaced and are cluster-wide
(pv for example).

## Task 1: Listing namespaces

To see which namespaces are available use the `kubectl get namespaces` command:

```
kubectl get namespaces

---

NAME              STATUS   AGE
default           Active   44h
kube-system       Active   44h
kube-public       Active   44h
kube-node-lease   Active   44h
```

## Task 2: Creating a new namespace

Creating a namespace is easy, `kubectl create namespace <namespacename>`:

```
kubectl create namespace test

---

namespace/test created
```

Check that your namespace has been created:

```
kubectl get ns

---

NAME              STATUS   AGE
default           Active   44h
kube-system       Active   44h
kube-public       Active   44h
kube-node-lease   Active   44h
test              Active   22s
```

> NOTE: as you can see we abreviated `namespace` to `ns`, most of the objects in
> Kubernetes have abreviations that you can use on the command line

## Task 3: Specifying namespaces

When working with namespaces it is important to know that if you do not specify
a specific namepace when issuing a `kubectl` command the default namespace of
your current context is assumed.  In our case this will be `default` but you can
of course create new and/or customize your context.

This means that running the following command:

```
kubectl get all

---

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   44h
```

Is exactly the same as running:

```
kubectl get all -n default

---

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   44h
```

But if we do this for a different namespace we of course get a completely
different result:

```
kubectl get all -n kube-system

---

NAME                                          READY   STATUS      RESTARTS      AGE
pod/helm-install-traefik-crd--1-rqnhj         0/1     Completed   0             44h
pod/helm-install-traefik--1-njmxv             0/1     Completed   2             44h
pod/local-path-provisioner-84bb864455-hwxtl   1/1     Running     2 (42m ago)   44h
pod/svclb-traefik-kkrr8                       2/2     Running     4 (43m ago)   44h
pod/coredns-96cc4f57d-xjk5r                   1/1     Running     2 (43m ago)   44h
pod/metrics-server-ff9dbcb6c-mltrt            1/1     Running     2 (43m ago)   44h
pod/traefik-55fdc6d984-7glvw                  1/1     Running     2 (43m ago)   44h

NAME                     TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/kube-dns         ClusterIP      10.43.0.10      <none>        53/UDP,53/TCP,9153/TCP       44h
service/metrics-server   ClusterIP      10.43.47.95     <none>        443/TCP                      44h
service/traefik          LoadBalancer   10.43.107.101   172.20.10.3   80:30211/TCP,443:32135/TCP   44h
```

## Task 4: All namespaces

It could happen that you do not really know in which namespace a Kubernetes
object is running.  If that is the case you can always add the
`--all-namespaces` option to your command to list objects from all of the
namespaces.

```
kubectl get pods --all-namespaces

---

kube-system   helm-install-traefik-crd--1-rqnhj         0/1     Completed   0             44h
kube-system   helm-install-traefik--1-njmxv             0/1     Completed   2             44h
kube-system   local-path-provisioner-84bb864455-hwxtl   1/1     Running     2 (44m ago)   44h
kube-system   svclb-traefik-kkrr8                       2/2     Running     4 (44m ago)   44h
kube-system   coredns-96cc4f57d-xjk5r                   1/1     Running     2 (44m ago)   44h
kube-system   metrics-server-ff9dbcb6c-mltrt            1/1     Running     2 (44m ago)   44h
kube-system   traefik-55fdc6d984-7glvw                  1/1     Running     2 (44m ago)   44h
```

Note that `--all-namespaces` can also be abbreviated to `-A`:

```
kubectl get pods -A

---

kube-system   helm-install-traefik-crd--1-rqnhj         0/1     Completed   0             44h
kube-system   helm-install-traefik--1-njmxv             0/1     Completed   2             44h
kube-system   local-path-provisioner-84bb864455-hwxtl   1/1     Running     2 (44m ago)   44h
kube-system   svclb-traefik-kkrr8                       2/2     Running     4 (44m ago)   44h
kube-system   coredns-96cc4f57d-xjk5r                   1/1     Running     2 (44m ago)   44h
kube-system   metrics-server-ff9dbcb6c-mltrt            1/1     Running     2 (44m ago)   44h
kube-system   traefik-55fdc6d984-7glvw                  1/1     Running     2 (44m ago)   44h
```

## Task 5: Changing the default context

As mentioned above, we can can change the behaviour that when no namespace is
specified, the default namespace is assumed.  This is done by changing the
context.

Standard behaviour is that default namespace is automatically assumed, let us
verify this first:

```
kubectl get all

---

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   21h
```

Now change the "default" namespace to `kube-system`:

```
kubectl config set-context --current --namespace=kube-system

---

Context "rancher-desktop" modified.
```

If we now run `kubectl get all` without specifying a namespace we get all the
objects of the `kube-system` namespace:

```
kubectl get all

---

NAME                                          READY   STATUS      RESTARTS      AGE
pod/helm-install-traefik-crd--1-rqnhj         0/1     Completed   0             44h
pod/helm-install-traefik--1-njmxv             0/1     Completed   2             44h
pod/local-path-provisioner-84bb864455-hwxtl   1/1     Running     2 (45m ago)   44h
pod/svclb-traefik-kkrr8                       2/2     Running     4 (45m ago)   44h
pod/coredns-96cc4f57d-xjk5r                   1/1     Running     2 (45m ago)   44h
pod/metrics-server-ff9dbcb6c-mltrt            1/1     Running     2 (45m ago)   44h
pod/traefik-55fdc6d984-7glvw                  1/1     Running     2 (45m ago)   44h

NAME                     TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/kube-dns         ClusterIP      10.43.0.10      <none>        53/UDP,53/TCP,9153/TCP       44h
service/metrics-server   ClusterIP      10.43.47.95     <none>        443/TCP                      44h
service/traefik          LoadBalancer   10.43.107.101   172.20.10.3   80:30211/TCP,443:32135/TCP   44h
```

Of course we can still get the objects from the default namespace by specifying
it specifically:

```
kubectl get all -n default

---

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   44h
```

Now change the context again to its normal behavior:

```
kubectl config set-context --current --namespace=default

---

Context "rancher-desktop" modified.
```

## Task 6: Deleting namespaces

Deleting a namespace is very easy, keep in mind however that when you delete a
namespace *all* the objects in that namespace will be deleten. So always verify
that all the objects in that namespace can be deleted:

```
kubectl delete ns test

---

namespace "test" deleted
```

> NOTE: keep in mind that when you delete a namespace with many resources in it, it can take a while before everything is deleted