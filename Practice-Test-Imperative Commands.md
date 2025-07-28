# Practice Test - Imperative Commands

### Deploy a pod named nginx-pod using the nginx:alpine image.
```sh
kubectl run nginx-pod --image=nginx:alpine
```

### Deploy a redis pod using the redis:alpine image with the labels set to tier=db.
```sh
kubectl run redis --image=redis:alpine
kubectl label pod redis tier=db --dry-run=client -o yaml > redis-add-label.yaml
kubectl apply -f redis-add-label.yaml
```

### Create a service named redis-service to expose the existing redis pod within the cluster on port 6379
```sh
kubectl expose pods redis --port=6379 --type=ClusterIP --name=redis-service
```

### Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas.
```sh
kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3
```

### Create a new pod called custom-nginx using the nginx image and run it on container port 8080.
```sh
kubectl run custom-nginx --image=nginx --port=8080
```

### Create a new namespace called dev-ns.
```sh
kubectl create namespace dev-ns
```

### Create a new deployment called redis-deploy in the dev-ns namespace with the redis image. It should have 2 replicas.
```sh
kubectl create deployment redis-deploy --namespace=dev-ns --image=redis --replicas=2
```

### Create a pod named httpd using the image httpd:alpine in the default namespace. Then, create a service of type ClusterIP with the same name (httpd) that exposes the pod on port 80.
```sh
kubectl run httpd --image=httpd:alpine --namespace=default
kubectl expose pod httpd --name=httpd --port=80 --type=ClusterIP
```


