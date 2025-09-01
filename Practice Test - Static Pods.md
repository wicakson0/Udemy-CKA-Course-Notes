# Practice Test - Static Pods

### How many static pods exist in this cluster in all namespaces?
```sh
kubectl get pods -A
```  
```yaml
NAMESPACE      NAME                                   READY   STATUS    RESTARTS   AGE
kube-flannel   kube-flannel-ds-85kqd                  1/1     Running   0          7m30s
kube-flannel   kube-flannel-ds-md2tf                  1/1     Running   0          7m2s
kube-system    coredns-7484cd47db-8rnbq               1/1     Running   0          7m30s
kube-system    coredns-7484cd47db-r2ndt               1/1     Running   0          7m30s
kube-system    etcd-controlplane                      1/1     Running   0          7m35s
kube-system    kube-apiserver-controlplane            1/1     Running   0          7m38s
kube-system    kube-controller-manager-controlplane   1/1     Running   0          7m35s
kube-system    kube-proxy-6ttvq                       1/1     Running   0          7m2s
kube-system    kube-proxy-wxf6d                       1/1     Running   0          7m30s
kube-system    kube-scheduler-controlplane            1/1     Running   0          7m35s
```  
there are 4 static pods  

### Which of the below components is NOT deployed as a static pod?
`coredns`  

### Which of the below components is NOT deployed as a static POD?
`kube-proxy`

### Which of the below components is NOT deployed as a static POD?
`controlplane`

### What is the path of the directory holding the static pod definition files?
`/etc/kubernetes/manifests`

### How many pod definition files are present in the manifests directory?
```sh
ls -la /etc/kubernetes/manifests
```  
```yaml
total 28
drwxrwxr-x 1 root root 4096 Sep  1 12:37 .
drwxrwxr-x 1 root root 4096 Sep  1 12:37 ..
-rw------- 1 root root 2559 Sep  1 12:37 etcd.yaml
-rw------- 1 root root 3893 Sep  1 12:37 kube-apiserver.yaml
-rw------- 1 root root 3394 Sep  1 12:37 kube-controller-manager.yaml
-rw-r--r-- 1 root root    0 Apr 23 13:47 .kubelet-keep
-rw------- 1 root root 1656 Sep  1 12:37 kube-scheduler.yaml
```

### What is the docker image used to deploy the kube-api server as a static pod?
```sh
cat kube-apiserver.yaml | grep image
```  
```yaml
image: registry.k8s.io/kube-apiserver:v1.33.0
imagePullPolicy: IfNotPresent
```

### Create a static pod named static-busybox that uses the busybox image , run in the default namespace and the command sleep 1000
```sh
vi static-busybox.yaml
```  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-busybox
spec:
  containers:
    - name: busybox
      image: busybox:1.28.4
      command: ["sh", "-c", "sleep 1000"]
  restartPolicy: Never
```

### Edit the image on the static pod you created in the previous task to use busybox:1.28.4
```sh
ssh root@node01
cat /var/lib/kubelet/config.yaml
```  
```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.crt
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 0s
    cacheUnauthorizedTTL: 0s
cgroupDriver: cgroupfs
clusterDNS:
- 172.20.0.10
clusterDomain: cluster.local
containerRuntimeEndpoint: ""
cpuManagerReconcilePeriod: 0s
crashLoopBackOff: {}
evictionPressureTransitionPeriod: 0s
fileCheckFrequency: 0s
healthzBindAddress: 127.0.0.1
healthzPort: 10248
httpCheckFrequency: 0s
imageMaximumGCAge: 0s
imageMinimumGCAge: 0s
kind: KubeletConfiguration
logging:
  flushFrequency: 0
  options:
    json:
      infoBufferSize: "0"
    text:
      infoBufferSize: "0"
  verbosity: 0
memorySwap: {}
nodeStatusReportFrequency: 0s
nodeStatusUpdateFrequency: 0s
resolvConf: /run/systemd/resolve/resolv.conf
rotateCertificates: true
runtimeRequestTimeout: 0s
shutdownGracePeriod: 0s
shutdownGracePeriodCriticalPods: 0s
staticPodPath: /etc/just-to-mess-with-you
streamingConnectionIdleTimeout: 0s
syncFrequency: 0s
volumeStatsAggPeriod: 0s
```  
```sh
cd /etc/just-to-mess-with-you
rm greenbox.yaml
```