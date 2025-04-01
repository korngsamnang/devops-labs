# Kubernetes on AWS

## Container Services on AWS

-   [Amazon ECR](https://aws.amazon.com/ecr/)
    -   Private Docker Registry
-   [Amazon ECS](https://aws.amazon.com/ecs/)
    -   Container Orchestration Service
-   [Amazon EKS](https://aws.amazon.com/eks/)
    -   Container Orchestration Service
    -   Managed Kubernetes Service

## Elastic Container Service

### Different Container Orchestration Tools

| Tool         | Developer                         | Key Features                                                                                                          | Use Case                                           |
| ------------ | --------------------------------- | --------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| ECS          | Amazon                            | - Integrated with AWS services<br>- Managed service (no infrastructure management)<br>- Supports Fargate (serverless) | Best for AWS-centric deployments                   |
| Docker Swarm | Docker                            | - Native Docker integration<br>- Simple setup and lightweight<br>- Uses standard Docker API                           | Small-scale, simple container orchestration        |
| Kubernetes   | Cloud Native Computing Foundation | - Highly scalable and flexible<br>- Extensive ecosystem<br>- Supports multi-cloud deployments                         | Large-scale, complex deployments across clouds     |
| Mesos        | Apache                            | - Can manage both containers and non-container workloads<br>- Highly scalable<br>- Two-level scheduling               | Mixed workloads at large scale                     |
| Nomad        | HashiCorp                         | - Lightweight and simple<br>- Supports containers and non-container apps<br>- Integrates with HashiCorp stack         | Simple orchestration with diverse workload support |

### How does ECS work?

-   **Control Plane**: mananged by AWS
-   Compute Fleet: **Hosted on EC2 instances**, connected and managed by ECS
-   EC2 instance needs to have:
    1. Container Runtime
    2. ECS Agent - for Control Plane communication

### Using EC2 vs AWS Fargate

| Feature            | ECS on EC2                                 | ECS on Fargate                     |
| ------------------ | ------------------------------------------ | ---------------------------------- |
| **Hosting**        | Runs on EC2 instances                      | Serverless (no EC2 management)     |
| **Management**     | Containers managed by AWS                  | Containers managed by AWS          |
| **Infrastructure** | Full access & control (you manage VMs)     | Fully managed by AWS (no servers)  |
| **Maintenance**    | You patch, scale, and maintain VMs         | No VM maintenance required         |
| **Pricing Model**  | Pay for entire EC2 instances               | Pay only for vCPU/memory used      |
| **Scaling**        | Manual scaling of EC2 capacity             | Auto-scaling (no fixed resources)  |
| **Provisioning**   | Pre-allocated EC2 resources                | On-demand, serverless provisioning |
| **Best For**       | Heavy workloads needing fine-tuned control | Ephemeral, variable workloads      |

## Elastic Kubernetes Service (EKS)

-   Managed Container service to run and scale K8s applications

### Different of ECS and EKS

| Category          | ECS (AWS Elastic Container Service) | EKS (AWS Elastic Kubernetes Service) |
| ----------------- | ----------------------------------- | ------------------------------------ |
| **Specificity**   | AWS-specific service                | Uses Kubernetes (open-source)        |
| **Migration**     | Difficult to migrate                | Easier to migrate to other platforms |
| **Complexity**    | Less complex applications           | More complex applications supported  |
| **Cost**          | Control plane is free               | Control plane has costs              |
| **Control Plane** | Managed by AWS                      | Managed by AWS                       |
| **Communication** | Via ECS Agent                       | Via Kubernetes Worker Processes      |
| **API**           | Proprietary AWS API                 | Standard Kubernetes API              |
| **Community**     | Limited to AWS ecosystem            | Large community (Helm charts, etc.)  |
| **Use Case**      | When deeply integrated with AWS     | When using or planning to use K8s    |

### How EKS works?

-   EKS deploys and manages the control plane, including:
    -   **API Server**: Exposes Kubernetes API
    -   **etcd**: Key-value store for cluster state
    -   **Controller Manager**: Manages controllers for cluster state
    -   **Scheduler**: Assigns pods to nodes based on resource availability
-   **Worker Nodes**: EC2 instances running the Kubernetes worker processes

-   **High Availability**: EKS control plane is automatically replicated across multiple Availability Zones (AZs) for fault tolerance.

**Different choices to host your Worker Nodes:**
| Hosting Option | Management Level | Description |
|-------------------------|------------------|-----------------------------------------------------------------------------|
| **EKS with EC2 Instances** | Self-managed | You fully manage the infrastructure of Worker Nodes (scaling, patching, etc.) |
| **EKS with Nodegroup** | Semi-managed | AWS creates/deletes EC2 instances automatically, but you configure the node group |
| **EKS with Fargate** | Fully-managed | No worker nodes to manage – AWS handles all infrastructure automatically |

### High-Level Steps to create an EKS cluster

1. Provision an EKS cluster (Control Plane Nodes)
2. Create Nodegroup of EC2 Instances (Worker Nodes)
3. Connect Nodegroup to the EKS cluster
4. Deploy your containerized applications

### Details Steps to create an EKS cluster

1. create EKS IAM Role
2. create VPC for Worker Nodes
3. create EKS cluster (Master Nodes)
4. connect kubectl to EKS cluster
5. create EC2 IAM Role for Node Group
6. create Node Group and attach to EKS cluster
7. configure auto-scaling group for Node Group
8. deploy applications using kubectl

**1. Create EKS IAM Role**

-   Create IAM Role in AWS account
-   Assign Role to EKS cluster managed by AWS to allow AWS to create and manage components on our behalf

**2. Create VPC for Worker Nodes**

-   EKS cluster needs specific networking configuration
-   Worker Nodes need specific Firewall configurations for Control Plan-Worker communication
-   Best practice Configure **public** and **private** subnet
-   Through IAM Role you give K8s permission to change VPC configurations

**3. Create EKS Cluster (Master Nodes)**

-   Manually
    -   Difficult to replicate
    -   Or do multiple times
-   eksctl CLI
    -   Command Line Tool for working with EKS clusters that automates many manual tasks
    -   Execute just 1 command
    -   Necessary resources get create and configured automatically in the background
    -   Cluster will be created with default parameters
    -   But you can customize your cluster with CLI options
-   IaC
    -   Use Infrastructure as Code tool like Terraform to define and create EKS cluster (Will be covered in the next section)

**4. Connect kubectl to EKS Cluster**

-   kubectl is the command line tool to interact with Kubernetes clusters
-   After EKS cluster is created, you need to configure kubectl to connect to the EKS cluster
-   This is done by updating the kubeconfig file with the EKS cluster details
-   You can use the AWS CLI to automatically update the kubeconfig file with the EKS cluster details

**5. Create EC2 IAM Role for Node Group**

-   Kubelet is the main worker process - scheduling, managing Pods and communicate with other AWS services
-   That's why Kubelet on Worker Node needs permission
-   Create Role for Node Group

> **Note:** With Node Group all neccessary worker processes (container runtime, kubelet, kube-proxy) are installed automatically on the EC2 instances.

## Elastic Container Registry (ECR)

-   Repository for Container Images
-   Store, manage, and deploy container images
-   Alternative to Docker Hub or Nexus
-   Integrated well with other AWS services

### How ECR works?

-   **Repositories**: ECR stores container images in repositories
-   **Image Push**: Developers push container images to ECR repositories
-   **Image Pull**: ECS/EKS can pull images from ECR to run containers
-   **Authentication**: ECR uses AWS IAM for authentication and authorization

## CD - Jenkins & EKS

### Deploy to EKS cluster from Jenkins Pipeline

1. **Install kubectl**: CLI inside Jenkins to interact with EKS
2. **Install aws-iam-authenticator**: Tool to authenticate with EKS cluster
3. **Create kubeconfig** file to connect to EKS cluster
    - contains all the necessary information
4. **Add AWS credentials** on Jenkins for AWS account autentication
5. **Adjust Jenkinsfile** to configure EKS cluster deployment

> **Best Practices:** Create AWS IAM User for Jenkins with limited permissions.

## Best Practices

-   Create VPC with private and public subnets
-   Security: User AWS Key Management Service (KMS) keys to provide envolope encryption of Kubernetes secrets

**Official AWS best practices to EKS:**

-   [EKS Best Practices](https://aws.github.io/aws-eks-best-practices/)
-   [EKS Security Best Practices](https://aws.github.io/aws-eks-best-practices/security/)
-   [EKS Networking Best Practices](https://aws.github.io/aws-eks-best-practices/networking/)
