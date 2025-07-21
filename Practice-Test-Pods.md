# Practice Test - Pods (KodeKloud)
[Link](https://learn.kodekloud.com/user/courses/udemy-labs-certified-kubernetes-administrator-with-practice-tests/module/e6ae2f68-9b3a-439e-a534-d63d372840d2/lesson/5c388f3e-bd27-41bd-9c3c-613dafc29bf9)  

### How many pods exist on the system?
```sh
kubectl get pods -n default
```

### Create a new pod using the nginx image.
```sh
kubectl run nginx --image=nginx
```

### How many pods are created now?
```sh
kubectl get pods -n default
```

### Which image is specified for the pods whose names begin with the newpods- prefix?
```sh
kubectl describe pods newpods-rtmcx
```

### Which nodes are these pods placed on?
```sh
kubectl get pods -A -o wide
```

### We just created a new pod named webapp. How many containers are part of the webapp pod?
```sh
kubectl describe pod webapp
```

### What images are used in the new webapp pod?
- Nginx
- Agentx

### What is the state of the container Agentx in pods webapp ?
```sh
kubectl describe pods webapp
```

### Why do you think the container agentx in pod webapp is in error?
```sh
kubectl describe pods webapp
```  
The repository might not exist or the cluster does not have access to the repository.  

### What does the READY column in the output of the kubectl get pods command indicate?
Running containers / total containers in pods.  

### Delete the webapp Pod.
```sh
kubectl delete pods webapp
```

### Create a new pod with the name redis and the image redis123.
```sh
kubectl run redis --image=redis123
```

### Now change the image on this pod to redis.
Dump the YAML file of the pods  
```sh
kubectl get pods redis -o YAML > redis-pod.yaml
```  
Change the image field in the redis-pod.yaml  
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-07-10T02:51:18Z"
  generation: 1
  labels:
    run: redis
  name: redis
  namespace: default
  resourceVersion: "1582"
  uid: 1a83817a-f916-4b1b-91ce-e7d691db1976
spec:
  containers:
  - image: redis
    imagePullPolicy: Always
    name: redis
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-fvspb
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: controlplane
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-fvspb
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2025-07-10T02:51:19Z"
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2025-07-10T02:51:18Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2025-07-10T02:51:18Z"
    message: 'containers with unready status: [redis]'
    reason: ContainersNotReady
    status: "False"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-07-10T02:51:18Z"
    message: 'containers with unready status: [redis]'
    reason: ContainersNotReady
    status: "False"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2025-07-10T02:51:18Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - image: redis123
    imageID: ""
    lastState: {}
    name: redis
    ready: false
    restartCount: 0
    started: false
    state:
      waiting:
        message: 'Back-off pulling image "redis123": ErrImagePull: failed to pull
          and unpack image "docker.io/library/redis123:latest": failed to resolve
          reference "docker.io/library/redis123:latest": pull access denied, repository
          does not exist or may require authorization: server message: insufficient_scope:
          authorization failed'
        reason: ImagePullBackOff
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-fvspb
      readOnly: true
      recursiveReadOnly: Disabled
  hostIP: 192.168.81.1
  hostIPs:
  - ip: 192.168.81.1
  phase: Pending
  podIP: 10.22.0.14
  podIPs:
  - ip: 10.22.0.14
  qosClass: BestEffort
  startTime: "2025-07-10T02:51:18Z"
```  
Reapply changes  
```sh
kubectl replace -f redis-pod.yaml 
```  
```sh
kubectl create deployment httpd-frontend --image=httpd:2.4-alpine --replicas=3 --dry-run=client -o YAML
```  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: httpd-frontend
  name: httpd-frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: httpd-frontend
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: httpd-frontend
    spec:
      containers:
      - image: httpd:2.4-alpine
        name: httpd
        resources: {}
status: {}
```  