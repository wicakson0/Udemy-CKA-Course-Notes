# Practice Test - Namespaces (Kodekloud)

### How many Namespaces exist on the system?
```sh
kubectl get namespaces
```  
```sh
NAME              STATUS   AGE
default           Active   9m50s
dev               Active   3m28s
finance           Active   3m28s
kube-node-lease   Active   9m50s
kube-public       Active   9m50s
kube-system       Active   9m51s
manufacturing     Active   3m28s
marketing         Active   3m28s
prod              Active   3m28s
research          Active   3m28s
```

### How many pods exist in the research namespace?
```sh
kubectl get pods -n research
```  
```sh
NAME    READY   STATUS             RESTARTS        AGE
dna-1   0/1     CrashLoopBackOff   6 (17s ago)     5m58s
dna-2   0/1     Completed          6 (2m59s ago)   5m58s
```

### Create a POD in the finance namespace.
```sh
kubectl run redis --image=redis -n finance -o yaml --dry-run=client > redis.yaml
```  
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis
  name: redis
  namespace: finance
spec:
  containers:
  - image: redis
    name: redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```  
```sh
kubectl apply -f redis.yaml
```

### Which namespace has the blue pod in it?
```sh
kubectl get pods -A | grep -i "blue"
```  
```sh
marketing       blue                                      1/1     Running            0               20m
```

### What DNS name should the Blue application use to access the database db-service in its own namespace - marketing?
```sh
kubectl get pods -n marketing
```  

### What DNS name should the Blue application use to access the database db-service in the dev namespace?
```sh
kubectl get services -n dev
```  