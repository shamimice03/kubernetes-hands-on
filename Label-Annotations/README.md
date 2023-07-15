### Add Labels
```
> k label pod pod-1 'id=app' 'test=user
```

### Show Labels
```
> k get pods --show-labels
NAME    READY   STATUS    RESTARTS   AGE   LABELS
pod-1   1/1     Running   0          39s   type=runner
pod-2   1/1     Running   0          27s   type=runner
pod-3   1/1     Running   0          12s   type=worker
```

### Add Labels to the multiple pods:
Add label to the pods with existing label `type: runner`
```
> k label -l type=runner pod 'protected=true'
pod/pod-1 labeled
pod/pod-2 labeled

>k get pods --show-labels
NAME    READY   STATUS    RESTARTS   AGE     LABELS
pod-1   1/1     Running   0          5m14s   protected=true,type=runner
pod-2   1/1     Running   0          5m2s    protected=true,type=runner
pod-3   1/1     Running   0          4m47s   type=worker
```

### Add annotation to a single pod:
```
> k annotate pod pod-3 'protected=do not delete this pod'
pod/pod-3 annotate
```
### Annotate multiple pods:
```
> k annotate -l type=runner pod 'protected=do not delete this pod'
pod/pod-1 annotate
pod/pod-2 annotate
```
