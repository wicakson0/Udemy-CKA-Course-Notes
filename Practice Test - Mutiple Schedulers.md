# Practice Test - Multiple Schedulers

### What is the name of the POD that deploys the default kubernetes scheduler in this environment?
```sh
kubectl get pods -A
```  
```sh
NAMESPACE      NAME                                   READY   STATUS    RESTARTS   AGE
kube-flannel   kube-flannel-ds-vg58t                  1/1     Running   0          14m
kube-system    coredns-7484cd47db-lwm48               1/1     Running   0          14m
kube-system    coredns-7484cd47db-xwcnx               1/1     Running   0          14m
kube-system    etcd-controlplane                      1/1     Running   0          14m
kube-system    kube-apiserver-controlplane            1/1     Running   0          14m
kube-system    kube-controller-manager-controlplane   1/1     Running   0          14m
kube-system    kube-proxy-jnqmk                       1/1     Running   0          14m
kube-system    kube-scheduler-controlplane            1/1     Running   0          14m
```  
`kube-scheduler-controlplane`

### What is the image used to deploy the kubernetes scheduler?
```sh
kubectl describe pods kube-scheduler-controlplane --namespace=kube-system
```  
```yaml
Name:                 kube-scheduler-controlplane
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 controlplane/192.168.231.144
Start Time:           Tue, 02 Sep 2025 01:49:51 +0000
Labels:               component=kube-scheduler
                      tier=control-plane
Annotations:          kubernetes.io/config.hash: 95c51cd054d6eeddb3f9ad5bc02e0fb9
                      kubernetes.io/config.mirror: 95c51cd054d6eeddb3f9ad5bc02e0fb9
                      kubernetes.io/config.seen: 2025-09-02T01:49:42.046944538Z
                      kubernetes.io/config.source: file
Status:               Running
SeccompProfile:       RuntimeDefault
IP:                   192.168.231.144
IPs:
  IP:           192.168.231.144
Controlled By:  Node/controlplane
Containers:
  kube-scheduler:
    Container ID:  containerd://75e7433d1d8014af5f45f2d3939432d0e7fbac45e6761a5ebc255134ad0009d4
    Image:         registry.k8s.io/kube-scheduler:v1.33.0
    Image ID:      registry.k8s.io/kube-scheduler@sha256:8dd2fbeb7f711da53a89ded239e54133f34110d98de887a39a9021e651b51f1f
    Port:          <none>
    Host Port:     <none>
    Command:
      kube-scheduler
      --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
      --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
      --bind-address=127.0.0.1
      --kubeconfig=/etc/kubernetes/scheduler.conf
      --leader-elect=true
    State:          Running
      Started:      Tue, 02 Sep 2025 01:49:44 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
    Liveness:     http-get https://127.0.0.1:10259/livez delay=10s timeout=15s period=10s #success=1 #failure=8
    Readiness:    http-get https://127.0.0.1:10259/readyz delay=0s timeout=15s period=1s #success=1 #failure=3
    Startup:      http-get https://127.0.0.1:10259/livez delay=10s timeout=15s period=10s #success=1 #failure=24
    Environment:  <none>
    Mounts:
      /etc/kubernetes/scheduler.conf from kubeconfig (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kubeconfig:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/kubernetes/scheduler.conf
    HostPathType:  FileOrCreate
QoS Class:         Burstable
Node-Selectors:    <none>
Tolerations:       :NoExecute op=Exists
Events:
  Type     Reason     Age   From     Message
  ----     ------     ----  ----     -------
  Warning  Unhealthy  20m   kubelet  Readiness probe failed: HTTP probe failed with statuscode: 500
```  
`registry.k8s.io/kube-scheduler:v1.33.0`

### We have already created the ServiceAccount and ClusterRoleBinding that our custom scheduler will make use of.
Checkout the following Kubernetes objects:  
- `ServiceAccount`: my-scheduler (kube-system namespace)
- `ClusterRoleBinding`: my-scheduler-as-kube-scheduler
- `ClusterRoleBinding`: my-scheduler-as-volume-scheduler

