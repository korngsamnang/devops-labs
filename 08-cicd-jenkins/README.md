# Build Automation & CI/CD with Jenkins

## What is Build Automation?

### Part 1

Build automation is the process of automating:

-   Retrieval of source code
-   Execution of automated tests
-   Compilation into binary code/building Docker images
-   Pushing artifacts to repositories
-   Deployment of artifacts

**Core Concept**: Build automation and CI/CD are at the heart of DevOps

### Part 2

Manual build tasks that automation replaces:

-   Stashing changes and switching branches
-   Logging into Docker repositories
-   Setting up test environments
-   Waiting for tests to complete
-   Building and pushing Docker images
    **Solution**: Dedicated build server that:
-   Has pre-configured test environments
-   Stores Docker credentials
-   Contains all necessary tools
-   Triggers builds automatically

## What is CI/CD?

**CI/CD** = Continuous Integration / Continuous Deployment  
Goal: "Release early and often" through automation
| Continuous Integration | Continuous Deployment |
|------------------------|-----------------------|
| Frequent code integration | Automated deployment |
| Automated building/testing | Multiple environments |
| Shared repository | Production releases |

## Jenkins - Industry Leader

Currently one of the most used CI/CD tools. Alternatives exist but Jenkins dominates.

## Introduction to Jenkins

### Part 1

1. Software installed on dedicated server
2. Web UI for configuration
3. Requires installation of supporting tools (Docker, Maven, npm, etc.)
4. Configure build tasks (testing, building, deployment)
5. Set up automatic triggers
   **Capabilities**:

-   Run tests
-   Build artifacts
-   Publish artifacts
-   Deploy applications
-   Send notifications

### Part 2

**Plugins**: Extend Jenkins functionality

-   Docker integration
-   Build tools
-   Repository connections
-   Deployment targets

### Part 3

**DevOps Engineer Responsibilities**:

-   Jenkins administration
-   Plugin management
-   Credential setup
-   Access configuration

**Requirements**:

-   Build tools available
-   Docker access
-   Secure credential storage
-   Proper user permissions

## Jenkins Installation Options

1. **As Docker container** :

    - Quick setup
    - Easy to manage
    - Requires Docker installed

    ```bash
    docker run -d -p 8080:8080 jenkins/jenkins:lts
    ```

2. **Direct OS installation** :

    - More control
    - Requires manual setup
    - Can be resource-intensive

    ```bash
    wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
    sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
    sudo apt-get update
    sudo apt-get install jenkins
    ```

## Getting Started

Basic setup flow:

1. Initial login with generated password
2. Automatic plugin installation
3. Configuration completion

## Roles in Jenkins

| Jenkins Administrator          | Jenkins User            |
| ------------------------------ | ----------------------- |
| Manages Jenkins infrastructure | Creates build jobs      |
| Installs plugins               | Configures pipelines    |
| Handles backups                | Sets up workflows       |
| Operations/DevOps teams        | Developers/DevOps teams |

## Installing Build Tools in Jenkins

### Part 1

Required tools vary by application type:

**Java Applications**:

-   Maven/Gradle for:
    -   Test execution
    -   JAR building

**JavaScript Applications**:

-   npm for:
    -   Test running
    -   Packaging
    -   Repository pushing

### Part 2

**Installation Methods**:

1. **Jenkins Plugins** (UI installation)
2. **Server Installation** (manual setup)

**Docker Note**: When Jenkins runs in container, mount host Docker:

```bash
-v /var/run/docker.sock:/var/run/docker.sock
```

## Jenkins Job Types

### Freestyle Jobs

-   Simple task orchestration
-   UI configuration
-   Limited flexibility
-   Basic plugin fields

### Pipeline Jobs

-   Complex workflows
-   CI/CD implementation
-   Stage division
-   UI + scripting (Pipeline as Code)
-   Advanced capabilities

**Pipeline Advantages:**

-   Parallel task execution
-   User input handling
-   Variable usage
-   Conditional logic

### Multibranch Pipelines

Automatically manages pipelines for different branches containing Jenkinsfiles
**Benefits:**

-   Automatic branch discovery
-   No manual job creation
-   Branch-specific configurations

### Job Type Comparison

| Feature       | Freestyle   | Pipeline        | Multibranch Pipeline      |
| ------------- | ----------- | --------------- | ------------------------- |
| Purpose       | Single task | CI/CD workflows | Branch management         |
| Configuration | UI config   | Code + UI       | Automatic branch handling |

## Pipeline as Code

Pipeline as Code allows defining CI/CD pipelines in code, typically using a `Jenkinsfile`. This enables version control and collaboration on pipeline definitions.
Modern best practice: Define pipelines in code (Jenkinsfile) rather than UI

** Implementation Options**:

