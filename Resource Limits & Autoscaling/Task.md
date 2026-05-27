# Resource Limits & Autoscaling

## Scenario: API service under load – need resource constraints and auto-scaling.

Update api Deployment with resource requests/limits:

CPU: 250m request, 500m limit

Memory: 256Mi request, 512Mi limit

Create HorizontalPodAutoscaler for api:

Min 2, Max 6 replicas

Scale on CPU at 60%

Generate load (using kubectl run load-generator --image=busybox -- /bin/sh -c "while true; do wget -q -O- http://api-svc; done") and watch scaling.




## Solution :

#### 1. Create a Deployment the edit or create a new deployment with the correct configurations

- Create a deployment "api".

```bash 
vim api.yaml
```

Then paste the configurations:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
  namespace: dev
  labels:
    app: api

spec:
  replicas: 2

  selector:
    matchLabels:
      app: api

  template:
    metadata:
      labels:
        app: api

    spec:
      containers:
      - name: api
        image: nginx:latest

        ports:
        - containerPort: 80

        resources:
          requests:
            cpu: "250m"
            memory: "256Mi"

          limits:
            cpu: "500m"
            memory: "512Mi"

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
          periodSeconds: 11


```
- Apply the api.yaml
```bash
 kubectl apply -f api.yaml
```


**Note** - Ensure to create a namespace 'dev' or alter to use appropriate namespace

- Verify
```bash 
kubectl get pods -n dev
```


#### 2. Verify metrics server.


In a Kubernetes environment, a Horizontal Pod Autoscaler (HPA) requires metrics to act as the "eyes and ears" of the cluster. 
Without real-time performance data, the HPA controller cannot detect traffic spikes, 
evaluate workload demands, or make automated decisions to scale your application's pods up or down. 
Therefore the metrics server is required for the HPA to work correctly.

- Install the metrics server
```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

- Patch it automatically