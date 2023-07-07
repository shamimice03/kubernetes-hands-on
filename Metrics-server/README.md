### Overview:
https://github.com/kubernetes-sigs/metrics-server

### Architecture:
![image](https://github.com/shamimice03/kubernetes-hands-on/assets/19708705/8a3574e9-a4cd-450d-b30b-be87f9096847)

- kubelet has a built-in Node and Pod Metric Collector based on Linux's Cgroup called cAdvisor. 
Metrics collected by cAdvisor are exposed to the outside through kubelet's 10250 Port's /stat Path.

- The Metric Server obtains the access information of the kubelet running on the Node from the Kubernetes API Server, 
and then collects the metrics of the Node and Pod from the kubelet. Collected metrics are stored in memory. 
Therefore, when Metric Server is restarted, all collected metric information disappears.
Metric Server registers Metric Service connected to Metric Server as metric.k8s.io API using Kubernetes' API Aggregation function. 
Therefore, Kubernetes components that need metric information from Metric Server do not bring metrics directly from Metric Server or Metric Service, but through Kubernetes API Server.

- After installing: View matrics
```
< kubectl top <node / pod>
```

### Refs:
- https://ssup2.github.io/theory_analysis/Kubernetes_Metric_Server/
