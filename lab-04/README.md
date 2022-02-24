# Lab 04 - Pods

In this lab we will run our first, very basic, application on Kubernetes.  We
will basically run a single pod as our application.

## Task 1: Creating a namespace

Create a namespace for this lab:

```
kubectl create ns lab-04

---

namespace/lab-04 created
```

Verify that your namespace was created:

```
kubectl get namespaces

---

NAME              STATUS   AGE
default           Active   44h
kube-system       Active   44h
kube-public       Active   44h
kube-node-lease   Active   44h
lab-04            Active   16s
```

## Task 2: Starting your first pod

To run your first pod (the official nginx Docker image), run the following
command:

```
kubectl run --restart=Never --image=nginx  nginx -n lab-04

---

pod/nginx created
```

The above command will create a single pod that is based on the official nginx
Docker image in the `lab-04` namespace.  Run the following command to verify that the pod has been
created and is in the running state (if the pod is not yet in the running state
wait a couple of seconds and try to run the command again):

```
kubectl get pods -n lab-04

---

NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          40s
```

As we have not yet configured any services and/or ingresses we will use a litte
"hack" to access our pod we just created.

Run the following command to forward the port of the pod (in our case port 80)
running in our Kubernetes cluster to a port on your laptop (in this case port
8080).

```
kubectl port-forward pod/nginx 8080:80 -n lab-04

---

Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

> NOTE: your prompt will be locked by the port-forward process

Now go to your browser and surf to http://localhost:8080, you should be greeted
with the default nginx welcome page:

![nginx welcome page](images/lab-04-nginx-welcome-page.png)

If that works you can close the port-foward connection by pressing `CTRL+c` and you will get access to your prompt again.

## Task 3: Connecting to your pod

To connect to your pod, you can use the following command (notice how it
resembles the `docker exec` command in options and functionality):

```
kubectl exec -ti nginx -n lab-04 -- bash

---

root@nginx:/#
```

Notice how the prompt changes.  `exec`-ing into a pod is very powerful for
troubleshooting, but keep in mind that by default pods/containers are immutable
so remember to not make any changes inside the pods/container.

To exit run the `exit` command.

```
root@nginx:/# exit

---

exit
```

## Task 4: Pod logs

Again similar to when working with Docker containers, Kubernetes has a built-in
feature that exposes all stdout/stderr output into logs.  To access those logs
issue the following command:

```
kubectl logs nginx -n lab-04

---

/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2022/02/24 07:04:14 [notice] 1#1: using the "epoll" event method
2022/02/24 07:04:14 [notice] 1#1: nginx/1.21.6
2022/02/24 07:04:14 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
2022/02/24 07:04:14 [notice] 1#1: OS: Linux 5.10.93-0-virt
2022/02/24 07:04:14 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2022/02/24 07:04:14 [notice] 1#1: start worker processes
2022/02/24 07:04:14 [notice] 1#1: start worker process 32
2022/02/24 07:04:14 [notice] 1#1: start worker process 33
127.0.0.1 - - [24/Feb/2022:07:07:34 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
127.0.0.1 - - [24/Feb/2022:07:07:34 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
127.0.0.1 - - [24/Feb/2022:07:07:34 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
127.0.0.1 - - [24/Feb/2022:07:07:35 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
127.0.0.1 - - [24/Feb/2022:07:07:35 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
127.0.0.1 - - [24/Feb/2022:07:07:35 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
127.0.0.1 - - [24/Feb/2022:07:07:35 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
127.0.0.1 - - [24/Feb/2022:07:07:35 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
127.0.0.1 - - [24/Feb/2022:07:07:35 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
127.0.0.1 - - [24/Feb/2022:07:07:36 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
127.0.0.1 - - [24/Feb/2022:07:07:36 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36" "-"
```

> NOTE: if your logs are empty, repeat Task 2 where you `kubectl port-forward` 
> the container port and hit reload the page a couple more times in your 
> browser

A very handy option of `kubectl logs` is that you can follow them real-time using the `-f`
option, this is extremely useful when troubleshooting:

```
kubectl logs nginx -n lab-04 -f
```

Hit `CTRL+c` to exit the logs.

## Task 5: Getting pod details

Like with most objects in Kubernetes you can use the `kubectl describe` command 
to get more information about a specific pod, for example:

```
kubectl describe pods nginx -n lab-04

---

Name:         nginx
Namespace:    lab-04
Priority:     0
Node:         lima-rancher-desktop/172.20.10.3
Start Time:   Thu, 24 Feb 2022 08:04:11 +0100
Labels:       run=nginx
Annotations:  <none>
Status:       Running
IP:           10.42.0.20
IPs:
  IP:  10.42.0.20
Containers:
  nginx:
    Container ID:   docker://f9df9679d14927fb59bb6047b9c0134e3cfa2574567af0808c02f565b8ad6a44
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 24 Feb 2022 08:04:14 +0100
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-csg5v (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-csg5v:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  5m8s  default-scheduler  Successfully assigned lab-04/nginx to lima-rancher-desktop
  Normal  Pulling    5m7s  kubelet            Pulling image "nginx"
  Normal  Pulled     5m6s  kubelet            Successfully pulled image "nginx" in 1.75104581s
  Normal  Created    5m5s  kubelet            Created container nginx
  Normal  Started    5m5s  kubelet            Started container nginx
```

## Task 6: Cleaning up

Clean up the namespace for this lab:

```
kubectl delete ns lab-04

---

namespace "lab-04" deleted
```