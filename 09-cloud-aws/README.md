# AWS Services

## Introduction to AWS

-   **AWS** = Amazon Web Services
-   Currently the most popular cloud platform
-   Offers a huge collection of cloud services

## AWS Account Structure

AWS has 3 scopes for resource deployment:

1. **Global** (e.g., IAM, Route 53)
2. **Region** (geographical area)
3. **Availability Zones** (isolated locations within regions)

## Creating an AWS Account

### Registration Process

1. Sign up on [AWS website](https://aws.amazon.com/)
2. Provide payment information (credit card required)

### Free Tier Benefits

-   1-year free access to basic resources
-   **Note**: Some services are NOT included in free tier
-   **Best Practice**: Delete unused resources to avoid charges

## Identity and Access Management (IAM)

### IAM Basics

-   Manages access to AWS services and resources
-   Key components:
    -   **Users**: Individual accounts
    -   **Groups**: User collections
    -   **Policies**: Permission sets

### Important Notes

-   **Root user** has unlimited privileges (use sparingly)
-   **Best Practice**: Create admin users with limited privileges

### IAM User Types

1. **Human Users**: For individual team members
2. **System Users**: For services like Jenkins needing AWS access

### IAM Roles

-   Similar to users but not tied to individuals
-   Used to grant AWS services access to other services
-   Setup process:
    1. Create IAM Role
    2. Assign to AWS Service
    3. Attach Policies

## AWS Infrastructure

### Regions & Availability Zones

-   **Region**: Physical location with data centers
-   **Availability Zone (AZ)**: One or more discrete data centers
-   **Best Practice**: Choose regions close to your users

### Virtual Private Cloud (VPC)

-   Isolated network in AWS cloud
-   Key characteristics:
    -   Created per region
    -   Spans all AZs in region
    -   Required for resource deployment

### Subnets

-   Range of IP addresses in your VPC
-   Types:
    -   **Public**: Accessible from internet
    -   **Private**: Internal-only access
-   Created per Availability Zone

## Networking & Security

### Internet Gateway

-   Connects VPC/subnets to the internet
-   Enables external communication

### Security Measures

1. **Network ACLs**: Subnet-level access control
2. **Security Groups**: Instance-level firewall
3. **CIDR Blocks**: Defines IP address ranges

## Elastic Compute Cloud (EC2)

### What is EC2?

-   Virtual servers in AWS cloud
-   Provides scalable computing capacity

### EC2 Deployment Steps

1. Create EC2 instance
2. Connect via SSH
3. Install Docker
4. Run containers
5. Configure firewall

### Launching EC2 Instance

1. Choose OS Image
2. Select instance type
3. Configure network
4. Add storage
5. Set tags
6. Configure Security Group

## Deployment Automation

### Jenkins to EC2 Deployment

**CI/CD Pipeline Steps**:

1. Connect to EC2 via SSH (using SSH Agent plugin)
2. Execute `docker run` commands

**Configuration**:

1. Install SSH Agent Plugin
2. Configure credentials
3. Use in Jenkinsfile deploy stage

## AWS Command Line Interface (CLI)

### AWS CLI Basics

-   Alternative to AWS Management Console
-   Requires:
    -   Access Key ID
    -   Secret Access Key

### Command Structure

```bash
aws <service> <subcommand> [options]
```

### Example: Launch EC2 via CLI

```bash
aws ec2 run-instances --image-id ami-xyz --count 1 --instance-type t2.micro
```

## Infrastructure as Code Preview

**Before Terraform**:

-   Manual commands
-   No centralized overview
-   Imperative approach

**With Terraform**:

-   Manage resources as code
-   Declarative configuration
-   Version-controlled infrastructure

## AWS Container Services

### Key Services

1. **ECR (Elastic Container Registry)**: Private Docker registry
2. **ECS (Elastic Container Service)**: AWS container orchestration
3. **EKS (Elastic Kubernetes Service)**: Managed Kubernetes

## Best Practices

### General Recommendations

-   Store .pem files in ~/.ssh/ with 400 permissions
-   Never share private keys - each user should have their own

-   Rotate credentials regularly
-   Use IAM roles for services instead of hardcoding credentials
-   Monitor AWS usage to avoid unexpected charges
-   Regularly review IAM policies and permissions

### Official Guides

-   [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
-   [VPC Best Practices](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-best-practices.html)
-   [EC2 Best Practices](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-best-practices.html)
