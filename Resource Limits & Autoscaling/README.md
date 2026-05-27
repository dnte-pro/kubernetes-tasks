# Kubernetes Resource Limits & Autoscaling Lab
## Overview

This lab demonstrates the implementation of resource management, horizontal pod autoscaling, and load testing in a Kubernetes environment running on Docker Desktop Kubernetes.

The objective was to simulate a production-like API workload and configure Kubernetes to automatically scale application replicas based on CPU utilization.

## Project Goals

- Configure container resource requests and limits
- Implement Horizontal Pod Autoscaling (HPA)
- Deploy and validate Metrics Server
- Generate sustained traffic load
- Observe automatic scaling behavior
- Understand Kubernetes resource optimization concepts

| Component          | Version / Platform        |
| ------------------ | ------------------------- |
| Kubernetes         | Docker Desktop Kubernetes |
| OS                 | Linux                     |
| Container Runtime  | Docker                    |
| kubectl            | Latest Stable             |
| Metrics Collection | metrics-server            |
| Workload           | API Deployment            |

### Resource Configuration

The API deployment was configured with production-style CPU and memory constraints.

#### Requests

These guarantee minimum resources required by the container.

```yaml
requests:
  cpu: "250m"
  memory: "256Mi"
Limits
```

These prevent the container from consuming excessive resources.

```yaml
limits:
  cpu: "500m"
  memory: "512Mi"
Horizontal Pod Autoscaler (HPA)
```

#### The HPA was configured to:

| Parameter              | Value |
| ---------------------- | ----- |
| Min Replicas           | 2     |
| Max Replicas           | 6     |
| Target CPU Utilization | 60%   |


#### HPA Creation
```yaml
kubectl autoscale deployment api \
  --cpu-percent=60 \
  --min=2 \
  --max=6
```


### Metrics Server Setup

Docker Desktop Kubernetes may not expose metrics by default.

The following steps were performed:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Patched for Docker Desktop compatibility:

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


#### Load Testing

A BusyBox-based load generator was deployed to continuously send requests to the API service.


Load Generator
```bash
kubectl run load-generator \
  --image=busybox \
  --restart=Never \
  -- /bin/sh -c \
  "while true; do for i in 1 2 3 4 5 6 7 8 9 10; do wget -q -O- http://api-svc > /dev/null & done; wait; done"
  ```


This generated parallel HTTP requests and triggered CPU-based scaling.

### Observability & Validation
##### Watch HPA Behavior
```bash
kubectl get hpa -w
```
#### Watch Pod Scaling
```bash
kubectl get pods -w
```

Observed scaling behavior:

```text
2 replicas → 3 replicas → 4 replicas → ... → 6 replicas
```


### Cleanup
```bash
kubectl delete pod load-generator
kubectl delete hpa api
```

### Final Outcome

Successfully implemented:

- Resource-constrained Kubernetes workloads
- CPU-based autoscaling
- Metrics collection and observability
- Load-driven scaling validation
- Production-style Kubernetes operational practices