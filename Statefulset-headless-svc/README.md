### What is `StatefulSet` in Kubernetes ? Why StatefulSet is important?

In Kubernetes, a StatefulSet is a controller object that manages the deployment and scaling of stateful applications. It is specifically designed for applications that require stable network identities, persistent storage, and ordered deployment or scaling.

StatefulSets are important for several reasons:

1. Stable Network Identities: StatefulSets assign a unique and stable hostname and network identity to each pod they manage. This allows applications to rely on these identities for communication and coordination. For example, databases often require stable network identities to maintain data consistency and replication.

2. Persistent Storage: StatefulSets provide support for persistent volumes, allowing each pod to have its own dedicated storage. This is crucial for stateful applications that need to store data persistently, such as databases or distributed file systems. StatefulSets ensure that the same persistent volume is attached to the same pod even during rescheduling or scaling events.

3. Ordered Deployment and Scaling: StatefulSets maintain a defined order when creating or scaling pods. This order is based on the ordinal index assigned to each pod. The ordinal index is used to create stable network identities, ensure proper bootstrapping of applications, and facilitate orderly scaling. This feature is essential for applications that have dependencies on specific pod ordering.

4. Rolling Updates and Rollbacks: StatefulSets support rolling updates, allowing you to update your stateful application in a controlled manner. During a rolling update, each pod is updated one at a time, ensuring that the application remains available and consistent throughout the process. If an update fails, you can easily roll back to the previous version.

5. Integration with Headless Services: StatefulSets work seamlessly with headless services in Kubernetes. Headless services enable direct, DNS-based communication to individual pods in a StatefulSet, which is important for distributed applications or databases that require direct access to specific pods.

By combining these features, StatefulSets provide a reliable and scalable solution for running stateful applications in Kubernetes, ensuring stable network identities, persistent storage, and orderly deployment and scaling.


### What is Headless Service in Kubernetes? How it works with Statefulset?
In Kubernetes, a headless service is a service that does not assign a cluster IP to the underlying pods. Instead, it allows direct DNS-based communication to individual pods in a StatefulSet. When you create a headless service, Kubernetes sets up DNS records for each pod in the StatefulSet, making it possible to access the pods directly by their hostname.

Here's how a headless service works with StatefulSets:

1. StatefulSet: A StatefulSet is a Kubernetes controller used to manage stateful applications, such as databases, where each pod has a unique identity and stable network identity.

2. Headless Service: When you create a headless service for a StatefulSet, Kubernetes creates a DNS record for each pod. These DNS records follow a specific naming convention based on the StatefulSet name and the ordinal index of the pod.

3. DNS Resolution: When you perform a DNS lookup for the headless service, you receive multiple IP addresses corresponding to the pods in the StatefulSet. Each IP address corresponds to a specific pod, and you can access the pods directly using their IP or hostname.

4. Direct Communication: With the DNS records in place, other applications or services within the Kubernetes cluster can communicate directly with individual pods in the StatefulSet. This direct communication is beneficial for scenarios like distributed databases, where you may need to target specific pods for read or write operations.

5. StatefulSet Operations: Headless services also play a crucial role in StatefulSet operations, such as scaling or rolling updates. When scaling up or down, the StatefulSet controller ensures that the DNS records are updated accordingly to reflect the changes in the number of pods.

Overall, headless services enable direct, DNS-based communication to individual pods in a StatefulSet, facilitating unique and stable network identities for each pod in a stateful application.

### Hands-on:

- ##### Let's create a `Headless Service`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-headless-service
spec:
  clusterIP: None # clusterIP: None ensures that no cluster IP is assigned, making it a headless service
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80

