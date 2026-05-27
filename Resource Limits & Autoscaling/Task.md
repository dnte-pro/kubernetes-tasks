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
```bash
kubectl patch deployment metrics-server -n kube-system \
  --type='json' \
  -p='[
    {
      "op":"add",
      "path":"/spec/template/spec/containers/0/args/-",
      "value":"--kubelet-insecure-tls"
    }
  ]'
```

or do it manually by editing the metrics-server deployment :

```bash 
kubectl edit deployment metrics-server -n kube-system
```

and edit the args section ```args:``` with 
```bash
 --kubelet-insecure-tls
```

The args section should look like:
```bash
args:
  - --cert-dir=/tmp
  - --secure-port=10250
  - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
  - --kubelet-use-node-status-port
  - --metric-resolution=15s
  - --kubelet-insecure-tls
```

Wait till the metrics-server runs 
```bash 
kubectl get deployment -n kube-system
```



#### 3. Expose the deployment as a service


Expose the deployment within the cluster for communication:

```bash
kubectl expose deployment api \
  --name=api-svc \
  --port=80 \
  --target-port=80 \
  -n development
```

Verify that the service was created:

```bash 
kubectl get svc -n dev
```



#### 4. Create the Horizontal Pod Autoscaler

Run the hpa:

```bash
kubectl autoscale deployment api \
  --cpu-percent=60 \
  --min=2 \
  --max=6 \
  -n dev
```

Verify:

```bash
kubectl get hpa -n dev
```

#### 6. Watch Autoscaling in real time

Open another terminal and watch what happens to the the hpa autoscaling:

```bash
kubectl get hpa,pods -n dev -w
```
