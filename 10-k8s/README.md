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

-   Minikube implements a local K8s cluster
-   Usefull for local K8s application development, because running a test cluster would be complex and expensive
-   Control Plane and Worker processes run on **ONE machine** (VM or container)
-   So you need a container runtime or virtual machine manager on you laptop

## kubectl – Kubernetes CLI

-   CLI Tool is interact with your K8s cluster
-   In order for kubectl to access a K8s cluster, it needs a **kubeconfig** file, which is created automatically when deploying your minikube cluster
-   By default, config file is located at `~/.kube/config`

**Basic Commands**

```bash
# Get status of different K8s components
kubectl get {k8s-component}
kubectl get nodes
kubectl get pods
kubectl get services
kubectl get deployments

# CRUD Operations
kubectl create {k8s-component} {name} {options}
kubectl create deployment my-nginx-depl --image=nginx

kubectl edit {k8s-component} {name}
kubectl delete {k8s-component} {name}

# Debugging
kubectl logs {pod-name}
kubectl describe {pod-name}

kubectl exec -it {pod-name} -- bash
kubectl apply -f config-file.yaml
```

## K8s YAML Configuration File

-   Also called **Kubernetes manifest**
-   Declarative: A manifest **specifies the desired state** of a K8s component
-   Config files are in YAML format, which is user-firendly, but **strict indentation**!
-   Config files should be stored in version control (e.g., Git) for tracking changes and collaboration

### Each configuration file has **3 parts**:

1. `metadata`

    - Contains information about the resource, such as name, namespace, and labels.

2. `specification`

    - Defines the desired state of the resource, including configuration details like replicas, container images, ports, etc.

3. `status` (optional)
    - Automatically generated and added by Kubernetes.
    - K8s gets this information from etcd, which holds the current status of any K8s component.

### Deployment Configuration File

-   Deployment Configuration is a bit special.
-   Since it's an abstraction over Pods, it has a `template` field in the `spec` section.
-   Own `metadata` and `spec` sections, which define the Pod template that will be used to create Pods.
-   Blueprint for Pods, which allows you to define how the Pods should look like.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: my-deployment
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

### Labels & Selectors

** Labels**

-   Labels are key/value pairs attached to resources, such as Pods.
-   Used to specify indentifying attributes that are meaningful and relevant to users.

**Selectors**

-   Labels do not provide uniqueness, so selectors are used to match labels and group resources.
-   Via selectors the user can identify a set of resources that match certain criteria.

**Example:**

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

### Ports in Service and Pod

-   In service component you need to specify:
    -   `port`: The port that the service will expose.
    -   `targetPort`: The port on the Pod that the service will forward traffic to.
-   In Deployment component:
    -   `containerPort`: The port that the container will listen on.

### Browser Request Flow through the K8s components

**Example Setup:**

-   Mongo Express as UI
-   MongoDB as database
-   User updates entries in database via browser
-   ConfigMap and Secret holds the MongoDB's endpoint (Service name of MongoDB) and credentials (user, pwd), which gets injected to MongoExpress Pod, so MongoExpress can connect to the DB

**K8s Components needed In this setup:**

-   2 Deplooyment / Pod
-   2 Services
-   1 ConfigMap
-   1 Secret

### Namespaces

-   Namespaces provide a mechanism for isolating groups of resources within a single cluster.
-   Names of resources need to be unique within a namespace, but not across namespaces.
-   Like a virtual cluster inside a cluster.

**Default Namespace:**

