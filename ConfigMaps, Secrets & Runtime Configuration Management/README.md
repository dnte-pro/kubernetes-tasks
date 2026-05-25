# Kubernetes ConfigMaps, Secrets & Runtime Configuration Management

Designed and deployed a production-style Kubernetes configuration workflow that separates application configuration from sensitive credentials using native Kubernetes resources.

## What I Implemented

### Dynamic Application Configuration

* Created and managed Kubernetes ConfigMaps for runtime application settings
* Injected non-sensitive configuration into containers as environment variables
* Configured:

  * `LOG_LEVEL=debug`
  * `API_TIMEOUT=30s`

### Secure Secret Management

* Built Kubernetes Opaque Secrets for sensitive credentials
* Secured database passwords using Kubernetes secret encoding mechanisms
* Injected secrets directly into running containers without hardcoding values

### Real-World Deployment Configuration

* Deployed a containerized API workload using Kubernetes Deployments
* Configured BusyBox containers for runtime validation and debugging
* Mounted:

  * ConfigMaps as environment variables
  * Secrets as protected environment variables

### Container Runtime Verification

* Performed in-container validation using `kubectl exec`
* Verified live configuration injection and secret availability inside running pods
* Demonstrated secure configuration decoupling in Kubernetes-native workflows

## Kubernetes Concepts Practiced

* ConfigMaps
* Secrets
* Environment Variable Injection
* Deployments
* Pod Runtime Inspection
* Namespace Isolation
* Secure Configuration Management
* Infrastructure as Code
* Container Debugging

## Key Takeaway

This lab strengthened my understanding of secure and scalable Kubernetes application configuration by implementing production-style ConfigMap and Secret management patterns. It demonstrates hands-on experience with separating application logic, runtime configuration, and sensitive credentials — a critical practice in cloud-native and enterprise Kubernetes environments.
