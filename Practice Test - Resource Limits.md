# Practice Test - Resource Limits

### A pod called rabbit is deployed. Identify the CPU requirements set on the Pod
```sh
kubectl describe pods rabbit
```   
```yaml
Name:             rabbit
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.212.174
Start Time:       Mon, 01 Sep 2025 05:10:43 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.22.0.9
IPs:
  IP:  10.22.0.9
Containers:
  cpu-stress:
    Container ID:  containerd://5ba0cb508b6cd1d16400f06b5330a65dac79304a7cba907c8658d67c548768c6
    Image:         ubuntu
    Image ID:      docker.io/library/ubuntu@sha256:7c06e91f61fa88c08cc74f7e1b7c69ae24910d745357e0dfe1d2c0322aaf20f9
    Port:          <none>
    Host Port:     <none>
    Args:
      sleep
      1000
    State:          Running
      Started:      Mon, 01 Sep 2025 05:10:46 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:  1
    Requests:
      cpu:        500m
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-tsnvp (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-tsnvp:
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
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  40s   default-scheduler  Successfully assigned default/rabbit to controlplane
  Normal  Pulling    39s   kubelet            Pulling image "ubuntu"
  Normal  Pulled     38s   kubelet            Successfully pulled image "ubuntu" in 1.54s (1.54s including waiting). Image size: 29732622 bytes.
  Normal  Created    38s   kubelet            Created container: cpu-stress
  Normal  Started    37s   kubelet            Started container cpu-stress
```
CPU: 0.5 (500m)  

### Delete the rabbit Pod.
```sh
kubectl delete pods rabbit
```  

### Another pod called elephant has been deployed in the default namespace. It fails to get to a running state. Inspect this pod and identify the Reason why it is not running.
```sh
kubectl logs elephant
```  
```yaml
stress: info: [1] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd
stress: FAIL: [1] (415) <-- worker 55 got signal 9
stress: WARN: [1] (417) now reaping child worker processes
stress: FAIL: [1] (421) kill error: No such process
stress: FAIL: [1] (451) failed run completed in 0s
```  
```sh
kubectl describe pods elephant
```  
```yaml
Name:             elephant
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.212.174
Start Time:       Mon, 01 Sep 2025 05:14:10 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.22.0.10
IPs:
  IP:  10.22.0.10
Containers:
  mem-stress:
    Container ID:  containerd://77ffd5dbae978864d5b5ac60be4ceff266440094ab5062298a1a577f2c8f8548
    Image:         polinux/stress
    Image ID:      docker.io/polinux/stress@sha256:b6144f84f9c15dac80deb48d3a646b55c7043ab1d83ea0a697c09097aaad21aa
    Port:          <none>
    Host Port:     <none>
    Command:
      stress
    Args:
      --vm
      1
      --vm-bytes
      15M
      --vm-hang
      1
    State:          Terminated
      Reason:       OOMKilled
      Exit Code:    1
      Started:      Mon, 01 Sep 2025 05:15:47 +0000
      Finished:     Mon, 01 Sep 2025 05:15:47 +0000
    Last State:     Terminated
      Reason:       OOMKilled
      Exit Code:    1
      Started:      Mon, 01 Sep 2025 05:15:00 +0000
      Finished:     Mon, 01 Sep 2025 05:15:00 +0000
    Ready:          False
    Restart Count:  4
    Limits:
      memory:  10Mi
    Requests:
      memory:     5Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-qvljb (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-qvljb:
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
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  100s              default-scheduler  Successfully assigned default/elephant to controlplane
  Normal   Pulled     98s               kubelet            Successfully pulled image "polinux/stress" in 741ms (741ms including waiting). Image size: 4041495 bytes.
  Normal   Pulled     98s               kubelet            Successfully pulled image "polinux/stress" in 135ms (135ms including waiting). Image size: 4041495 bytes.
  Normal   Pulled     81s               kubelet            Successfully pulled image "polinux/stress" in 138ms (138ms including waiting). Image size: 4041495 bytes.
  Normal   Pulled     50s               kubelet            Successfully pulled image "polinux/stress" in 135ms (136ms including waiting). Image size: 4041495 bytes.
  Normal   Pulling    3s (x5 over 99s)  kubelet            Pulling image "polinux/stress"
  Normal   Created    3s (x5 over 98s)  kubelet            Created container: mem-stress
  Normal   Started    3s (x5 over 98s)  kubelet            Started container mem-stress
  Normal   Pulled     3s                kubelet            Successfully pulled image "polinux/stress" in 142ms (142ms including waiting). Image size: 4041495 bytes.
  Warning  BackOff    2s (x8 over 97s)  kubelet            Back-off restarting failed container mem-stress in pod elephant_default(0b72719a-da38-43bc-9eac-88166c9303d7)
```  
OOMKilled  

