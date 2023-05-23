### Kubernetes Commands | CheatSheet

- Write manifest for pod
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
- Deploy and `exec` into the pod: :

```
>> kubectl apply -f busybox.yaml 
>>  k exec -it busybox -- sh
/ # ls
bin    dev    etc    home   lib    lib64  proc   root   sys    tmp    usr    var
```