Run the commnad:  
```sh
kubectl get serviceaccount -n kube-system
```  
```sh
NAME                                          SECRETS   AGE
attachdetach-controller                       0         23m
bootstrap-signer                              0         23m
certificate-controller                        0         23m
clusterrole-aggregation-controller            0         23m
coredns                                       0         23m
cronjob-controller                            0         23m
daemon-set-controller                         0         23m
default                                       0         23m
deployment-controller                         0         23m
disruption-controller                         0         23m
endpoint-controller                           0         23m
endpointslice-controller                      0         23m
endpointslicemirroring-controller             0         23m
ephemeral-volume-controller                   0         23m
expand-controller                             0         23m
generic-garbage-collector                     0         23m
horizontal-pod-autoscaler                     0         23m
job-controller                                0         23m
kube-proxy                                    0         23m
legacy-service-account-token-cleaner          0         23m
my-scheduler                                  0         2m1s
namespace-controller                          0         23m
node-controller                               0         23m
persistent-volume-binder                      0         23m
pod-garbage-collector                         0         23m
pv-protection-controller                      0         23m
pvc-protection-controller                     0         23m
replicaset-controller                         0         23m
replication-controller                        0         23m
resourcequota-controller                      0         23m
root-ca-cert-publisher                        0         23m
service-account-controller                    0         23m
service-cidrs-controller                      0         23m
statefulset-controller                        0         23m
token-cleaner                                 0         23m
ttl-after-finished-controller                 0         23m
ttl-controller                                0         23m
validatingadmissionpolicy-status-controller   0         23m
```  
```sh
kubectl get clusterrolebinding
```  
```yaml
NAME                                                            ROLE                                                                               AGE
cluster-admin                                                   ClusterRole/cluster-admin                                                          26m
flannel                                                         ClusterRole/flannel                                                                26m
kubeadm:cluster-admins                                          ClusterRole/cluster-admin                                                          26m
kubeadm:get-nodes                                               ClusterRole/kubeadm:get-nodes                                                      26m
kubeadm:kubelet-bootstrap                                       ClusterRole/system:node-bootstrapper                                               26m
kubeadm:node-autoapprove-bootstrap                              ClusterRole/system:certificates.k8s.io:certificatesigningrequests:nodeclient       26m
kubeadm:node-autoapprove-certificate-rotation                   ClusterRole/system:certificates.k8s.io:certificatesigningrequests:selfnodeclient   26m
kubeadm:node-proxier                                            ClusterRole/system:node-proxier                                                    26m
my-scheduler-as-kube-scheduler                                  ClusterRole/system:kube-scheduler                                                  5m26s
my-scheduler-as-volume-scheduler                                ClusterRole/system:volume-scheduler                                                5m26s
system:basic-user                                               ClusterRole/system:basic-user                                                      26m
system:controller:attachdetach-controller                       ClusterRole/system:controller:attachdetach-controller                              26m
system:controller:certificate-controller                        ClusterRole/system:controller:certificate-controller                               26m
system:controller:clusterrole-aggregation-controller            ClusterRole/system:controller:clusterrole-aggregation-controller                   26m
system:controller:cronjob-controller                            ClusterRole/system:controller:cronjob-controller                                   26m
system:controller:daemon-set-controller                         ClusterRole/system:controller:daemon-set-controller                                26m
system:controller:deployment-controller                         ClusterRole/system:controller:deployment-controller                                26m
system:controller:disruption-controller                         ClusterRole/system:controller:disruption-controller                                26m
system:controller:endpoint-controller                           ClusterRole/system:controller:endpoint-controller                                  26m
system:controller:endpointslice-controller                      ClusterRole/system:controller:endpointslice-controller                             26m
system:controller:endpointslicemirroring-controller             ClusterRole/system:controller:endpointslicemirroring-controller                    26m
system:controller:ephemeral-volume-controller                   ClusterRole/system:controller:ephemeral-volume-controller                          26m
system:controller:expand-controller                             ClusterRole/system:controller:expand-controller                                    26m
system:controller:generic-garbage-collector                     ClusterRole/system:controller:generic-garbage-collector                            26m
system:controller:horizontal-pod-autoscaler                     ClusterRole/system:controller:horizontal-pod-autoscaler                            26m
system:controller:job-controller                                ClusterRole/system:controller:job-controller                                       26m
system:controller:legacy-service-account-token-cleaner          ClusterRole/system:controller:legacy-service-account-token-cleaner                 26m
system:controller:namespace-controller                          ClusterRole/system:controller:namespace-controller                                 26m
system:controller:node-controller                               ClusterRole/system:controller:node-controller                                      26m
system:controller:persistent-volume-binder                      ClusterRole/system:controller:persistent-volume-binder                             26m
system:controller:pod-garbage-collector                         ClusterRole/system:controller:pod-garbage-collector                                26m
system:controller:pv-protection-controller                      ClusterRole/system:controller:pv-protection-controller                             26m
system:controller:pvc-protection-controller                     ClusterRole/system:controller:pvc-protection-controller                            26m
system:controller:replicaset-controller                         ClusterRole/system:controller:replicaset-controller                                26m
system:controller:replication-controller                        ClusterRole/system:controller:replication-controller                               26m
system:controller:resourcequota-controller                      ClusterRole/system:controller:resourcequota-controller                             26m
system:controller:root-ca-cert-publisher                        ClusterRole/system:controller:root-ca-cert-publisher                               26m
system:controller:route-controller                              ClusterRole/system:controller:route-controller                                     26m
system:controller:selinux-warning-controller                    ClusterRole/system:controller:selinux-warning-controller                           26m
system:controller:service-account-controller                    ClusterRole/system:controller:service-account-controller                           26m
system:controller:service-cidrs-controller                      ClusterRole/system:controller:service-cidrs-controller                             26m
system:controller:service-controller                            ClusterRole/system:controller:service-controller                                   26m
system:controller:statefulset-controller                        ClusterRole/system:controller:statefulset-controller                               26m
system:controller:ttl-after-finished-controller                 ClusterRole/system:controller:ttl-after-finished-controller                        26m
system:controller:ttl-controller                                ClusterRole/system:controller:ttl-controller                                       26m
system:controller:validatingadmissionpolicy-status-controller   ClusterRole/system:controller:validatingadmissionpolicy-status-controller          26m
system:coredns                                                  ClusterRole/system:coredns                                                         26m
system:discovery                                                ClusterRole/system:discovery                                                       26m
system:kube-controller-manager                                  ClusterRole/system:kube-controller-manager                                         26m
system:kube-dns                                                 ClusterRole/system:kube-dns                                                        26m
system:kube-scheduler                                           ClusterRole/system:kube-scheduler                                                  26m
system:monitoring                                               ClusterRole/system:monitoring                                                      26m
system:node                                                     ClusterRole/system:node                                                            26m
system:node-proxier                                             ClusterRole/system:node-proxier                                                    26m
system:public-info-viewer                                       ClusterRole/system:public-info-viewer                                              26m
system:service-account-issuer-discovery                         ClusterRole/system:service-account-issuer-discovery                                26m
system:volume-scheduler                                         ClusterRole/system:volume-scheduler                                                26m
```  

