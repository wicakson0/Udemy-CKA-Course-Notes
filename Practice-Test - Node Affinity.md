# Practice-Test - Node Affinity

### How many Labels exist on node node01?
```sh
kubectl describe nodes node01
```  
```yaml
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=node01
                    kubernetes.io/os=linux
```  

### What is the value set to the label key beta.kubernetes.io/arch on node01?
`amd64`

### Apply a label color=blue to node node01
```sh
kubectl label nodes node01 color=blue
```
### Create a new deployment named blue with the nginx image and 3 replicas.
```sh
kubectl create deployment blue --image=nginx --replicas=3
```

### Which node can host blue (check taints on all nodes)
```sh
kubectl describe nodes controlplane | grep "Taints"
```  
```sh
kubectl describe nodes node01 | grep "Taints"
```

### Set Node Affinity to the blue deployment to place the pods on node01 only. Ensure that node01 has the label color=blue.
```sh
kubectl get deployments blue -o yaml > blue.yaml
```  
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue
spec:
  replicas: 3
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
      affinity:            # affinity rules added from here
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: color
                operator: In
                values:
                - blue
``` 
```sh
ubectl apply -f blue-new.yaml
```

