# Practice Test - Services

### How many Services exist on the system? In the current(default) namespace
```sh
kubectl get services -n default
```  
```
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   19m
```  
### kubectl describe services kubernetes
```sh
kubectl describe services kubernetes
```  
```
Name:                     kubernetes
Namespace:                default
Labels:                   component=apiserver
                          provider=kubernetes
Annotations:              <none>
Selector:                 <none>
Type:                     ClusterIP
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.43.0.1
IPs:                      10.43.0.1
Port:                     https  443/TCP
TargetPort:               6443/TCP
Endpoints:                192.168.114.197:6443
Session Affinity:         None
Internal Traffic Policy:  Cluster
Events:                   <none>
```  
TargetPort: 6443/TCP

### How many labels are configured on the kubernetes service?
```sh
kubectl get services kubernetes -o YAML
```  
```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2025-07-11T03:38:30Z"
  labels:
    component: apiserver
    provider: kubernetes
  name: kubernetes
  namespace: default
  resourceVersion: "202"
  uid: 86a1ee75-793e-48bf-947f-03ae43eff3ad
spec:
  clusterIP: 10.43.0.1
  clusterIPs:
  - 10.43.0.1
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - name: https
    port: 443
    protocol: TCP
    targetPort: 6443
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
```  
`2 labels are configured`  
### How many Endpoints are attached on the kubernetes service?
`1`  

### How many deployments are currently on the default namespace
```sh
kubectl get deployments -n default
```
`1`  
### What is the image used to create the pods in the deployment?
```sh
kubectl get deployments simple-webapp-deployment -o YAML
```  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2025-07-11T08:12:56Z"
  generation: 1
  name: simple-webapp-deployment
  namespace: default
  resourceVersion: "1066"
  uid: eb44b3f1-42bf-4041-b03e-6a80edf186bb
spec:
  progressDeadlineSeconds: 600
  replicas: 4
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      name: simple-webapp
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: simple-webapp
    spec:
      containers:
      - image: kodekloud/simple-webapp:red
        imagePullPolicy: IfNotPresent
        name: simple-webapp
        ports:
        - containerPort: 8080
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 4
  conditions:
  - lastTransitionTime: "2025-07-11T08:13:00Z"
    lastUpdateTime: "2025-07-11T08:13:00Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2025-07-11T08:12:56Z"
    lastUpdateTime: "2025-07-11T08:13:00Z"
    message: ReplicaSet "simple-webapp-deployment-8555484b96" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 4
  replicas: 4
  updatedReplicas: 4
```  
# Create a new service to access the web application using the service-definition-1.yaml file.
```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
  namespace: default
spec:
  ports:
  - nodePort: 30080
    port: 8080
    targetPort: 8080
  selector:
    name: simple-webapp
  type: NodePort
```  
```sh
kubectl apply -f service-definition-1.yaml
```