### Please create a ConfigMap that the new scheduler will utilize, implementing the concept of ConfigMap as a volume. A ConfigMap definition file named my-scheduler-configmap.yaml has been provided at the /root/ path. This file will be used to create a ConfigMap with the name my-scheduler-config, utilizing the content from the file located at /root/my-scheduler-config.yaml.
```yaml
# my-scheduler-configmap.yaml
apiVersion: v1
data:
  my-scheduler-config.yaml: |
    apiVersion: kubescheduler.config.k8s.io/v1
    kind: KubeSchedulerConfiguration
    profiles:
      - schedulerName: my-scheduler
    leaderElection:
      leaderElect: false
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: my-scheduler-config
  namespace: kube-system
```  
```yaml
# my-scheduler-config.yaml
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
  - schedulerName: my-scheduler
leaderElection:
  leaderElect: false
```  
```yaml
# my-scheduler.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: my-scheduler
  name: my-scheduler
  namespace: kube-system
spec:
  serviceAccountName: my-scheduler
  containers:
  - command:
    - /usr/local/bin/kube-scheduler
    - --config=/etc/kubernetes/my-scheduler/my-scheduler-config.yaml
    image: registry.k8s.io/kube-scheduler:v1.33.0
    livenessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
      initialDelaySeconds: 15
    name: kube-second-scheduler
    readinessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
    resources:
      requests:
        cpu: '0.1'
    securityContext:
      privileged: false
    volumeMounts:
      - name: config-volume
        mountPath: /etc/kubernetes/my-scheduler
  hostNetwork: false
  hostPID: false
  volumes:
    - name: config-volume
      configMap:
        name: my-scheduler-config
```  
```sh
kubectl apply -f my-scheduler-configmap.yaml
```

# Deploy an additional scheduler to the cluster following the given specification.
Utilize the manifest file located at /root/my-scheduler.yaml. Ensure that you are using the same image as that of the default Kubernetes scheduler.  
To verify the image used by the default Kubernetes scheduler, execute the following command:  
```sh
kubectl describe pod kube-scheduler-controlplane --namespace=kube-system
```  
```sh
kubectl apply -f my-scheduler.yaml
```

# Please modify the provided Pod manifest file located at /root/nginx-pod.yaml to specify that the Pod should be scheduled by your custom scheduler, which is named my-scheduler.
```yaml
# nginx-pod.yaml
apiVersion: v1 
kind: Pod 
metadata:
  name: nginx 
spec:
  containers:
  - image: nginx
    name: nginx
```  
```sh
kubectl apply -f nginx-pod.yaml
```