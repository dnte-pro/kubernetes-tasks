# Kubernetes Persistent Storage & Stateful Workloads

Implemented a production-style persistent storage workflow in Kubernetes by deploying a stateful Redis workload backed by Persistent Volumes and Persistent Volume Claims.

## What I Built

### Persistent Storage Architecture

* Configured Kubernetes PersistentVolumeClaims (PVCs) for durable application storage
* Allocated `1Gi` persistent storage for Redis data persistence
* Worked with dynamic storage provisioning using Kubernetes StorageClasses
* Validated PVC lifecycle states (`Pending` → `Bound`)

### Stateful Redis Deployment

* Deployed a Redis containerized workload using Kubernetes Deployments
* Mounted persistent storage into the container at:

  ```text
  /data
  ```
* Configured Redis to persist application data independently of pod lifecycle

### Data Persistence Validation

* Connected to Redis using `redis-cli`
* Inserted test data:

  ```redis
  SET foo bar
  ```
* Simulated pod failure by deleting the Redis pod
* Verified Kubernetes self-healing recreated the pod automatically
* Confirmed persistent storage functionality by retrieving data after pod recreation:

  ```redis
  GET foo
  → "bar"
  ```

### Storage Troubleshooting & Cluster Diagnostics

* Diagnosed PVC `Pending` states
* Investigated StorageClass provisioning behavior
* Worked with:

  * `local-path`
  * dynamic provisioning
  * default StorageClasses
* Inspected PVC events and storage controller behavior using:

  ```bash
  kubectl describe pvc
  kubectl get storageclass
  ```

## Kubernetes Concepts Practiced

* PersistentVolumes (PV)
* PersistentVolumeClaims (PVC)
* StorageClasses
* Dynamic Provisioning
* Stateful Workloads
* Volume Mounts
* Redis Persistence
* Kubernetes Self-Healing
* Pod Recreation
* Cluster Storage Troubleshooting

## Key Takeaway

This lab strengthened my practical understanding of Kubernetes stateful application management by implementing persistent storage for Redis and validating real-world data durability across pod restarts. It demonstrates hands-on experience with Kubernetes storage orchestration, dynamic volume provisioning, and production-grade stateful workload operations.
