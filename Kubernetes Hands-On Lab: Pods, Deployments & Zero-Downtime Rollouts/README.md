# Kubernetes Hands-On Lab: Pods, Deployments & Zero-Downtime Rollouts

Built and managed a complete Kubernetes microservice workflow focused on real-world troubleshooting, resiliency, and deployment operations.

## What I Implemented

### Pod Management & Troubleshooting

* Created isolated workloads using namespaces
* Deployed interactive debugging pods with BusyBox
* Executed in-container networking diagnostics using `wget`
* Simulated container image failures (`ImagePullBackOff`)
* Diagnosed and recovered broken workloads
* Resolved `CrashLoopBackOff` issues through ReplicaSet and Deployment cleanup

### Deployments & High Availability

* Built highly available Kubernetes Deployments with:

  * Multiple replicas
  * Rolling update strategy
  * Self-healing containers
* Configured:

  * Readiness probes
  * Liveness probes
  * Container ports
* Exposed workloads internally using ClusterIP Services

### Zero-Downtime Delivery

* Performed rolling updates between application versions
* Monitored rollout health in real time
* Executed safe rollbacks using Kubernetes rollout history
* Scaled applications dynamically from 2 → 5 replicas

## Kubernetes Concepts Practiced

* Pods
* Namespaces
* Deployments
* ReplicaSets
* Services
* Rolling Updates
* Rollbacks
* Scaling
* Health Checks
* Container Debugging
* Cluster Networking
* Self-Healing Infrastructure

## Key Takeaway

This lab strengthened my practical Kubernetes operations skills by simulating production-style deployment management, failure recovery, and zero-downtime application delivery. It reflects hands-on experience with the core workflows used in modern cloud-native environments.
