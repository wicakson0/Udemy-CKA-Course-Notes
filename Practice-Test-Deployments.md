# Practice Test - Deployments

### How many PODs exist on the system? In the current(default) namespace.
```sh
kubectl get pods -n default
```

### How many ReplicaSets exist on the system? In the current(default) namespace.
```sh
kubectl get replicasets -n default
```

### How many Deployments exist on the system? In the current(default) namespace.
```sh
kubectl get deployments -n default
```

### How many Deployments exist on the system now?
```sh
kubectl get deployments -n default
``` 
```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
frontend-deployment   0/4     4            0           23s
```  
There is currently only `1` deployment.  

### How many ReplicaSets exist on the system now?
```sh
kubectl get replicasets -n default
```  
There is currently only `1`  
```
NAME                           DESIRED   CURRENT   READY   AGE
frontend-deployment-cd6b557c   4         4         0       2m46s
```

### How many PODs exist on the system now?
```sh
kubectl get pods -n default
```  
There is currently `4`  
```
NAME                                 READY   STATUS             RESTARTS   AGE
frontend-deployment-cd6b557c-rnvmp   0/1     ImagePullBackOff   0          10m
frontend-deployment-cd6b557c-tjpxw   0/1     ImagePullBackOff   0          10m
frontend-deployment-cd6b557c-wv9nb   0/1     ImagePullBackOff   0          10m
frontend-deployment-cd6b557c-xgqkl   0/1     ImagePullBackOff   0          10m
```
### Out of all the existing PODs, how many are ready?
`0`, none are ready  

### What is the image used to create the pods in the new deployment?
```sh
kubectl get deployments frontend-deployment -o YAML
```  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"frontend-deployment","namespace":"default"},"spec":{"replicas":4,"selector":{"matchLabels":{"name":"busybox-pod"}},"template":{"metadata":{"labels":{"name":"busybox-pod"}},"spec":{"containers":[{"command":["sh","-c","echo Hello Kubernetes! \u0026\u0026 sleep 3600"],"image":"busybox888","name":"busybox-container"}]}}}}
  creationTimestamp: "2025-07-10T08:09:07Z"
  generation: 1
  name: frontend-deployment
  namespace: default
  resourceVersion: "1340"
  uid: 11b7cfe0-8597-4ccb-999f-65b97e96e756
spec:
  progressDeadlineSeconds: 600
  replicas: 4
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      name: busybox-pod
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
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
        image: busybox888
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
  conditions:
  - lastTransitionTime: "2025-07-10T08:09:07Z"
    lastUpdateTime: "2025-07-10T08:09:07Z"
    message: Deployment does not have minimum availability.
    reason: MinimumReplicasUnavailable
    status: "False"
    type: Available
  - lastTransitionTime: "2025-07-10T08:19:08Z"
    lastUpdateTime: "2025-07-10T08:19:08Z"
    message: ReplicaSet "frontend-deployment-cd6b557c" has timed out progressing.
    reason: ProgressDeadlineExceeded
    status: "False"
    type: Progressing
  observedGeneration: 1
  replicas: 4
  unavailableReplicas: 4
  updatedReplicas: 4
```  
image name is `busybox888`.  

### Why do you think the deployment is not ready?
```
NAME                                 READY   STATUS             RESTARTS   AGE
frontend-deployment-cd6b557c-rnvmp   0/1     ImagePullBackOff   0          24m
frontend-deployment-cd6b557c-tjpxw   0/1     ImagePullBackOff   0          24m
frontend-deployment-cd6b557c-wv9nb   0/1     ImagePullBackOff   0          24m
frontend-deployment-cd6b557c-xgqkl   0/1     ImagePullBackOff   0          24m
```  
ImagePullBackOff indicates that the cluster failed to pull the image, image most likely doesn't exist.  

### Create a new Deployment using the deployment-definition-1.yaml file located at /root/.
deployment-definition-1.yaml  
there is a mistake in `kind`, should be `Deployment`  
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-1
spec:
  replicas: 2
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      labels:
        name: busybox-pod
    spec:
      containers:
      - name: busybox-container
        image: busybox
        command:
        - sh
        - "-c"
        - echo Hello Kubernetes! && sleep 3600
```  
```sh
kubectl apply -f deployment-definition-1.yaml
```

### Create a new Deployment with the below attributes using your own deployment definition file.
Name: `httpd-frontend`;  
Replicas: `3`;  
Image: `httpd:2.4-alpine`;  

```sh
kubectl create deployment httpd-frontend --image=httpd:2.4-alpine --replicas=3
```