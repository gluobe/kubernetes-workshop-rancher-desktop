# Lab 02 - Nodes

A node is a worker machine in Kubernetes, previously known as a minion. A node
may be a VM or physical machine, depending on the cluster.  A node is where your 
application containers will run.

Each node contains the services necessary to run pods and is managed by the 
master components. The services on a node include the container runtime, kubelet 
and kube-proxy.

## Task 1: Listing nodes

To see which nodes are part of your Kubernetes cluster run the
`kubectl get nodes` command:

```
kubectl get nodes

---

NAME                   STATUS   ROLES                  AGE   VERSION
lima-rancher-desktop   Ready    control-plane,master   44h   v1.22.6+k3s1
```

As we are using Rancher Desktop we only have a single node.  Below is the output of a standard 
3 node cluster:

```
kubectl get nodes

---

NAME                                        STATUS    ROLES     AGE       VERSION
gke-kbc-steven-default-pool-b82ee1c9-5n9j   Ready     <none>    20m       v1.11.7-gke.4
gke-kbc-steven-default-pool-b82ee1c9-6wnx   Ready     <none>    20m       v1.11.7-gke.4
gke-kbc-steven-default-pool-b82ee1c9-x13z   Ready     <none>    20m       v1.11.7-gke.4
```

We can use the `kubectl get nodes -o wide` command to get some additional
information about the nodes:

```
kubectl get nodes -o wide

---

NAME                   STATUS   ROLES                  AGE   VERSION        INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
lima-rancher-desktop   Ready    control-plane,master   44h   v1.22.6+k3s1   172.20.10.3   <none>        Alpine Linux v3.14   5.10.93-0-virt   docker://20.10.11
```

> NOTE: pay special attention to the OS image in use and the Container runtime

## Task 2: Getting detailed node information

If we want more detailed information about a node we have to use the
`kubectl describe nodes <node_name>` command, for example:

