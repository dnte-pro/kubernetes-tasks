# Kubernetes' Services, Ingress & External Traffic Routing

Implemented a production-style Kubernetes networking architecture to expose containerized applications externally using Services and Ingress Controllers.

## Project Overview

This lab focused on Kubernetes networking, traffic routing, and external application exposure by configuring:

* NodePort Services
* ClusterIP Services
* NGINX Ingress Controller
* Host-based routing
* Layer 7 traffic management

The goal was to simulate how modern cloud-native applications are exposed securely and efficiently in production Kubernetes environments.

---

# What I Built

## NodePort Service for External Access

Created a Kubernetes `NodePort` service to expose the web application outside the cluster for testing and debugging purposes.

### Key Features

* Exposed application traffic externally
* Enabled direct access using:

  ```text
  http://<NODE-IP>:<NODEPORT>
  ```
* Verified service accessibility using `curl`

### Real-World Benefit

NodePort services are commonly used for:

* Development environments
* Temporary testing endpoints
* Internal infrastructure access
* Bare-metal Kubernetes clusters

They provide a quick method to expose workloads without requiring a cloud load balancer.

---

# Internal Service Discovery with ClusterIP

Configured internal Kubernetes networking using `ClusterIP` services.

### Key Features

* Enabled pod-to-pod communication
* Abstracted backend pods behind a stable virtual IP
* Provided service discovery inside the cluster

### Real-World Benefit

ClusterIP services are foundational for:

* Microservice communication
* Internal APIs
* Service mesh architectures
* Scalable backend routing

They decouple application networking from ephemeral pod lifecycles.

---

# NGINX Ingress Controller Deployment

Installed and configured the NGINX Ingress Controller using Helm.

### Key Features

* Centralized HTTP/HTTPS routing
* Layer 7 traffic management
* Reverse proxy functionality
* Dynamic routing based on hostname and path

### Real-World Benefit

Ingress controllers are production-critical for:

* Multi-service application routing
* API gateways
* SSL/TLS termination
* Centralized traffic management
* Kubernetes edge networking

This mirrors how enterprise Kubernetes platforms expose applications to users.

---

# Host-Based Routing with Ingress

Created an Ingress resource to expose the application through:

```text
http://ecostore.local
```

### Key Features

* Hostname-based routing
* Path-based traffic forwarding
* Reverse proxy configuration
* Service backend mapping

### Example Routing

```text
ecostore.local  --->  web-svc ---> web pods
```

### Real-World Benefit

Ingress resources are widely used for:

* Production web applications
* Domain-based routing
* Multi-tenant Kubernetes platforms
* API routing
* SaaS application architectures

They allow multiple services to share a single ingress endpoint efficiently.

---

# Traffic Validation & Troubleshooting

Validated application routing using:

```bash
curl -H "Host: ecostore.local"
```

Performed troubleshooting for:

* `503 Service Temporarily Unavailable`
* Missing service endpoints
* Label selector mismatches
* Ingress backend connectivity issues

### Real-World Benefit

This reflects real DevOps and SRE operational workflows involving:

* Kubernetes networking diagnostics
* Service discovery troubleshooting
* Ingress debugging
* Production incident resolution

---

# Kubernetes Concepts Practiced

* Services

  * ClusterIP
  * NodePort
* Ingress
* Ingress Controllers
* Host-Based Routing
* Service Discovery
* Kubernetes Networking
* Traffic Routing
* Endpoint Validation

---

# Tools & Technologies

* Kubernetes
* kubectl
* Helm
* NGINX Ingress Controller
* NGINX
* curl
* YAML Manifests

---

# Key Takeaway

This lab strengthened my practical Kubernetes networking and traffic management skills by implementing production-style service exposure, ingress routing, and application accessibility workflows. It demonstrates hands-on experience with real-world Kubernetes networking patterns used in scalable cloud-native infrastructures and enterprise container platforms.
