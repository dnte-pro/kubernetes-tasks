# Multi-Container Pods & Sidecar Logging Pattern

Designed and deployed a production-style multi-container Kubernetes Pod implementing the Sidecar Pattern for centralized application log streaming and container collaboration.

### Project Overview

This focused on advanced Kubernetes Pod architecture by deploying:

- A primary NGINX web container
- A BusyBox logging sidecar container
- Shared ephemeral storage using emptyDir

The objective was to simulate how modern cloud-native applications handle runtime logging and inter-container communication inside Kubernetes Pods.

### Architecture Implemented

![Architecture](https://github.com/user-attachments/assets/f79ee6f6-b7f0-4bff-86aa-1b8bb9d28134)

#### Multi-Container Pod Deployment

Created a Kubernetes Pod containing:

- Main application container (nginx)
- Logging sidecar container (busybox)

Both containers operated within the same Pod lifecycle and shared:

- network namespace
- storage volumes
- localhost communication


Multi-container Pods enable tightly coupled workloads to collaborate efficiently while maintaining separation of responsibilities.

This pattern is commonly used for:
- logging agents
- monitoring collectors
- service mesh proxies
- security scanners
- traffic interceptors


### Sidecar logging pattern 
Implemented a dedicated logging sidecar container responsible for continuously streaming NGINX access logs.


The sidecar pattern provides:

- separation of concerns
- centralized logging pipelines
- reusable observability architecture
- independent auxiliary container management




### Shared Storage with emptyDir
Configured a shared ephemeral volume using Kubernetes emptyDir.

Volume was decalred in the pod:
```yaml
emptyDir: {}
```

Shared volumes allow containers in the same Pod to exchange:

- logs
- temporary files
- runtime artifacts
- cache data

emptyDir is widely used for:

- scratch storage
- transient processing
- inter-container communication
- temporary caching