### The status OOMKilled indicates that it is failing because the pod ran out of memory. Identify the memory limit set on the POD.
```sh
kubectl get pods -o yaml elephant > elephant.yaml
```
modify the resource limit.  
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-09-01T05:14:10Z"
  generation: 1
  name: elephant
  namespace: default
  resourceVersion: "1315"
  uid: 0b72719a-da38-43bc-9eac-88166c9303d7
spec:
  containers:
  - args:
    - --vm
    - "1"
    - --vm-bytes
    - 15M
    - --vm-hang
    - "1"
    command:
    - stress
    image: polinux/stress
    imagePullPolicy: Always
    name: mem-stress
    resources:
      limits:
        memory: 20Mi
      requests:
        memory: 5Mi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-qvljb
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
  - name: kube-api-access-qvljb
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
    lastTransitionTime: "2025-09-01T05:14:12Z"
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2025-09-01T05:14:10Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2025-09-01T05:15:01Z"
    message: 'containers with unready status: [mem-stress]'
    reason: ContainersNotReady
    status: "False"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-09-01T05:15:01Z"
    message: 'containers with unready status: [mem-stress]'
    reason: ContainersNotReady
    status: "False"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2025-09-01T05:14:10Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - allocatedResources:
      memory: 5Mi
    containerID: containerd://76ae03ec419a94f5707a900430095ae24122dadedc78f4da91b41ff2e9b5face
    image: docker.io/polinux/stress:latest
    imageID: docker.io/polinux/stress@sha256:b6144f84f9c15dac80deb48d3a646b55c7043ab1d83ea0a697c09097aaad21aa
    lastState:
      terminated:
        containerID: containerd://76ae03ec419a94f5707a900430095ae24122dadedc78f4da91b41ff2e9b5face
        exitCode: 1
        finishedAt: "2025-09-01T05:25:21Z"
        reason: OOMKilled
        startedAt: "2025-09-01T05:25:21Z"
    name: mem-stress
    ready: false
    resources:
      limits:
        memory: 10Mi
      requests:
        memory: 5Mi
    restartCount: 7
    started: false
    state:
      waiting:
        message: back-off 5m0s restarting failed container=mem-stress pod=elephant_default(0b72719a-da38-43bc-9eac-88166c9303d7)
        reason: CrashLoopBackOff
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-qvljb
      readOnly: true
      recursiveReadOnly: Disabled
  hostIP: 192.168.212.174
  hostIPs:
  - ip: 192.168.212.174
  phase: Running
  podIP: 10.22.0.10
  podIPs:
  - ip: 10.22.0.10
  qosClass: Burstable
  startTime: "2025-09-01T05:14:10Z"
```  
```sh
kubectl delete -f elephant.yaml
kubectl apply -f elephant.yaml
```

### Inspect the pod make sure it is running
```sh
kubectl get pods --namespace default
```

### Delete te elephant Pod
```sh
kubectl delete -f elephant.yaml
```