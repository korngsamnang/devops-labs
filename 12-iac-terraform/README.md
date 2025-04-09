# Infrastructure as Code (IaC) with Terraform

## Introduction to Terraform

-   An open-source **infrastructure as code (IaC)** by HashiCorp.
-   Let's you automate and manage:

    -   your infrastructure
    -   your platform
    -   services that run that platform

-   By defining the resources in human-readable **configuration files** that you can:
    -   version control
    -   share with others
    -   reuse across different environments
-   Definition of configuration files is declarative, meaning you describe the desired state of your infrastructure, and Terraform figures out how to achieve that state.

## Terraform Use Cases

-   **Infrastructure Provisioning**:
    1. Private Network space
    2. EC2 server instances
    3. Install Docker and other tools
    4. Configure Security
    5. ... and more
-   **Manage existing infrastructure**:

    -   Automate the continuous changes to your infrastructure

-   **Replicating Infrastructure**:
    -   Easily replicate infrastructure on different environments (e.g., dev, staging, production) or across different cloud providers.

## Difference of Ansible and Terraform

| Feature         | Terraform                                  | Ansible                                           | Both                         |
| --------------- | ------------------------------------------ | ------------------------------------------------- | ---------------------------- |
| Primary Purpose | Mainly an infrastructure provisioning tool | Mainly a configuration management tool            | Infrastructure as Code (IaC) |
| Strengths       | More advanced in orchestration             | Better for configuring provisioned infrastructure |                              |
| Best Used For   | Provisioning the infrastructure            | Configuring the provisioned infrastructure        |                              |

## How Terraform Works

An important part of Terraform (TF) is that knows:

-   your desired statte (config file) and
-   keeps track of your existing real infrastructure (in astate file)

-   TF compares your desired with actual state to know which changes it needs to make your infrastructure match the desired state.
-   Without state, you would always have to check the current state yourself and see how to update your desired state.

This is the core workflow of Terraform:

1. **Write**:
    - Define your infrastructure resources in the configuration file
    - E.g. creating 2 EC2 instances on AWS
2. **Plan**:
    - Based on the config file and state file,
    - TF creates an execution plan - the change it will make to your infrastructure
3. **Apply**:
    - TF executed the changes to your infrastructure
    - And updates the state file, so it up-to-date with the actual state of your infrastructure

## Terraform Architecture

