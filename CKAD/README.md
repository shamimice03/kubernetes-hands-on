### Pods
```
# Create a new pod
> k run nginx-pod --image=nginx

# Image used to create pod
 > k describe pods | grep -i "Image:"
    Image:          nginx
    Image:         busybox
    Image:         busybox
    Image:         busybox

# Which nodes are these pods placed on?
> k get pods -o wide
NAME            READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
nginx           1/1     Running   0          2m58s   10.42.0.9    controlplane   <none>           <none>
newpods-fvwcm   1/1     Running   0          2m      10.42.0.11   controlplane   <none>           <none>
newpods-nlxnq   1/1     Running   0          2m      10.42.0.12   controlplane   <none>           <none>
newpods-9sf8x   1/1     Running   0          2m      10.42.0.10   controlplane   <none>           <none>

# Images used to create a pod
> k describe pod  webapp | grep -i image:
    Image:          nginx
    Image:          agentx

# Delete a pod
> k delete pod webapp

# Generate YAML for pod
> k run redis --image=redis123 --dry-run=client -o yaml > redis.yaml

# Pod with commands
> k run yellow --image=busybox --dry-run=client -o yaml --command -- sleep 1000 
```

### ReplicaSets
```
# List ReplicaSets
> k get rs

> k describe rs new-replica-set

```

### Deployment
```
# Create deployment
> k create deploy httpd-frontend --image=httpd:2.4-alpine --replicas 3 
```

### Namespace
```
# Number of namespaces
>  k get ns --no-headers | wc -l

# Number of pods in a specific namespace
>  k get pods -n research --no-headers | wc -l

# Create pod on specific namespace
>  k run redis --image=redis -n finance
```

### Labels using imperative commands:
```
> k run redis --image=redis:alpine --labels='tier=db' --dry-run=client -o yaml
> k run redis --image=redis:alpine --labels='tier=db,app=webapp' --dry-run=client -o yaml
```

### Imperative command to expose a pod
```
>  k expose pod redis --port=6379 --name=redis-service --type=ClusterIP
```

### Logs
```
> k logs -f <pod-name>

# For multiple containers
> k logs -f <pod-name> <container-name>

```

### Ingress
```
# Create Ingress Resource
> k create ingress wear-watch --rule=/wear=wear-service:8080 --rule=/watch=video-service:8080
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  creationTimestamp: null
  name: wear-watch
spec:
  rules:
  - http:
      paths:
      - backend:
          service:
            name: wear-service
            port:
              number: 8080
        path: /wear
        pathType: Exact
      - backend:
          service:
            name: video-service
            port:
              number: 8080
        path: /watch
        pathType: Exact
status:
  loadBalancer: {}
```

```
# Annotate
> k annotate ingress wear-watch nginx.ingress.kubernetes.io/rewrite-target=/
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
  creationTimestamp: "2023-07-10T02:26:58Z"
  generation: 1
  name: wear-watch
  namespace: app-space
  resourceVersion: "2564"
  uid: 8bf23f30-70c1-43d5-afd1-e7951db3e325
spec:
  rules:
  - http:
      paths:
      - backend:
          service:
            name: wear-service
            port:
              number: 8080
        path: /wear
        pathType: Exact
      - backend:
          service:
            name: video-service
            port:
              number: 8080
        path: /watch
        pathType: Exact
status:
  loadBalancer:
    ingress:
    - ip: 10.99.137.30
```

### API-Versions
- Enable Or Disable A Kubernetes API
- Add `runtime-config` filed on kube-api server manifests
- Enable the `v1alpha1` version for `rbac.authorization.k8s.io` API group on the controlplane node.
- Add the `--runtime-config=rbac.authorization.k8s.io/v1alpha1` option to the `kube-apiserver.yaml` file.
- `kubectl explain deployment` to view preferred version

### `kubectl convert` 
- Install https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/
```
# Change the deprecated API version to the networking.k8s.io/v1 and create the resource
> kubectl convert -f ingress-old.yaml --output-version networking.k8s.io/v1 > ingress.yaml
```
