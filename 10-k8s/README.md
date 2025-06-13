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

# Deep Dive into Kubernetes

## Minikube – Local Cluster Setup

-   **Minikube** simulates a full Kubernetes cluster on a single machine.
-   Used for **local testing and development**.
-   Control plane and worker components run in one VM or container.
-   Requires a container runtime (e.g., Docker) or VM manager (e.g., VirtualBox).
-   Starts with:
    ```bash
    minikube start
    ```

## kubectl – Kubernetes CLI

-   `kubectl` is the official CLI for interacting with Kubernetes.
-   Uses a **kubeconfig** file (usually at `~/.kube/config`) to access clusters.

**Basic Commands**

```bash
kubectl get pods       # Check pod status
kubectl create -f file.yaml  # Create resources
kubectl logs <pod>     # Debugging
kubectl describe       # Detailed info
```

## Kubernetes YAML Configuration Files

-   Declarative files used to define the desired state of resources.
-   Written in YAML.
-   Should be version-controlled.
-   Commonly used for:
    -   Deployments
    -   Services
    -   ConfigMaps
    -   Secrets

### Sections of a YAML File

1. `apiVersion`
    - Specifies the version of the Kubernetes API to use for the resource.
    - Example: `apiVersion: apps/v1` for Deployments.
2. `kind`
    - Defines the type of resource (e.g., Pod, Service, Deployment).
    - Example: `kind: Deployment`
3. `metadata`
    - Contains metadata about the resource, such as name, namespace, and labels.
    - Example:
        ```yaml
        metadata:
            name: my-deployment
            labels:
                app: my-app
        ```
4. `spec`
    - Defines the desired state of the resource.
    - Contains configuration details like replicas, container images, ports, etc.
    - Example:
        ```yaml
        spec:
            replicas: 3
            selector:
                matchLabels:
                    app: my-app
            template:
                metadata:
                    labels:
                        app: my-app
                spec:
                    containers:
                        - name: my-container
                          image: my-image:latest
                          ports:
                              - containerPort: 80
        ```

**Example: Deployment**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx-deployment
spec:
    replicas: 2
    selector:
        matchLabels:
            app: nginx
    template:
        metadata:
            labels:
                app: nginx
        spec:
            containers:
                - name: nginx
                  image: nginx:1.25
                  ports:
                      - containerPort: 80
```

**Labels & Selectors**

-   Labels: Key-value pairs attached to objects.
-   Selectors: Used to match labels and group resources.

```yaml
metadata:
    labels:
        app: frontend
```

```yaml
selector:
    matchLabels:
        app: frontend
```

### Services and Ports

-   A Service exposes Pods and provides a stable endpoint.

**Example: ClusterIP Service**

```yaml
apiVersion: v1
kind: Service
metadata:
    name: frontend-service
spec:
    selector:
        app: frontend
    ports:
        - port: 80
          targetPort: 8080
    type: ClusterIP
```

### Ingress – HTTP Routing

Ingress allows external access to services via HTTP(S).
**Example: Basic Ingress**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
    name: web-ingress
spec:
    rules:
        - host: example.com
          http:
              paths:
                  - path: /
                    pathType: Prefix
                    backend:
                        service:
                            name: frontend-service
                            port:
                                number: 80
```

> 🔗 **Note**: Ingress requires an Ingress Controller (e.g., NGINX, Traefik) to function.

### ConfigMap & Secret

-   **ConfigMap**: Stores non-sensitive configuration data.
-   **Secret**: Stores sensitive data (e.g., passwords, API keys).

**Example: ConfigMap**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: app-config
data:
    DB_HOST: mongodb-service
    DB_PORT: "27017"
```

**Example: Secret Example**

```yaml
apiVersion: v1
kind: Secret
metadata:
    name: db-credentials
type: Opaque
data:
    username: YWRtaW4= # base64 encoded
    password: c2VjcmV0 # base64 encoded
```

### Namespaces

-   Logical separation within a single cluster.
-   Useful for team isolation, environment isolation, or RBAC.

**Create via CLI**

```bash
kubectl create namespace dev-team
```

**Create via YAML**

```yaml
apiVersion: v1
kind: Namespace
metadata:
    name: dev-team
```

### Persistent Storage

-   Kubernetes supports Persistent Volumes for storing data beyond Pod lifecycles.

**Example: PersistentVolumeClaim (PVC)**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
    name: app-pvc
spec:
    accessModes:
        - ReadWriteOnce
    resources:
        requests:
            storage: 1Gi
```

### StatefulSets

-   Used for apps where each instance (Pod) has its own state and storage (e.g., databases).
    **Example: StatefulSet with PVC**

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
    name: mongo-db
spec:
    serviceName: mongo
    replicas: 2
    selector:
        matchLabels:
            app: mongo
    template:
        metadata:
            labels:
                app: mongo
        spec:
            containers:
                - name: mongo
                  image: mongo
                  ports:
                      - containerPort: 27017
                  volumeMounts:
                      - name: mongo-storage
                        mountPath: /data/db
    volumeClaimTemplates:
        - metadata:
              name: mongo-storage
          spec:
              accessModes: ["ReadWriteOnce"]
              resources:
                  requests:
                      storage: 1Gi
```
