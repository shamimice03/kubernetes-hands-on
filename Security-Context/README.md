# Security Context:

### Applied on both pod and container levels:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
  - name: sec-ctx-demo
    image: busybox:1.28
    command: [ "sh", "-c", "sleep 1h" ]
    securityContext:
      allowPrivilegeEscalation: false
```

### Pod-level: The securityContext subsection sets the security context for the containers running within the Pod. 
It specifies the user ID (runAsUser) as 1000, group ID (runAsGroup) as 3000, and the file system group (fsGroup) as 2000.
```yaml
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
```

### Container-level:
The securityContext subsection within the container definition sets the security context specifically for this container. 
It sets `allowPrivilegeEscalation` to `false`, which means the container will not be allowed to escalate its privileges.
```yaml
  securityContext:
      allowPrivilegeEscalation: false
```

> Note: When we say that a container is not allowed to escalate its privileges, it means that the container is restricted from gaining additional privileges or permissions beyond what it was initially granted when it started.

### Applied on container levels:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo-2
spec:
  securityContext:
    runAsUser: 1000
  containers:
  - name: sec-ctx-demo-2
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      runAsUser: 2000
      allowPrivilegeEscalation: false
```

### Set capabilities for a Container ( Only available on container level )
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo-4
spec:
  containers:
  - name: sec-ctx-4
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```
The capabilities field allows fine-grained control over the Linux capabilities that the container will have.
Linux capabilities are distinct privileges that can be granted to processes, allowing them to perform specific actions. 
In this case, the add field specifies that two capabilities, `NET_ADMIN` and `SYS_TIME`, should be added to the container's capabilities.

 `NET_ADMIN`  capability allows the container to perform various network-related administrative tasks.
 `SYS_TIME` capability allows the container to set system time and date.
By adding these capabilities to the container, it gains the ability to perform these privileged actions within its own namespace.