```
kubectl describe nodes minikube

---

Name:               lima-rancher-desktop
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/instance-type=k3s
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=lima-rancher-desktop
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=true
                    node-role.kubernetes.io/master=true
                    node.kubernetes.io/instance-type=k3s
Annotations:        alpha.kubernetes.io/provided-node-ip: 172.20.10.3
                    flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"8a:6e:74:32:8a:08"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 172.20.10.3
                    k3s.io/hostname: lima-rancher-desktop
                    k3s.io/internal-ip: 172.20.10.3
                    k3s.io/node-args: ["server","--https-listen-port","6443","--flannel-iface","rd0","--docker"]
                    k3s.io/node-config-hash: FP4JUZRZON6PIDYEK5B42N36BZL2WKETOM2X4HIBZGOVFUEAEFYA====
                    k3s.io/node-env: {"K3S_DATA_DIR":"/var/lib/rancher/k3s/data/8307e9b398a0ee686ec38e18339d1464f75158a8b948b059b564246f4af3a0a6"}
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Tue, 22 Feb 2022 11:12:50 +0100
Taints:             <none>
Unschedulable:      false
Lease:
  HolderIdentity:  lima-rancher-desktop
  AcquireTime:     <unset>
  RenewTime:       Thu, 24 Feb 2022 07:55:17 +0100
Conditions:
  Type             Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----             ------  -----------------                 ------------------                ------                       -------
  MemoryPressure   False   Thu, 24 Feb 2022 07:51:33 +0100   Tue, 22 Feb 2022 11:12:50 +0100   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure     False   Thu, 24 Feb 2022 07:51:33 +0100   Tue, 22 Feb 2022 11:12:50 +0100   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure      False   Thu, 24 Feb 2022 07:51:33 +0100   Tue, 22 Feb 2022 11:12:50 +0100   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready            True    Thu, 24 Feb 2022 07:51:33 +0100   Thu, 24 Feb 2022 07:04:42 +0100   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:  172.20.10.3
  Hostname:    lima-rancher-desktop
Capacity:
  cpu:                2
  ephemeral-storage:  102625208Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             6095776Ki
  pods:               110
Allocatable:
  cpu:                2
  ephemeral-storage:  99833802265
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             6095776Ki
  pods:               110
System Info:
  Machine ID:                 6d8c6a7c15e27afbbcc659a3bbb081c5
  System UUID:                6d8c6a7c15e27afbbcc659a3bbb081c5
  Boot ID:                    fe8e95d6-23b5-4750-bc1c-ba341189a929
  Kernel Version:             5.10.93-0-virt
  OS Image:                   Alpine Linux v3.14
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://20.10.11
  Kubelet Version:            v1.22.6+k3s1
  Kube-Proxy Version:         v1.22.6+k3s1
PodCIDR:                      10.42.0.0/24
PodCIDRs:                     10.42.0.0/24
ProviderID:                   k3s://lima-rancher-desktop
Non-terminated Pods:          (5 in total)
  Namespace                   Name                                       CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                       ------------  ----------  ---------------  -------------  ---
  kube-system                 local-path-provisioner-84bb864455-hwxtl    0 (0%)        0 (0%)      0 (0%)           0 (0%)         44h
  kube-system                 svclb-traefik-kkrr8                        0 (0%)        0 (0%)      0 (0%)           0 (0%)         44h
  kube-system                 coredns-96cc4f57d-xjk5r                    100m (5%)     0 (0%)      70Mi (1%)        170Mi (2%)     44h
  kube-system                 metrics-server-ff9dbcb6c-mltrt             100m (5%)     0 (0%)      70Mi (1%)        0 (0%)         44h
  kube-system                 traefik-55fdc6d984-7glvw                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         44h
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                200m (10%)  0 (0%)
  memory             140Mi (2%)  170Mi (2%)
  ephemeral-storage  0 (0%)      0 (0%)
  hugepages-1Gi      0 (0%)      0 (0%)
  hugepages-2Mi      0 (0%)      0 (0%)
Events:
  Type     Reason                   Age                From        Message
  ----     ------                   ----               ----        -------
  Normal   Starting                 44h                kube-proxy  
  Normal   Starting                 50m                kube-proxy  
  Normal   Starting                 3m51s              kube-proxy  
  Normal   NodeHasNoDiskPressure    44h (x2 over 44h)  kubelet     Node lima-rancher-desktop status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     44h (x2 over 44h)  kubelet     Node lima-rancher-desktop status is now: NodeHasSufficientPID
  Normal   NodeHasSufficientMemory  44h (x2 over 44h)  kubelet     Node lima-rancher-desktop status is now: NodeHasSufficientMemory
  Normal   NodeAllocatableEnforced  44h                kubelet     Updated Node Allocatable limit across pods
  Normal   Starting                 44h                kubelet     Starting kubelet.
  Normal   NodeReady                44h                kubelet     Node lima-rancher-desktop status is now: NodeReady
  Normal   Starting                 50m                kubelet     Starting kubelet.
  Normal   NodeAllocatableEnforced  50m                kubelet     Updated Node Allocatable limit across pods
  Normal   NodeHasSufficientMemory  50m                kubelet     Node lima-rancher-desktop status is now: NodeHasSufficientMemory
  Normal   NodeHasSufficientPID     50m                kubelet     Node lima-rancher-desktop status is now: NodeHasSufficientPID
  Warning  Rebooted                 50m                kubelet     Node lima-rancher-desktop has been rebooted, boot id: 02ffe31a-fb6c-4cb3-81e9-2cdcc0816af2
  Normal   NodeNotReady             50m                kubelet     Node lima-rancher-desktop status is now: NodeNotReady
  Normal   NodeHasNoDiskPressure    50m                kubelet     Node lima-rancher-desktop status is now: NodeHasNoDiskPressure
  Normal   NodeReady                50m                kubelet     Node lima-rancher-desktop status is now: NodeReady
  Normal   Starting                 3m51s              kubelet     Starting kubelet.
  Normal   NodeAllocatableEnforced  3m51s              kubelet     Updated Node Allocatable limit across pods
  Normal   NodeHasSufficientMemory  3m51s              kubelet     Node lima-rancher-desktop status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    3m51s              kubelet     Node lima-rancher-desktop status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     3m51s              kubelet     Node lima-rancher-desktop status is now: NodeHasSufficientPID
  Warning  Rebooted                 3m51s              kubelet     Node lima-rancher-desktop has been rebooted, boot id: fe8e95d6-23b5-4750-bc1c-ba341189a929
```

Read through the output above to see all the information that is available about
the node.

The `kubectl describe nodes <node_name>` is a very useful tool when
troubleshooting a node that is failing.  Going through the output (especially 
the event section) will, in almost all cases, give a  good indication why the 
node is not behaving as it should.