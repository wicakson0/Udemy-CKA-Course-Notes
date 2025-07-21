# Practice Test ReplicaSets
[Link](https://learn.kodekloud.com/user/courses/udemy-labs-certified-kubernetes-administrator-with-practice-tests/module/e6ae2f68-9b3a-439e-a534-d63d372840d2/lesson/0bda5a1f-2154-4522-a843-c7f90278130d)  

### How many PODs exist on the system?
```sh
kubectl get pods -n default
```

### How many ReplicaSets exist on the system?
```sh
kubectl get replicasets -n default
```

### How about now? How many ReplicaSets do you see?
`1`, named new-replica-set, consisting of 4 pods.  

### How many PODs are DESIRED in the new-replica-set?
`4`  

### What is the image used to create the pods in the new-replica-set?
```sh
kubectl describe replicasets new-replica-set
```  

```yaml
Name:         new-replica-set
Namespace:    default
Selector:     name=busybox-pod
Labels:       <none>
Annotations:  <none>
Replicas:     4 current / 4 desired
Pods Status:  0 Running / 4 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  name=busybox-pod
  Containers:
   busybox-container:
    Image:      busybox777
    Port:       <none>
    Host Port:  <none>
    Command:
      sh
      -c
      echo Hello Kubernetes! && sleep 3600
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  2m53s  replicaset-controller  Created pod: new-replica-set-shmk7
  Normal  SuccessfulCreate  2m53s  replicaset-controller  Created pod: new-replica-set-g62kq
  Normal  SuccessfulCreate  2m53s  replicaset-controller  Created pod: new-replica-set-5dftg
  Normal  SuccessfulCreate  2m53s  replicaset-controller  Created pod: new-replica-set-854tp
```  
The image used is `busybox777`  

### How many PODs are READY in the new-replica-set?
```sh
kubectl get replicasets -A
```  
```
NAMESPACE     NAME                                DESIRED   CURRENT   READY   AGE
default       new-replica-set                     4         4         0       13m
kube-system   coredns-697968c856                  1         1         1       28m
kube-system   local-path-provisioner-774c6665dc   1         1         1       28m
kube-system   metrics-server-6f4c6675d5           1         1         1       28m
kube-system   traefik-c98fdf6fb                   1         1         1       27m
```  
`0`, no pods are ready.  

### Why do you think the PODs are not ready?
```sh
kubectl describe replicasets new-replica-set
kubectl describe pods new-replica-set-5dftg
```  

```
Name:             new-replica-set-5dftg
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.243.163
Start Time:       Thu, 10 Jul 2025 03:52:36 +0000
Labels:           name=busybox-pod
Annotations:      <none>
Status:           Pending
IP:               10.22.0.11
IPs:
  IP:           10.22.0.11
Controlled By:  ReplicaSet/new-replica-set
Containers:
  busybox-container:
    Container ID:  
    Image:         busybox777
    Image ID:      
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo Hello Kubernetes! && sleep 3600
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-bv8qz (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-bv8qz:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                  From               Message
  ----     ------     ----                 ----               -------
  Normal   Scheduled  16m                  default-scheduler  Successfully assigned default/new-replica-set-5dftg to controlplane
  Normal   Pulling    13m (x5 over 16m)    kubelet            Pulling image "busybox777"
  Warning  Failed     13m (x5 over 16m)    kubelet            Failed to pull image "busybox777": failed to pull and unpack image "docker.io/library/busybox777:latest": failed to resolve reference "docker.io/library/busybox777:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
  Warning  Failed     13m (x5 over 16m)    kubelet            Error: ErrImagePull
  Normal   BackOff    104s (x64 over 16m)  kubelet            Back-off pulling image "busybox777"
  Warning  Failed     104s (x64 over 16m)  kubelet            Error: ImagePullBackOff
```  
`Kubernetes cannot pull the image from the repository`, either the image doesn't exist or insufficient privilege.  

### Now, delete one of the four PODs directly using kubectl delete command.
```sh
kubectl delete pods new-replica-set-5dftg
```  

### How many PODs exist now?
`4`, ReplicaSets will self-heal.  

### Why are there still 4 PODs, even after you deleted one?
`ReplicaSets ensures that the desired number of pods always run.`  

### Create a ReplicaSet using the replicaset-definition-1.yaml file located at /root/. There is an issue with the file, so try to fix it.
```yaml
apiVersion: v1
kind: ReplicaSet
metadata:
  name: replicaset-1
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx
```  
change apiVersion to `apps/v1' then.  
```sh
kubectl apply -f replicasset-definition-1.yaml
```

### Fix the issue in the replicaset-definition-2.yaml file and create a ReplicaSet using it.
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-2
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```  
change `tier: nginx` to `tier: frontend` and then reapply the changes.  

### Delete the two newly created ReplicaSets - replicaset-1 and replicaset-2
```sh
kubectl delete replicasets replicaset-1 replicaset-2
```

### Scale the ReplicaSet to 5 PODs.
```sh
kubectl scale --replicas=5 replicasets new-replica-set
```

### Now scale the ReplicaSet down to 2 PODs
Dump the old replicasets yaml  
```sh
kubectl get replicasets new-replica-set -o YAML > new-replica-set.yaml
```  
Change the `replicas`  
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  creationTimestamp: "2025-07-10T06:23:05Z"
  generation: 3
  name: new-replica-set
  namespace: default
  resourceVersion: "1462"
  uid: a982c01e-3793-41da-8e05-ec7345da7b29
spec:
  replicas: 2
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: busybox-pod
    spec:
      containers:
      - command:
        - sh
        - -c
        - echo Hello Kubernetes! && sleep 3600
        image: busybox777
        imagePullPolicy: Always
        name: busybox-container
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  fullyLabeledReplicas: 5
  observedGeneration: 3
  replicas: 5
```  

Reapply the changes  
```sh
kubectl apply -f new-replica-set.yaml
```