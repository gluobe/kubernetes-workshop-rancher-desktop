# Lab 06 - Deployments

Deploying pods is not something you normally do when working with Kubernetes,
instead of interacting with pods directly you would use a higher lever
construct.  Deployments are such a higher level construct, they are usually the
way you would describe your applications.

## Task 0: Creating a namespace

Create a namespace for this lab:

```
kubectl create ns lab-06

---

namespace "lab-06" created
```

## Task 1: Creating a deployment

A very basic deployment looks like this:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: container-info
  labels:
    app: container-info
spec:
  replicas: 1
  selector:
    matchLabels:
      app: container-info
  template:
    metadata:
      labels:
        app: container-info
    spec:
      containers:
      - name: container-info
        image: gluobe/container-info:blue
        ports:
        - containerPort: 80
```

Copy the above into a file `lab-06-deployment.yml` and `kubectl apply` it:

```
kubectl apply -f lab-06-deployment.yml -n lab-06

---

deployment.apps/container-info created
```

Use `kubectl port-forward` to see the deployed application in your local
browser (NOTE: as we are forwarding a deployment we are using `deployment/*` 
instead of `pod/*` like we used in a previous lab):

```
kubectl port-forward deployment/container-info 8080:80 -n lab-06

---

Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

Check out the page: http://localhost:8080

![container-info blue](images/lab-06-container-info.png)

Kill the `kubectl port-forward` process by pressing `CTRL+c`.

## Task 2: Scaling a deployment

Because we are using a deployment we can very easily scale our application from
a Kubernetes point of view (you of course need to ensure that your application
is stateless so it can properly scale).

Scaling your running application is as simple as:

```
kubectl scale deployment container-info --replicas=3 -n lab-06

---

deployment.extensions "container-info" scaled
```

When you do a `kubectl get pods -n lab-06` quickly enough you will see that 
there are 2 additional container-info pods being started (if you are not fast 
enough you will see them in the `Running` state already).

```
kubectl get pods -n lab-06

---

NAME                              READY   STATUS    RESTARTS   AGE
container-info-785bc569f6-4pwvc   1/1     Running   0          99s
container-info-785bc569f6-qwrct   1/1     Running   0          11s
container-info-785bc569f6-z4nvr   1/1     Running   0          11s
```

Scaling down to 1 can be done by re-applying the original YAML:

```
kubectl apply -f lab-06-deployment.yml -n lab-06
```

When you do a `kubectl get pods -n lab-06` you will see 2 pods are
being terminated:

```
kubectl get pods -n lab-06

---

NAME                              READY   STATUS        RESTARTS   AGE
container-info-6d9747978f-5n6z9   1/1     Running       0          27m
container-info-6d9747978f-ndl6n   0/1     Terminating   0          85s
container-info-6d9747978f-vmfdd   0/1     Terminating   0          85s
```

> NOTE: similarly, scaling up can also be done by editing the "replica" field in 
> the YAML

## Task 3: Exposing the deployment via a service

When our deployment consists of multiple pods we can't use the port-forward to
reach all pods. In this scenario we will have to create a service and expose
this service.  Think about a service as a virtual load balancer in front of the 
different pods.

First we will scale the number of pods up again:

```
kubectl scale deployment container-info --replicas=5 -n lab-06

---

deployment.extensions "container-info" scaled
```

Next we create a service for our deployment:

```
kubectl expose deployment container-info --type=NodePort --name=container-info -n lab-06

---

service/container-info exposed
```

To access the service we need the IP of the node and the port of the service.

The IP of the node can be found using the following command:

```
kubectl get nodes -o wide

---
NAME                   STATUS   ROLES                  AGE   VERSION        INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
lima-rancher-desktop   Ready    control-plane,master   45h   v1.22.6+k3s1   172.20.10.3   <none>        Alpine Linux v3.14   5.10.93-0-virt   docker://20.10.11
```

> NOTE: you will need to write down the `INTERNAL-IP`

Next you need the port of the service:

```
kubectl get svc -n lab-06

---

NAME             TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
container-info   NodePort   10.43.23.58   <none>        80:30683/TCP   7m23s
```

> NOTE: you will need to write down the `30XYZ` number in the `PORT(S)` section 

You can now visit your application on:

http://INTERNAL-IP:30XYZ

> NOTE: write down the values of the node IP and port above

Or in case of the example above: http://172.20.10.3:30683

> NOTE: try hitting refresh a couple of times, you should be able to see a different icon appear (as each container has a differnt icon)

## Task 4: Changing the image of a deployment

Updating the image (tag) of a deployment is just as easy as scaling a deployment
and it also can be done using the CLI or by editing the YAML.

```
kubectl set image deployment container-info container-info=gluobe/container-info:green -n lab-06
```

Or simply edit the YAML and `kubectl apply` it again:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: container-info
  labels:
    app: container-info
spec:
  replicas: 1
  selector:
    matchLabels:
      app: container-info
  template:
    metadata:
      labels:
        app: container-info
    spec:
      containers:
      - name: container-info
        image: gluobe/container-info:green
        ports:
        - containerPort: 80
```

Open the application again, the box should be green now:

http://INTERNAL-IP:30XYZ

## Task 5: Cleaning up

Clean up the namespace for this lab:

```
kubectl delete ns lab-06

---

namespace "lab-06" deleted
```