### Job
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: throw-dice-job
spec:
  backoffLimit: 25  # Specifies the number of retries the Job should attempt in case of failures
  completions: 3  # Specifies the desired number of successfully completed Pods
  parallelism: 3  # Specifies the number of Pods that can run in parallel
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
      - image: kodekloud/throw-dice  # Container image to run for the task
        name: throw-dice-job  # Name of the container
      restartPolicy: OnFailure  # Defines the Pod restart policy in case of failures

```

```
> kubectl apply -f job.yaml
> kubectl get pods
NAME                   READY   STATUS      RESTARTS   AGE
throw-dice-job-624s9   0/1     Completed   0          31s
throw-dice-job-rxjx4   0/1     Completed   0          6s
throw-dice-job-t6jfv   0/1     Completed   2          27s
```
### CronJob
```
# ┌───────────── minute (0 - 59)
# │ ┌───────────── hour (0 - 23)
# │ │ ┌───────────── day of the month (1 - 31)
# │ │ │ ┌───────────── month (1 - 12)
# │ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday;
# │ │ │ │ │                                   7 is also Sunday on some systems)
# │ │ │ │ │                                   OR sun, mon, tue, wed, thu, fri, sat
# │ │ │ │ │
# * * * * *
```
```
> kubectl create cronjob throw-dice-cron-job --image=kodekloud/throw-dice --schedule='30 21 * * *'
```

### More: 
- clean-up-finished-jobs-automatically https://kubernetes.io/docs/concepts/workloads/controllers/job/#clean-up-finished-jobs-automatically
