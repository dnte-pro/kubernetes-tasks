# Jobs & CronJobs (Batch processing)
## Scenario: Daily database backup job at 2 AM.

### Guidelines

Create Job db-backup-manual that runs echo "Backing up..." (test once).

Convert to CronJob db-backup-daily:

Schedule: 0 2 * * *

Container: postgres:13 with command pg_dump ... (simulate with echo)

Restart policy: OnFailure

View job history and logs.



### Steps

#### 1. Create Manual Backup Job

- Create a job :
```bash
vim db-backup-job.yaml
```

Then paste the configurations:
```bash
apiVersion: batch/v1
kind: Job

metadata:
  name: db-backup-manual
  namespace: dev

spec:
  template:
    spec:
      containers:
      - name: backup
        image: busybox

        command:
        - sh
        - -c
        - echo "Backing up database..."

      restartPolicy: Never
```

**NB**: Ensure to ue the correct namespace, if you're using the default namespace, omit the namespace

Then apply the yaml configurations:

```bash
kubectl apply -f db-backup-job.yaml
```

![](https://github.com/user-attachments/assets/4039126d-0371-4dcd-949e-30d649dba997)

#### 2. Verify job Execution

Verify that the job has been created:
```bash
kubectl get jobs -n dev
```
Then check the pods created by the job:

```bash
kubectl get pods -n dev
```

![](https://github.com/user-attachments/assets/50ade188-50b2-490c-b832-18fe92baa17c)

#### 3. View job logs

- To view the logs of the job, get the pod name:
```bash
kubectl get pods -n dev
```

- View logs:
```bash
kubectl logs <pod-name> -n dev
```
Replace the ```<pod-name>``` with the actual pod name from the previous step.

![](https://github.com/user-attachments/assets/482c5ab9-2da2-4017-b451-ec94886c32f7)

#### 4. Create a CronJob for daily backups

Create a cronjob ```db-backup-cronjob.yaml```

```bash
vim db-backup-cronjob.yaml
```

- Then paste the configurations:

```yaml
apiVersion: batch/v1
kind: CronJob

metadata:
  name: db-backup-daily
  namespace: dev

spec:
  schedule: "0 2 * * *"

  successfulJobsHistoryLimit: 3
  failedJobsHistoryLimit: 1

  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: postgres-backup
            image: postgres:13

            command:
            - sh
            - -c
            - echo "Running scheduled PostgreSQL backup..."

          restartPolicy: OnFailure

```

- Apply:
```bash
kubectl apply -f db-backup-daily
```

![](https://github.com/user-attachments/assets/923b0b24-2800-43a7-b007-d7da9b4dcd6f)

#### 5. Verify the CronJob:

Check Cronjobs

```bash
kubectl get cronjobs -n dev
```


![](https://github.com/user-attachments/assets/1177db95-6521-4970-8365-0a6d7c825c49)



- Describe it :

```bash
kubectl describe cronjob db-backup-daily -n dev
```

![](https://github.com/user-attachments/assets/701bfae6-13cf-483c-9066-86e9d0e0980b)


**Checkpoint**

- The Cronjob takes database backup daily at 2.00am, we can, however, trigger a cronjob manually without scheduling it for later.


#### 6. Trigering a CronJob manually
Instead of waiting fo the 2.00am backup, we can trigger the backup using the cronjob to check the functionality.

```bash
kubectl create job --from=cronjob/db-backup-daily \
  manual-backup-test \
  -n dev
```

![](https://github.com/user-attachments/assets/008aeb0d-58ed-46a9-abbd-e8c4e00627d1)

#### 7. View the CronJob-created jobs
- Confirm the jobs created from the cronjob:

```bash
kubectl get jobs -n dev
```

- You should see:

```text
manual-backup-test
```
![](https://github.com/user-attachments/assets/fda5b102-58ac-42dd-96e4-7d4400cbed59)

#### 8. View logs from the CronJob-created Job

- Get the pod name <pod-name>
```bash
kubectl get pods -n dev
```

- View the logs of the pod
```bash
kubectl logs <pod-name> -n dev
```
- Expected

```text
Running scheduled PostgreSQL backup...
```
![](https://github.com/user-attachments/assets/d8ec5175-fc32-4c0a-a317-97222e139931)

#### 9. View Job history

Check completed jobs:
```bash
kubectl get jobs -n dev
```

Check CronJob history:
```bash
kubectl describe cronjob db-backup-daily -n dev
```

![](https://github.com/user-attachments/assets/4835661d-55c0-4259-8a2d-80f3654384fa)

Check for:
- last schedule time
- active jobs
- successful job history


