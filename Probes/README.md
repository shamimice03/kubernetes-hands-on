### Probes

- Liveness probes let Kubernetes know whether your app (running in a container inside Pod) is healthy.
- Readiness probes let Kubernetes know when your app (running in a container inside Pod) is ready to serve traffic.
- Startup probes let Kubernetes know whether your app (running in a container inside Pod) has properly started.

### Details and hands-on:
- https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/

### Blog post:
- https://medium.com/devops-mojo/kubernetes-probes-liveness-readiness-startup-overview-introduction-to-probes-types-configure-health-checks-206ff7c24487


### Restart Policy:
- https://www.baeldung.com/linux/kubernetes-always-vs-onfailure-restart-policy

📋 **Overview**
- Kubernetes uses restart policies to control container restarts in Pods.
- Restart policies determine under what conditions containers are automatically restarted.
- Two common restart policies are Always and OnFailure.

🔄 **Always Restart Policy**
- Default policy in Kubernetes.
- Containers restart regardless of exit status.
- Useful for keeping containers running continuously.
- Demonstrated with an example Pod that uses the Always policy.

🔁 **OnFailure Restart Policy**
- Restarts container only if it exits with an error.
- Suitable for containers that must run successfully and then stop.
- Illustrated with a Pod example using the OnFailure policy.

🔁 **Never Restart Policy**
- Won't restart 

👍 **Conclusion**
- Kubernetes restart policies enable self-healing for containers.
- Always restarts containers upon termination, while OnFailure restarts only on errors.
