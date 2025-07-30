# Practice Test - Taints and Tolerations

### How many nodes exist on the system? Including the controlplane node.
```sh
kubectl get nodes
```  
```sh
NAME           STATUS   ROLES           AGE     VERSION
controlplane   Ready    control-plane   7m21s   v1.33.0
node01         Ready    <none>          6m42s   v1.33.0
```

### Do any taints exist on node01 node?
```sh
kubectl describe nodes node01 | grep "Taints"
```  
```yaml
Taints:             <none>
```

### Create a taint where if spray is mortein then NoSchedule
```sh
kubectl taint nodes node01 spray=mortein:NoSchedule
```  
```yaml
spec:
  podCIDR: 172.17.1.0/24
  podCIDRs:
  - 172.17.1.0/24
  taints:
  - effect: NoSchedule
    key: spray
    value: mortein
```

### Create a new pod with the nginx image and pod name as mosquito.
```sh
kubectl run mosquito --image=nginx
```
### What is the state of the POD?
```sh
kubectl get pods -A
```  
```sh
NAMESPACE      NAME                                   READY   STATUS    RESTARTS   AGE
default        mosquito                               0/1     Pending   0          83s
kube-flannel   kube-flannel-ds-d697s                  1/1     Running   0          21m
kube-flannel   kube-flannel-ds-wsqd7                  1/1     Running   0          20m
kube-system    coredns-7484cd47db-826d4               1/1     Running   0          21m
kube-system    coredns-7484cd47db-k7hwm               1/1     Running   0          21m
kube-system    etcd-controlplane                      1/1     Running   0          21m
kube-system    kube-apiserver-controlplane            1/1     Running   0          21m
kube-system    kube-controller-manager-controlplane   1/1     Running   0          21m
kube-system    kube-proxy-bpj67                       1/1     Running   0          20m
kube-system    kube-proxy-p76jm                       1/1     Running   0          21m
kube-system    kube-scheduler-controlplane            1/1     Running   0          21m
```

### Why do you think the pod is in a pending state?
```sh
kubectl describe pods mosquito
```  
```yaml
Events:
  Type     Reason            Age    From               Message
  ----     ------            ----   ----               -------
  Warning  FailedScheduling  3m56s  default-scheduler  0/2 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: }, 1 node(s) had untolerated taint {spray: mortein}. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
```

### Create another pod named bee with the nginx image, which has a toleration set to the taint mortein.
```sh
kubectl run bee --image=nginx --dry-run=client -o yaml > bee.yaml
```  
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: bee
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  tolerations:
    - key: spray
      operator: "Equal"
      value: "mortein"
      effect: "NoSchedule"
status: {}
```  

### Do you see any taints on controlplane node?
```sh
kubectl describe nodes controlplane | grep "Taints:"
```
```yaml
Taints:             node-role.kubernetes.io/control-plane:NoSchedule
```

### Remove the taint on controlplane, which currently has the taint effect of NoSchedule.
```sh
kubectl taint nodes controlplane node-role.kubernetes.io/control-plane:NoSchedule-
```

### What is the state of the pod mosquito now?
```sh
kubectl get pods -A -o wide
```  
```yaml
default        mosquito                               1/1     Running   0          26m   172.17.0.4        controlplane   <none>           <none>
```

### Which node host mosquito now ?
`controlplane`