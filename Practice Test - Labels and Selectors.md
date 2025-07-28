# Practice Test - Labels and Selectors
### We have deployed a number of PODs. They are labelled with tier, env and bu. How many PODs exist in the dev environment (env)?
```sh
kubectl get pods --selector="env=dev"
```  
```sh
NAME          READY   STATUS    RESTARTS   AGE
app-1-p46bs   1/1     Running   0          107s
app-1-pm6zc   1/1     Running   0          107s
app-1-t9n4s   1/1     Running   0          107s
db-1-bbqlv    1/1     Running   0          107s
db-1-c5zbm    1/1     Running   0          107s
db-1-c8l96    1/1     Running   0          107s
db-1-pdbnx    1/1     Running   0          107s
```
### How many PODs are in the finance business unit (bu)?
```sh
kubectl get pods --selector="bu=finance"
```  
```yaml
NAME          READY   STATUS    RESTARTS   AGE
app-1-p46bs   1/1     Running   0          6m35s
app-1-pm6zc   1/1     Running   0          6m35s
app-1-t9n4s   1/1     Running   0          6m35s
app-1-zzxdf   1/1     Running   0          6m35s
auth          1/1     Running   0          6m35s
db-2-w2z88    1/1     Running   0          6m35s
```

### How many objects are in the prod environment including PODs, ReplicaSets and any other objects?
```sh
kubectl get all --selector="env=prod"
```  
```sh
NAME              READY   STATUS    RESTARTS   AGE
pod/app-1-zzxdf   1/1     Running   0          8m32s
pod/app-2-gsnn4   1/1     Running   0          8m32s
pod/auth          1/1     Running   0          8m32s
pod/db-2-w2z88    1/1     Running   0          8m32s

NAME            TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/app-1   ClusterIP   10.43.3.56   <none>        3306/TCP   8m32s

NAME                    DESIRED   CURRENT   READY   AGE
replicaset.apps/app-2   1         1         1       8m32s
replicaset.apps/db-2    1         1         1       8m32s
```  
### Identify the POD which is part of the prod environment, the finance BU and of frontend tier?
```sh
kubectl get all --selector env=prod,bu=finance,tier=frontend
```  
```sh
NAME              READY   STATUS    RESTARTS   AGE
pod/app-1-zzxdf   1/1     Running   0          18m
```

### A ReplicaSet definition file is given replicaset-definition-1.yaml. Attempt to create the replicaset; you will encounter an issue with the file. Try to fix it.
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
   name: replicaset-1
spec:
   replicas: 2
   selector:
      matchLabels:
        tier: front-end
   template:
     metadata:
       labels:
        tier: front-end
     spec:
       containers:
       - name: nginx
         image: nginx
```  
```sh
kubectl apply -f replicaset-definition-1.yaml
```