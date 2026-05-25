# Persistent Volumes for Stateful Workload
Task: Persistent Volumes for Stateful Workload

## Scenario: Redis cache needs persistent storage (even if pod restarts).

Create PersistentVolumeClaim redis-pvc of 1Gi (storage class standard if available, else manual).

Create Deployment redis with:

Container redis:alpine

Mount path /data from redis-pvc

Write some data via redis-cli SET foo bar, delete pod, verify data persists.

<details>
<summary>Solution</summary>

---
---
1. Create Persistent Volume Claim(PVC) redis-pvc:

```bash
vim redis-pvc.yaml
```
Then paste the yaml configurations below:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: redis-pvc
  namespace: dev

spec:
  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 1Gi

  storageClassName: local-path
```
Apply the PVC :
```bash
kubectl apply -f redis-pvc
```

Verify the pvc exists:

```bash
kubectl get pvc -n dev
```
Expected output:

```bash
NAME        STATUS   VOLUME   CAPACITY
redis-pvc   Pending
```
### Important notes:

- Ensure to note the storage classes

```bash
    kubectl get sc
```

 - In Kubernetes, a PersistentVolumeClaim (PVC) requests storage from the cluster, similar to how a pod requests CPU and memory.
- Storage class automates PersistentVolume creation.

- Pending means Kubernetes cannot find or provision storage for the PVC yet and is waiting for the PVC to be provisioned so that it can be bound to it.

----

----

2. Create Redis Deployment

Create redis.yaml

```bash
vim redis.yaml
```

The paste the yaml configuration:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  namespace: dev

spec:
  replicas: 1

  selector:
    matchLabels:
      app: redis

  template:
    metadata:
      labels:
        app: redis

    spec:
      containers:
      - name: redis
        image: redis:alpine

        ports:
        - containerPort: 6379

        volumeMounts:
        - name: redis-storage
          mountPath: /data

      volumes:
      - name: redis-storage
        persistentVolumeClaim:
          claimName: redis-pvc
```

Apply the deployment :

```bash
kubectl apply -f redis.yaml
```

Verify the deployment:

```bash
kubectl get pods,pvc -n dev
```

---
---

3. Write Data into Redis
- Get pod name :

```bash
kubectl get pods -n dev
```
- Copy the name of the pod and paste it the next section.


- Exec into the Redis pod:

```bash
kubectl exec -it <redis-pod-name> -n development -- sh
```


- Inside the pod:
```bash
redis-cli
```

  - Set data:
```bash
SET foo bar
```
Expected:

```bash
    OK
```
  - Verify:

```bash
   GET foo
```
Expected:

```bash
"bar"
```

  - Exit redis cli
```bash
exit
```
  - Exit the pod
```bash
exit
```

---
---

4. Delete the Redis pod
Delete the Redis pod:

```bash
kubectl delete pod -l app=redis -n dev
```
Kubernetes automatically recreates it because it is managed by a Deployment.

Check if a new pod is created automatically:

```bash
kubectl get pods -n dev
```
---
---

5. Verify Data Persistence

Exec into the new Redis pod:

```bash
kubectl exec -it <new-pod-name> -n dev -- sh
```

Open the Redis CLI

```bash
redis-cli
```

Retrieve the stored value:
```bash
GET foo
```

Expected:
```bash
"bar"
```


</details>