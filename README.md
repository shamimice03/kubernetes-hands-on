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
