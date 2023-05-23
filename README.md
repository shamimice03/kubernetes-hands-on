# Kubernetes Commands | CheatSheet
## Pod 
- Generate pod manifest with `commands`
```
>> kubectl run busybox --image=busybox --dry-run=client -o yaml --command -- sleep 3000 > busybox.yaml
>> cat busybox.yaml 

---
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - command:
    - sleep
    - "3000"
    image: busybox
    name: busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

---

```
- Generate pod manifest with `labels`
```
>> kubectl run demo-pod --image=nginx --labels='app=webapp,run=test' --dry-run=client -o yaml
---
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    app: webapp
    run: test
  name: demo-pod
spec:
  containers:
  - image: nginx
    name: demo-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

- Deploy and `exec` into the pod: :

```
>> kubectl apply -f busybox.yaml 
>>  k exec -it busybox -- sh
/ # ls
bin    dev    etc    home   lib    lib64  proc   root   sys    tmp    usr    var
```

- Show `labels`
```
kubectl get pods --show-labels 
kubectl get pods --show-labels -l app=myapp
```

***
## Service
- Generate manifest for `NodePort` service

`kubectl create service nodeport NAME [--tcp=port:targetPort] [--dry-run=server|client|none] [options]`

```
>> kubectl create service nodeport webapp-service --tcp=8080:80 --node-port=30008 --dry-run=client -o yaml

---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: webapp-service
  name: webapp-service
spec:
  ports:
  - name: 8080-80
    nodePort: 30008
    port: 8080
    protocol: TCP
    targetPort: 80
  selector:
    app: webapp-service
  type: NodePort
status:
  loadBalancer: {}

```
***
## Deployment
- Generate `deployment` manifest:
```
>> kubectl create deploy webapp --image=nginx:1.17 --replicas=3 --dry-run=client -o yaml

---
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: webapp
  name: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: webapp
    spec:
      containers:
      - image: nginx:1.17
        name: nginx
        resources: {}
status: {}

```
### Rollout and Rollback
```
>>> kubectl rollout -h
Manage the rollout of one or many resources.
  
 Valid resource types include:

  *  deployments
  *  daemonsets
  *  statefulsets

Examples:
  # Rollback to the previous deployment
  kubectl rollout undo deployment/abc
  
  # Check the rollout status of a daemonset
  kubectl rollout status daemonset/foo
  
  # Restart a deployment
  kubectl rollout restart deployment/abc
  
  # Restart deployments with the app=nginx label
  kubectl rollout restart deployment --selector=app=nginx

Available Commands:
  history       View rollout history
  pause         Mark the provided resource as paused
  restart       Restart a resource
  resume        Resume a paused resource
  status        Show the status of the rollout
  undo          Undo a previous rollout

Usage:
  kubectl rollout SUBCOMMAND [options]
```
### Example:
```
>> kubectl rollout history deploy/webapp

deployment.apps/webapp 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

>> kubectl rollout undo deploy/webapp
deployment.apps/webapp rolled back

>> kubectl rollout history deploy/webapp
deployment.apps/webapp 

REVISION  CHANGE-CAUSE
2         <none>
3         <none>
```