-   If no namespace is specified, resources are created in the `default` namespace. (DON'T modify kube-system namespace!)

**Creating a Namespace:**

-   Via `kubectl` command:

```bash
kubectl create namespace my-namespace
```

-   Or by defining a YAML file:

```yaml
apiVersion: v1
kind: Namespace
metadata:
    name: my-namespace
    nampespace: my-namespace
```

**Use Cases** for when to use namespaces:

1. Group resources logically (e.g., dev, staging, prod). Instead of having all in the default namespace.
2. Insolate team resources (e.g., team A, team B). To avoid conflicts between teams.
3. Share resources between different environments (e.g., dev, staging, prod) while keeping them isolated.
4. Limit permissions and compute resources for teams.

# Deep Dive into Kubernetes Services

-   Abstract way to expose an application running on a set of Pods

**Why Services?**

-   Stable IP address
-   Loadbalancing
-   Loose coupling between Pods and clients
-   Within & Outside Cluster

## Service Types

| Service Type | Description                                                                                    |
| ------------ | ---------------------------------------------------------------------------------------------- |
| ClusterIP    | Default type, exposes service on a cluster-internal IP. Only accessible within the cluster.    |
| NodePort     | Exposes service on each Node’s IP at a static port. Accessible from outside the cluster.       |
| LoadBalancer | Creates an external load balancer in supported cloud providers. Routes traffic to the service. |

### ClusterIP Service & its subtypes

-   ClusterIP is an internal service, not accessible from outside the cluster
-   All Pods in the cluster can talk to this internal service

There are 2 subtypes of ClusterIP service:

1. **Headless Service**:

    - When client needs to communicate with 1 specific Pod directly, instead of randomly selected
    - Use case: When Pod replicas are not identical. For example statefull apps, like when only master is allowed to write to database, but all replicas can read from it.

2. **Multi-port Service**:
    - When a single service needs to expose multiple ports
    - Use case: When a Pod runs multiple containers, each listening on different ports

### NodePort Service

-   Unlike internal Service, is accessible from outside the cluster
-   Exposes the service on each Node’s IP at a static port
-   Traffic sent to any Node’s IP on that port is forwarded to the service
-   Useful for development and testing, but not recommended for production

> ⚠️ **NOT SECURE**: External traffic has access to fixed port on each Worker Node

### LoadBalancer Service

-   Exposes the Service externally using a cloud provider's load balancer
-   NodePort and ClusterIP Services, to which the external load balancer routes, are created automatically
-   Automatically provisions an external IP address
-   Ideal for production environments where you need a stable external endpoint

> ⚠️ **CLOUD PROVIDER DEPENDENT**: Only works with cloud providers that support external load balancers (e.g., AWS, GCP, Azure)

**Important Note**: LoadBalancer Service is not available in Minikube, as it runs locally. Use NodePort for local testing.

## Ingress

-   External Services are the a way to access application in K8s from outside the cluster
-   In production a better alternative is **Ingress**
-   Not a Service type, but acts as the entry point for your cluster
-   **More intelligent and flexible**: Let's you consolidate your routing into a single resource as it can expose multiple services under the same IP address

**Before Ingress Controller you still need 1 load labalancer**

-   **Option 1 - Cloud service provider**: have out-of-the-box K8s solutions

-   **Option 2 - Bare Metal**: You need to configure some kind of entry point. Either inside the cluster or outside as separate server (e.g. NGINX, Traefik)

# Deep Dive into Kubernetes Volumes

-   K8s offers no persistent storage by default
-   At its core, a _volume is a directory_ (which some data in it), _which is accessible to containers in a Pod_
-   K8s supports many types of volumes
-   Ephemeral volume type have a lifetime of a Pod, persistent volumes exist beyond the lifetime of a Pod
-   In this lecture we talk about _persistent volumes_, with these storage requirements:

    -   Storage the doesn't depend on pod lifecycle
    -   Storage must be available on all Nodes
    -   Storage needs to survive even if _cluster crashes_

-   The way to persist data in K8s using Volumes is with these 3 resources:

1. **PersistentVolume (PV)**:

-   **Storage** in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.

2 . **PersistentVolumeClaim (PVC)**:

-   A **request for storage by a user**.
-   Similar to Pods. While Pods consume node resources, PVCs consume PV resources.

3. **StorageClass**:

-   SC provisions PV dynamically when PVC claims it.

# Deep Dive into StatefulSets

-   Used to manage stateful applications, like databases
-   Manages the deployment and scaling of a set of Pods and provides guarantees about the ordering and uniqueness of these Pods

---

## Stateless vs Stateful Applications

| Aspect            | Stateless Applications          | Stateful Applications                      |
| ----------------- | ------------------------------- | ------------------------------------------ |
| Data Dependency   | Doesn't depend on previous data | Updates and queries based on previous data |
| Deployment Method | Deployed using **Deployment**   | Deployed using **StatefulSet**             |
| State Awareness   | No state awareness              | Depends on up-to-date data/state           |

> Both manage Pods based on container specification

---

## Deployment vs StatefulSet

-   **StatefulSet**

    -   Maintains a sticky identity for each Pod
    -   Pods are created from the same spec but **not interchangeable**
    -   Each has a persistent identifier maintained across rescheduling
    -   Pods:
        -   More difficult to manage
        -   Can't be created/deleted at the same time
        -   Can't be randomly addressed

-   **Deployment**
    -   Pods are **identical and interchangeable**
    -   Created in random order with random hashes
    -   One service can load balance to `$(statefulset name)-$(ordinal)` Pod

---

## Scaling Database Applications

-   Each replica has its own storage
-   These storages are constantly synchronized
-   Only one replica can make changes
-   So replicas are **not identical**

# Managed K8s Servics

## Kubernetes on Cloud platform

-   2 options to create a Kubernetes cluster on a cloud platform:
    -   Managed Kubernetes Service
    -   Self-managed Kubernetes cluster

### Create own cluster from scratch

-   You need to manage everything yourself, including:
    -   Control Plane
    -   Worker Nodes
    -   Networking
    -   Storage
    -   Security
-   Not pracical, when you want to setup things fast and easy

### Use Managed K8s Service

-   You only care about Worker Nodes, which you can scale up and down
-   Everything pre-installed and configured for you
-   Control Plane Nodes created and managed by the cloud provider
-   You only pay for the Worker Nodes
-   Use cloud native load balancer for ingress controller
-   Use cloud storage
-   Less effort and time

## Managed Kubernetes Services

-   **AWS**: Amazon EKS (Elastic Kubernetes Service)
-   **Azure**: Azure Kubernetes Service (AKS)
-   **Google Cloud**: Google Kubernetes Engine (GKE)
-   **IBM Cloud**: IBM Cloud Kubernetes Service
-   **DigitalOcean**: DigitalOcean Kubernetes
-   **Alibaba Cloud**: Alibaba Cloud Container Service for Kubernetes (ACK)

# Helm Package Manager

-   Helm is the package manager for Kubernetes. Think of it like `apt` or `yum` for Kubernetes.
-   Packages YAML files and distributes them in public and private repositories.
-   Helm packages are called **Charts**.

| Term           | Description                                                                |
| -------------- | -------------------------------------------------------------------------- |
| **Helm**       | Tool that installs and manages Kubernetes (K8s) applications using Charts. |
| **Chart**      | A Helm package containing:                                                 |
|                | - `Chart.yaml`: Describes the package.                                     |
|                | - One or more templates: Contain K8s manifest files.                       |
| **Helm Chart** | You can:                                                                   |
|                | - Create your own Helm Charts.                                             |
|                | - Push them to a Helm Repository.                                          |
|                | - Download and use existing ones.                                          |

## Why Use Helm Charts?

-   Helm simplifies Kubernetes application deployment by bundling all necessary manifest files into a single package called a **Chart**.
-   Instead of everyone writing their own Kubernetes YAML files, Helm provides reusable, version-controlled Charts.
-   You can use **existing official Charts** created by trusted sources:
    -   Example: MySQL Chart from MySQL, ElasticSearch Chart from ElasticSearch.

## Sharing and Discovering Charts

-   Helm Charts are stored in repositories.
-   These can be:
    -   **Public repositories** (e.g., Helm Hub)
    -   **Private repositories** within your organization (e.g., Nexus)
-   You can search for Charts using:
    -   `helm search <keyword>`
    -   The [Helm Hub](https://artifacthub.io)

---

## Helm as a Templating Engine

## How Helm Works with Templates

-   Helm **renders templates** using provided values and communicates with the **Kubernetes API** to deploy resources.

### Typical Helm Chart Structure

```
mychart/
├── Chart.yaml # Metadata about the chart
├── values.yaml # Default configuration values
├── charts/ # Dependency charts
└── templates/ # Templated Kubernetes manifests
```

### What’s Inside a Chart?

1. **Chart.yaml** – Contains the chart’s name and metadata.
2. **values.yaml** – Defines default values used in templates.
3. **charts/** – Holds dependent charts (sub-charts).
4. **templates/** – Contains the Kubernetes resource templates.

---

## Benefits of Templating

-   Templates let you **define a common blueprint** for Kubernetes resources.
-   Helm allows dynamic customization by replacing placeholders with actual values at deployment time.

## Real-World Use Case

-   Many deployments share common configuration.
-   Helm makes it easy to:
    -   Reuse the same template with different values.
    -   Deploy the same application across multiple Kubernetes clusters with minimal changes. (e.g., dev, staging, production)

# CD - Jenkins & Kubernetes

### Pull Docker Images into K8s cluster

-   _PRIVATE REPO_: For K8s to fetch the Docker image from a private repository, you need to create a **Secret** that contains the credentials for the private registry.
-   _PUBLIC REPO_: If you use a public repository, you don't need to create a Secret, as K8s can pull images from public repositories without authentication.

### Step to pull image from Private Registry

_1. Create Secret Component_

-   Contains credentials from Docker registry

```bash
kubectl create secret docker-registry my-registry-secret \
    --docker-server=https://index.docker.io/v1/ \
    --docker-username=my-username \
    --docker-password=my-password \
```

_2. Configure Deployment_

-   Use Secret using imagePullSecrets field in the Deployment configuration file

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
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
                  image: my-private-registry/my-image:latest
                  ports:
                      - containerPort: 80
            imagePullSecrets:
                - name: my-registry-secret
```

# Kubernetes Operators

-   Statefull application need constant management and syncing after deployment. So statfull applications, like database need to be operated.
-   Instead of a human operator, you have an automated scripted operator.
-   An **Operator** is a method of packaging, deploying, and managing a Kubernetes application.

## How Operators Work

-   Operators use the **Kubernetes API** to watch for changes in the cluster.
-   They can create, update, and delete resources based on the desired state defined in the Operator's code.
-   Operators can also handle complex tasks like backups, scaling, and failover.
