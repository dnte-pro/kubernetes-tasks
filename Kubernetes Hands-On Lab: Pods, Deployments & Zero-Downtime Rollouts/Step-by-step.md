# Task 1: Deployments & Rolling Updates
Scenario: The web microservice needs high availability and zero-downtime updates.


Create a Deployment web with:
3 replicas
Container web:1.25 (nginx), port 80
Readiness probe (HTTP GET /)
Liveness probe (HTTP GET /)
Expose it internally via ClusterIP service web-svc.
Rolling update: Change image to web:1.1. Watch rollout status.
Rollback to web:1.0 using kubectl rollout undo.
Scale replicas to 5, then down to 2.

## Solution
#### 1. Create the Deployment with 3 replicas with a namespace dev
Create a yaml file ```web.yaml```

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: dev

spec:
  replicas: 3

  selector:
    matchLabels:
      app: web

  template:
    metadata:
      labels:
        app: web

    spec:
      containers:
      - name: web
        image: nginx:1.25
        ports:
        - containerPort: 80

        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5

        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 10
```
Apply the file:
```kubectl apply -f web.yaml```


Verify deployment:
```kubectl get deployments,pods -n dev```

Wait for rollout :
```kubectl rollout status deployment/web -n dev```


---
2. Expose internally with ClusterIP service

```bash
kubectl expose deployment web \
  --name=web-svc \
  --port=80 \
  --target-port=80 \
  --type=ClusterIP \
  -n development
```
Verify the service:
```kubectl get svc -n dev```
![Service](https://github.com/user-attachments/assets/ef5479fa-4c91-49d6-a554-3c529fb8f53b)

---
3. Rolling Update
Update the image:

```bash
kubectl set image deployment/web \
web=nginx:1.26 \
-n dev
```

Watch rollout:

```kubectl rollout status deployment/web -n dev ```

Check rollout history

```kubectl rollout history deployment/web -n dev```


---
4. Rollback to a previous version.
Rollback:

```kubectl rollout undo deployment/web -n dev```


Verify rollback:
```kubectl describe deployment web -n dev```


---
5. Scale to 5 replicas
Scaling the replicas to 5 from the initial 3

```bash
kubectl scale deployment web \
    --replicas=5 \
    -n dev
```
Verification:
```kubectl get pods -n dev```


---
6. Scale replicas down to 2

```bash
kubectl scale deployment web \
    --replicas=2 \
    -n dev
```

Verification:
```kubectl get pods -n dev```
