# Container Orchestration with Kubernetes

## Introduction to Kubernetes

### What is Kubernetes?

-   **Open-source container orchestration platform**
-   Originally developed by **Google**
-   Now maintained by the **Cloud Native Computing Foundation (CNCF)**
-   Automates:
    -   Deployment
    -   Scaling
    -   Management
    -   Recovery of containerized applications
-   Also known as **"K8s"** or **"Kube"**

---

### Why Kubernetes?

#### From Monoliths to Microservices

-   Modern applications are increasingly **decomposed into microservices**
-   Each microservice runs independently—often in containers

#### Containers Simplify Microservices

-   Lightweight
-   Isolated
-   Fast to start and stop
-   Ideal for CI/CD pipelines

#### Challenge:

-   Managing **hundreds or thousands** of containers manually is **impractical**

#### Kubernetes Solves This By Providing:

-   **High availability** – zero downtime
-   **Horizontal scaling** – automatic workload expansion
-   **Disaster recovery** – failover, backups, rollbacks
-   **Self-healing** – automatic restarts, replacements
-   **Efficient resource usage** – scheduling and bin-packing

---

## Core Kubernetes Components

### 1. POD

-   Smallest deployable unit in Kubernetes
-   A group of one or more containers that:
    -   Share the same network namespace
    -   Can access the same storage volumes
-   Typically runs a **single application**
-   **Ephemeral**: Recreated Pods get new IPs
-   Use **Deployments** or **StatefulSets** to manage them reliably

---

### 2. SERVICE

-   Provides a **stable network identity** for accessing Pods
-   **Load balances** traffic across Pod replicas
-   Decouples Pod lifecycle from client access
-   Types of Services:
    -   **ClusterIP** (default): Internal cluster access only
    -   **NodePort**: Exposes service on each Node’s IP and static port
    -   **LoadBalancer**: Creates external IP via cloud provider
    -   **ExternalName**: Redirects to an external DNS name

---

### 3. INGRESS

-   Handles **external HTTP(S)** traffic into the cluster
-   Routes based on URL paths or hostnames
-   Supports:
    -   SSL/TLS termination
    -   Virtual hosting
-   Requires an **Ingress Controller** (e.g., NGINX, Traefik)

---

### 4. CONFIGMAP & SECRET

| ConfigMap                               | Secret                                 |
| --------------------------------------- | -------------------------------------- |
| Non-sensitive configuration data        | Sensitive data (e.g., API keys)        |
| Key-value format                        | Base64-encoded by default              |
| Mounted as env vars, files, or CLI args | Same usage options                     |
| Not encrypted                           | Can be encrypted at rest (with config) |

> 🔐 **Security Note**: Secrets are only base64-encoded by default. For production, enable encryption at rest and use RBAC to restrict access.

---

### 5. VOLUME

-   Provides **persistent storage** to containers
-   Supports:
    -   Local storage (`emptyDir`, `hostPath`)
    -   Networked or cloud storage via **PersistentVolumes**
-   Helps prevent data loss across container restarts
-   Kubernetes handles mounting and lifecycle — but **not the durability** or backups

---

### 6. DEPLOYMENT & STATEFULSET

| Deployment                           | StatefulSet                            |
| ------------------------------------ | -------------------------------------- |
| Ideal for **stateless** apps         | Ideal for **stateful** apps            |
| Pods are interchangeable             | Each Pod has a **stable identity**     |
| Rolling updates supported            | Ordered and controlled updates         |
| No persistent name/volume by default | Stable hostname and persistent volumes |
| Example: web server, API             | Example: database, Kafka, Redis        |

---

## Kubernetes Architecture

### Node Types

1. **Worker Nodes**:

    - Run application workloads (Pods)
    - Include:
        - Container runtime
        - `kubelet`
        - `kube-proxy`
    - Can scale horizontally as demand grows

2. **Control Plane**:
    - Manages the entire Kubernetes cluster
    - Schedules workloads, handles API requests, monitors cluster state
    - Should be **highly available** in production setups

---

### Worker Node Components

1. **Container Runtime**

    - Software that runs containers (e.g., `containerd`, `CRI-O`, `Docker`)

2. **Kubelet**

    - Primary node agent that manages containers based on Pod specs

3. **Kube-proxy**
    - Manages network routing and forwarding rules for Services

---

### Control Plane Components

1. **API Server**

    - Main entry point to the cluster
    - Handles REST requests from users, tools, and other components
    - Validates and processes object definitions (Pods, Services, etc.)

2. **Scheduler**

    - Assigns Pods to appropriate nodes
    - Considers:
        - Resource requirements (CPU, memory)
        - Affinities and taints
        - Availability zones

3. **Controller Manager**

    - Ensures the **desired state** is maintained
    - Includes:
        - Node controller
        - Replication controller
        - Endpoints controller
        - Namespace controller

4. **etcd**
    - Distributed, consistent **key-value store**
    - Acts as the **cluster’s database**
    - Stores all configuration and state data
    - Must be secured and backed up regularly

---

## Scaling Kubernetes Clusters

### Scaling the Control Plane

1. Provision a new server
2. Install control plane components (API server, etcd, controller)
3. Join it to the existing cluster using `kubeadm` or similar tool

> 🛡️ **Tip**: Use load balancers for API server HA

---

### Scaling Worker Nodes

1. Provision new server (VM or bare metal)
2. Install worker node components:
    - Container runtime
    - Kubelet
    - Kube-proxy
3. Join the cluster using:
   `bash
kubeadm join <API_SERVER_IP>:<PORT> --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
`

**✅ Benefit**: You can scale workloads horizontally by adding nodes without downtime.
