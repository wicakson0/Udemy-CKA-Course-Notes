# Practice Test - Priority Class

### Which of the following PriorityClasses are part of a default Kubernetes setup?
```sh
kubectl get PriorityClasses -A
```  
```yaml
NAME                      VALUE        GLOBAL-DEFAULT   AGE   PREEMPTIONPOLICY
system-cluster-critical   2000000000   false            34m   PreemptLowerPriority
system-node-critical      2000001000   false            34m   PreemptLowerPriority
```

### What is the priority value assigned to system-node-critical?
```sh
kubectl describe PriorityClasses system-node-critical
```  
```yaml
Name:              system-node-critical
Value:             2000001000
GlobalDefault:     false
PreemptionPolicy:  PreemptLowerPriority
Description:       Used for system critical pods that must not be moved from their current node.
Annotations:       <none>
Events:            <none>
```

### What is the value of preemptionPolicy in system-node-critical?
`PreemptLowerPriority`

### Create a PriorityClass named high-priority, a value of 100000, and a preemption policy of PreemptLowerPriority. Do not set this class as a global default.
```yaml
# high-priority.yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 100000
globalDefault: false
preemptionPolicy: PreemptLowerPriority
description: "This priority class should be used for XYZ service pods only."
```  
```sh
kubectl apply -f high-priority.yaml
```

### Create another PriorityClass named low-priority with a value of 1000. Do not set this class as a global default.
```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 1000
globalDefault: false
preemptionPolicy: PreemptLowerPriority
description: "This priority class should be used for XYZ service pods only."
```  
```sh
kubectl apply -f low-priority.yaml
```

### In the default namespace, create a pod named low-prio-pod that runs an nginx image and uses the low-priority PriorityClass.
```yaml
# low-prio-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: low-prio-pod
  name: low-prio-pod
  namespace: default
spec:
  containers:
  - image: nginx
    name: low-prio-pod
    resources: {}
  priorityClassName: low-priority
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```  
```sh
kubectl apply -f low-prio-pod.yaml
```

### Now, create another pod in the default namespace named high-prio-pod that runs an nginx image and uses the high-priority PriorityClass.
generate pod template  
```sh
kubectl run high-prio-pod --image=nginx --namespace=default --dry-run=client -o yaml > high-prio-pod.yaml
```
edit to use high priority  
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: high-prio-pod
  name: high-prio-pod
  namespace: default
spec:
  containers:
  - image: nginx
    name: high-prio-pod
    resources: {}
  priorityClassName: high-priority
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```  
```sh
kubectl apply -f high-prio-pod.yaml
```

### You can compare the priority classes on both pods using the following command:
```sh
kubectl get pods -o custom-columns="NAME:.metadata.name,PRIORITY:.spec.priorityClassName"
```

### two new pods critical-app and low-app are created, check their status.
```sh
kubectl get pods --namespace=default -o wide
```  
```yaml
NAME            READY   STATUS    RESTARTS   AGE    IP          NODE           NOMINATED NODE   READINESS GATES
critical-app    0/1     Pending   0          42s    <none>      <none>         <none>           <none>
high-prio-pod   1/1     Running   0          119s   10.50.0.5   controlplane   <none>           <none>
low-app         1/1     Running   0          42s    10.50.0.6   controlplane   <none>           <none>
low-prio-pod    1/1     Running   0          8m9s   10.50.0.4   controlplane   <none>           <none>
```

### The pod critical-app is stuck in Pending state due to the low-app pod getting scheduled and requesting high resources.

Check the status of the critical-app pod using the following command:  
```sh
kubectl describe pod critical-app
```  
```yaml
Name:             critical-app
Namespace:        default
Priority:         0
Service Account:  default
Node:             <none>
Labels:           <none>
Annotations:      <none>
Status:           Pending
IP:               
IPs:              <none>
Containers:
  critical-container:
    Image:      nginx
    Port:       <none>
    Host Port:  <none>
    Limits:
      memory:  3Gi
    Requests:
      cpu:        1
      memory:     3Gi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-z7846 (ro)
Conditions:
  Type           Status
  PodScheduled   False 
Volumes:
  kube-api-access-z7846:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason            Age                From               Message
  ----     ------            ----               ----               -------
  Warning  FailedScheduling  55s (x3 over 11m)  default-scheduler  0/1 nodes are available: 1 Insufficient cpu, 1 Insufficient memory. preemption: 0/1 nodes are available: 1 No preemption victims found for incoming pod.
```  
### To resolve this situation and get the critical-app to a running state, you should:
- Assign the high-priority class to the critical-app
- Delete and recreate the pod with the new priority
- Make sure the pod is in running state after applying the actions.

```sh
kubectl get pods critical-app --namespace=default -o yaml > critical-app.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"name":"critical-app","namespace":"default"},"spec":{"containers":[{"image":"nginx","name":"critical-container","resources":{"limits":{"memory":"3Gi"},"requests":{"cpu":"1","memory":"3Gi"}}}]}}
  creationTimestamp: "2025-09-02T01:33:52Z"
  generation: 1
  name: critical-app
  namespace: default
  resourceVersion: "6127"
  uid: f4dec54b-1f8b-48b5-a009-c5ce0add764a
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: critical-container
    resources:
      limits:
        memory: 3Gi
      requests:
        cpu: "1"
        memory: 3Gi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-z7846
      readOnly: true
  priorityClassName: high-priority
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
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
  - name: kube-api-access-z7846
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
    lastTransitionTime: "2025-09-02T01:33:52Z"
    message: '0/1 nodes are available: 1 Insufficient cpu, 1 Insufficient memory.
      preemption: 0/1 nodes are available: 1 No preemption victims found for incoming
      pod.'
    reason: Unschedulable
    status: "False"
    type: PodScheduled
  phase: Pending
  qosClass: Burstable
```  
```sh
kubectl delete -f critical-app.yaml
kubectl apply -f critical-app.yaml
```