![Terraform Architecture](https://miro.medium.com/v2/resize:fit:1100/format:webp/0*bJzMGdZBo0zKfbvQ)

> source: [Medium](https://medium.com/@impradeep.techie/terraform-architecture-overview-structure-and-workflow-fdc60697941a)

## Core Terraform Commands

| Command              | Description                                                                                                  |
| -------------------- | ------------------------------------------------------------------------------------------------------------ |
| `terraform init`     | Initializes a Terraform working directory, downloads provider plugins, and sets up the backend.              |
| `terraform plan`     | Creates an execution plan, showing what actions Terraform will take to change                                |
|                      | the infrastructure to match the desired state defined in the configuration files.                            |
| `terraform apply`    | Applies the changes required to reach the desired state of the configuration.                                |
| `terraform destroy`  | Destroys the infrastructure managed by Terraform, removing all resources defined in the configuration files. |
| `terraform fmt`      | Formats Terraform configuration files to a canonical format and style.                                       |
| `terraform validate` | Validates the configuration files for syntax errors and checks if they are valid.                            |
| `terraform output`   | Displays the output values defined in the configuration files after applying changes.                        |

_Example:_

```bash
# Configure the AWS Provider
provider "aws" {
  version = "3.0"
  region  = "us-west-2"
}

# Create a VPC
resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0/16"
}
```

## Terraform Providers

A provider is what Terraform uses to create and manage resources on a specific platform (like AWS, Azure, Kubernetes, or even GitHub).

-   Provider = bridge between Terraform and a service
-   It defines how to interact with the service's API
-   You must configure a provider in your Terraform code to use it

Example:
If you're using Terraform to deploy infrastructure on AWS, the AWS provider is what connects Terraform to your AWS account.

## Resources and Data Sources

-   **Resources**: The components of your infrastructure that you want to create or manage (e.g., EC2 instances, S3 buckets, etc.).

Example:

```bash
# Configure the AWS Provider
provider "aws" {
    version = "3.0"
    region = "us-west-2"
}

# Create an EC2 instance
resource "aws_instance" "my_instance" {
    ami           = "ami-0c55b159cbfafe1f0" # Example AMI ID
    instance_type = "t2.micro"
    tags = {
        Name = "MyInstance"
    }
}
```

-   **Data Sources**: Allow you to fetch information from existing resources or services without managing them directly. They are read-only and do not create or modify resources.

Example:

```bash
# Fetch an existing AMI ID
data "aws_ami" "latest_amazon_linux" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}
```

## Terraform Variables

-   Variables (Input variables) let you customize behavior without editing the terraform configuration files.

**2 steps to use variables in Terraform:**

1. Define variable and use it in your TF script
2. Set the variable values when applying the script

Example:

```bash
# Define a variable
variable "subnet_cidr_blcok" {
  description = "The CIDR block for the subnet"
}

resource "aws_vpc" "development-vpc"{
  cidr_block = "10.0.0/16"
  tags = {
    Name = "Development VPC"
  }
}

resource "aws_subnet" "development-subnet" {
  vpc_id            = aws_vpc.development-vpc.id
  cidr_block        = var.subnet_cidr_blcok # Use the variable here
  availability_zone = "us-west-2a"
  tags = {
    Name = "Development Subnet"
  }
}

```

** 3 ways to set variable values for the defined variables:**

1. **Command Line**: Use the `-var` flag when running `terraform apply` or `terraform plan`.

    ```bash
    terraform apply -var="subnet_cidr_block=10.0.1/24"
    ```

2. **Variable File**: Create a file named `terraform.tfvars` or any `.tfvars` file and define your variables there.

    ```bash
    subnet_cidr_block = "10.0.1/24"
    ```

3. **Environment Variables**: Set environment variables with the prefix `TF_VAR_` followed by the variable name.

    ```bash
    export TF_VAR_subnet_cidr_block="10.0.1/24"
    ```

**REAL use case with variables:**

-   Same script parameterized for different environments (e.g., dev, staging, prod)
-   Use different variable values for each environment to customize the infrastructure without changing the script.

## Git Repository for Terraform Projects

-   Like your application code, Terraform script being infrastructure as code, should be managed by version control system Git and be hosted in a git repository.

**Why?**

-   safekeepping
-   history of changes
-   team collaboration
-   review infrastructure changes using merge requests

> Best practice: Have a separate git repository for app code and terraform code.

## Executing Commands on Virtual Servers

### User Data Attribute

-   **Purpose**: Pass initial configuration during VM creation
-   **Cloud Support**: Available in most cloud providers (AWS, Azure, GCP)
-   **Use Case**:
    -   First-boot configuration
    -   Installing essential packages
    -   Setting up basic services

### Provisioners (Alternative Approach)

-   **Types**:
    -   `remote-exec`: Execute commands on the new resource
    -   `file`: Copy files to the new resource
    -   `local-exec`: Run commands on your local machine

#### Provisioner Comparison

| Type          | Execution Location | Key Parameters          | Common Use Case             |
| ------------- | ------------------ | ----------------------- | --------------------------- |
| `remote-exec` | Remote resource    | `inline`, `script`      | Post-creation configuration |
| `file`        | Remote resource    | `source`, `destination` | Deploy configuration files  |
| `local-exec`  | Local machine      | `command`               | Trigger local workflows     |

---

### When to Use Each Method

1. **Prefer User Data When**:

    - Cloud provider supports it
    - Need simple initialization
    - Want to maintain idempotency

2. **Consider Provisioners When**:

    - Need complex setup not possible via user data
    - Must copy files to new instances
    - Require integration with local systems

3. **Better Alternatives**: - For complex configuration, use Ansible after provisioning - For file distribution, consider cloud-init or container images

⚠️ **Provisioner Warnings**:

-   Break Terraform's idempotency principle
-   Create opaque dependencies in your infrastructure
-   Make state management more difficult
-   Should be used as last resort

> **Recommended Workflow**:  
> Terraform (provision) → Ansible (configure) → Monitoring
