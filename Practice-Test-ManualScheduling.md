# Practice Test - Manual Scheduling
### A pod definition file nginx.yaml is given. Create a pod using the file. Only create the POD for now. We will inspect its status next.
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginx
```  
```sh
kubectl apply -f nginx.yaml 
```
### What is the status of the created POD?
```sh
kubectl get pods -A
```  
```sh
NAMESPACE      NAME                                   READY   STATUS    RESTARTS   AGE
default        nginx                                  0/1     Pending   0          72s
kube-flannel   kube-flannel-ds-qblgf                  1/1     Running   0          11m
kube-flannel   kube-flannel-ds-wr597                  1/1     Running   0          11m
kube-system    coredns-7484cd47db-6z2vf               1/1     Running   0          11m
kube-system    coredns-7484cd47db-c5jw7               1/1     Running   0          11m
kube-system    etcd-controlplane                      1/1     Running   0          11m
kube-system    kube-apiserver-controlplane            1/1     Running   0          11m
kube-system    kube-controller-manager-controlplane   1/1     Running   0          11m
kube-system    kube-proxy-78rbt                       1/1     Running   0          11m
kube-system    kube-proxy-pn58f                       1/1     Running   0          11m
```  
### Why is the POD in a pending state? Inspect the environment for various kubernetes control plane components.
```sh
kubectl get pods -n kube-system
```  
```sh
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-7484cd47db-6z2vf               1/1     Running   0          13m
coredns-7484cd47db-c5jw7               1/1     Running   0          13m
etcd-controlplane                      1/1     Running   0          13m
kube-apiserver-controlplane            1/1     Running   0          13m
kube-controller-manager-controlplane   1/1     Running   0          13m
kube-proxy-78rbt                       1/1     Running   0          13m
kube-proxy-pn58f                       1/1     Running   0          13m
```  
`no scheduler is present`

### Manually schedule the pod on node01. Delete and recreate the POD if necessary.
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01
  containers:
  -  image: nginx
     name: nginx
```  
```sh
kubectl delete pods nginx
kubectl apply -f nginx.yaml
```

### Now schedule the same pod on the controlplane node. Delete and recreate the POD if necessary.
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: controlplane
  containers:
  -  image: nginx
     name: nginx
```  
```sh
kubectl delete pods nginx
kubectl apply -f nginx.yaml
```