```


- ##### Create a `Statefulset` with 3 replica and assoicate with the `Headless Service`

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: state-app  # The name of the StatefulSet
spec:
  selector:
    matchLabels:
      app: my-app  # Selects the pods that match this label
  serviceName: "my-headless-service"  # Specifies the headless service to use for network identity
  replicas: 3  # The desired number of replicas for the StatefulSet
  minReadySeconds: 10  # The minimum number of seconds that a pod should be ready before considering it available
  template:
    metadata:
      labels:
        app: my-app  # Labels to apply to the pods created by the StatefulSet
    spec:
      terminationGracePeriodSeconds: 10  # The number of seconds that Kubernetes waits before forcefully terminating a pod during shutdown
      containers:
      - name: nginx  # The name of the container
        image: registry.k8s.io/nginx-slim:0.8  # The container image to use
        ports:
        - containerPort: 80  # The port on which the container listens
          name: web  # The name of the port
        volumeMounts:
        - name: www  # The name of the volume to mount
          mountPath: /usr/share/nginx/html  # The path in the container where the volume should be mounted
  volumeClaimTemplates:
  - metadata:
      name: www  # The name of the volume claim template
    spec:
      accessModes: [ "ReadWriteOnce" ]  # The access modes for the volume claim
      storageClassName: "vultr-block-storage-hdd"  # The storage class to use for provisioning the volume
      resources:
        requests:
          storage: 40Gi  # The amount of storage requested for the volume claim


```

- ##### For testing create a pod using `ubuntu` image:

```bash
Kubectl run ubuntu --image=ubuntu --command -- sleep 3000
```

Let's verify workload running perfectly:
```sh

> kubectl get all -o wide

***
NAME              READY   STATUS    RESTARTS   AGE   IP              NODE                 NOMINATED NODE   READINESS GATES
pod/state-app-0   1/1     Running   0          18m   10.244.30.17    ng-01-42b34cc75bdd   <none>           <none>
pod/state-app-1   1/1     Running   0          17m   10.244.148.12   ng-01-8f376c8b8f90   <none>           <none>
pod/state-app-2   1/1     Running   0          17m   10.244.30.18    ng-01-42b34cc75bdd   <none>           <none>
pod/ubuntu        1/1     Running   0          18m   10.244.30.16    ng-01-42b34cc75bdd   <none>           <none>

NAME                          TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE     SELECTOR
service/kubernetes            ClusterIP   10.96.0.1    <none>        443/TCP   3h14m   <none>
service/my-headless-service   ClusterIP   None         <none>        80/TCP    18m     app=my-app

NAME                         READY   AGE   CONTAINERS   IMAGES
statefulset.apps/state-app   3/3     18m   nginx        registry.k8s.io/nginx-slim:0.8

***
```

### Test

- `exec` into the ubuntu pod and install `nslookup`:

```sh
kubectl exec -it ubuntu -- bash

>> ubuntu$ apt-get update -y
>> ubuntu$ apt-get install dnsutil -y
```

- Lets try to resolve `headless-service` 
```sh
root@ubuntu:/# nslookup my-headless-service.default.svc.cluster.local

Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   my-headless-service.default.svc.cluster.local
Address: 10.244.148.12
Name:   my-headless-service.default.svc.cluster.local
Address: 10.244.30.17
Name:   my-headless-service.default.svc.cluster.local
Address: 10.244.30.18

```

In the above result, when we performed a DNS lookup for the `headless service`, we  received multiple IP addresses corresponding to the pods in the StatefulSet. Each IP address corresponds to a specific pod, and we can access the pods directly using their IP or hostname.

- Let's try to resolve pod using their `hostname` (part of `statefulset`) 

```sh
root@ubuntu:/# nslookup state-app-0.my-headless-service.default.svc.cluster.local                                       
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   state-app-0.my-headless-service.default.svc.cluster.local
Address: 10.244.30.17

root@ubuntu:/# nslookup state-app-1.my-headless-service.default.svc.cluster.local                                       
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   state-app-1.my-headless-service.default.svc.cluster.local
Address: 10.244.148.12

root@ubuntu:/# nslookup state-app-2.my-headless-service.default.svc.cluster.local                                       
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   state-app-2.my-headless-service.default.svc.cluster.local
Address: 10.244.30.18
```