1. **Direct UI editing**: Use Jenkins UI to create and edit pipelines.
2. **Jenkinsfile**: Store pipeline code in a file named `Jenkinsfile` in the repository.
3. **Shared Libraries**: Use shared libraries for reusable pipeline code across multiple projects.

## Jenkinsfile Basics

Text file defining Jenkins pipelines, stored in project repository.

**Syntax Options**:

1. Scripted:

-   Original Groovy syntax
-   Maximum flexibility
-   Steeper learning curve

**Example Jenkinsfile (Scripted)**:

```groovy
node {
    stage('Build') {
        echo 'Building...'
    }
    stage('Test') {
        echo 'Testing...'
    }
    stage('Deploy') {
        echo 'Deploying...'
    }
}
```

2. Declarative:

-   Easier starting point
-   Predefined structure
-   Less powerful than scripted

**Example Jenkinsfile (Declarative)**:

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
```

**Key Components**:

-   `pipeline`: Defines the pipeline structure (Top-level block)
-   `agent`: Defines where the pipeline runs (e.g., any available agent)
-   `stages`: Contains individual stages
-   `stage`: Represents a specific phase in the pipeline
-   `steps`: Contains the actions to be performed in each stage

**Advanced Features**:

-   `post`: Post-build actions (e.g., notifications, cleanup)
-   `environment`: Define environment variables
-   `tools`: Specify tools required for the pipeline (e.g., Maven, Node.js)
-   `triggers`: Define automatic triggers for the pipeline (e.g., SCM changes, scheduled builds)
-   `parameters`: Define input parameters for the pipeline (e.g., user inputs, build parameters)

## Jenkins Terminology

| Term                     | Simple Explanation                                                                   |
| ------------------------ | ------------------------------------------------------------------------------------ |
| **Job** (or **Project**) | A task or set of instructions Jenkins will run (e.g., build code, run tests).        |
| **Build**                | A single run of a Job, producing logs and results (success/failure).                 |
| **Pipeline**             | A series of automated steps (code-based) for CI/CD (e.g., build → test → deploy).    |
| **Plugin**               | Add-on that extends Jenkins' features (e.g., Git integration, Slack notifications).  |
| **Node**                 | A machine (or agent) that executes Jobs (can be the main server or worker machines). |
| **Workspace**            | The folder where Jenkins stores files while running a Job.                           |
| **Jenkinsfile**          | A script defining a Pipeline (stored in your code repository).                       |
| **Stage**                | A logical step in a Pipeline (e.g., "Build", "Test").                                |

## Credentials Management

### Scope

| Type       | Access Level           |
| ---------- | ---------------------- |
| **System** | Server-only access     |
| **Global** | Accessible by all jobs |

### Credential Types

-   **Secret text** (e.g., API tokens)
-   **Username/password**
-   **Secret files**
-   **SSH keys**
-   **Certificates**
-   **Docker credentials**

## Jenkins Shared Libraries

**Purpose**
Share common pipeline code across projects to reduce duplication

**Use Cases**

1. Shared logic across microservices
2. Company-wide standard tasks

**Implementation Steps**

1. Create library repository
2. Write Groovy code
3. Configure Jenkins access
4. Reference in Jenkinsfiles

## Triggering Jenkins Jobs

### Methods

1. **Manual**:

-   Production pipelines
-   Special cases

2. **Automatic**:

-   Git changes (webhooks)
-   Requires configuration in Git repository

3. **Scheduled**:

-   Regular intervals (e.g., nightly builds)
-   Long-running tasks
-   Maintenance tasks

4. **Remote API**:

-   Trigger jobs from external systems
-   Useful for integration with other tools

## Webhooks

Automatically trigger builds on repository commits
**Configuration Steps**:

1. Git repository settings
2. Jenkins job setup

## Software Versioning

** Version Components**
| Component | Purpose | Examples |
|-----------|----------------------------------|-----------------------|
| **Major** | Breaking changes | 2.0.0 (from 1.9.0) |
| **Minor** | Backward-compatible new features | 1.2.0 (from 1.1.0) |
| **Patch** | Bug fixes | 1.0.1 (from 1.0.0) |
| **Suffix**| Release status indicator | `-SNAPSHOT`, `-alpha` |

**Automated Versioning**
Build tools can auto-increment versions:

-   Maven/Gradle commands
-   npm version handling

## Best Practices

**Pipeline Code**

1. Store Jenkinsfiles in source control
2. Use standard naming (`Jenkinsfile`)
3. Contain work within stages
4. Add Groovy shebang (`#!/usr/bin/env groovy`)
5. Proper input handling

**General**

1. Implement automatic versionings
2. Use Shared Libraries
3. Follow [industry best practices](https://www.lambdatest.com/blog/jenkins-best-practices/)
