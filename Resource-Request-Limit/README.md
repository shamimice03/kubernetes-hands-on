### Request for resource 
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: app
    image: images.my-company.example/app:v4
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
  - name: log-aggregator
    image: images.my-company.example/log-aggregator:v6
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

![screenshot-www udemy com-2023 07 13-16_04_55](https://github.com/shamimice03/kubernetes-hands-on/assets/19708705/82aca026-0109-4b0c-9775-0eb1c7ca704a)

![screenshot-www udemy com-2023 07 13-16_13_56](https://github.com/shamimice03/kubernetes-hands-on/assets/19708705/915fa816-8f13-40ce-b611-cb735f3e2db2)

