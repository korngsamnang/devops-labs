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

1. **As Docker container**
2. **Direct OS installation**

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
