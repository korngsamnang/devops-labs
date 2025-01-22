## Demo Project:

Run Nexus on Droplet and Publish Artifact to Nexus

## Technologies Used:

Nexus, DigitalOcean, Linux, Java, Gradle, Maven

## Project Description:

-   Install and configure Nexus from scratch on a cloud server
-   Create new User on Nexus with relevant Permissions
-   Java Gradle Project: Build Jar & Upload to Nexus
-   Java Maven Project: Build Jar & Upload to Nexus

---

## What is an Artifact Repository?

-   Storage of build artifacts produced by continuous integration
-   Makes artifacts available for automated deployment to different environments
-   Provides a central location for artifacts
-   Artifacts are applications built into single files (JAR, WAR, ZIP, TAR, etc.)
-   Repository needs to support specific artifact formats

## What is an Artifact Repository Manager?

-   Can store many different artifact types in one place
-   Eliminates need for separate repositories for each artifact type
-   Single repository for managing all your different artifact types

## Public vs Private Artifact Repository Managers

### Public Repository Managers

-   Host public libraries/frameworks used as dependencies
-   Example: Maven Central
-   Can make your own projects publicly available

### Private Repository Managers (e.g., Nexus)

-   Private central store for your company
-   Upload and store different built artifacts
-   Retrieve (download) artifacts later
-   For internal company usage

## Nexus - Artifact Repository Manager

-   Has open source and commercial versions
-   Features:
    -   Host own repositories
    -   Proxy repositories (intermediary to another repository)
    -   Fetch everything from one place (company internal + public artifacts)
    -   Support for multiple repositories for different formats/teams

### Supported Formats in Nexus:

-   maven2 (for Java JAR artifacts)
-   nuget (for .NET packages)
-   docker (for Docker images)

## Features of Repository Manager

-   Integration with LDAP
-   Flexible and powerful REST API for tool integration
-   Backup and Restore capabilities
-   Multi-Format Support (zip, tar, docker etc)
-   Metadata Tagging (labeling and tagging artifacts)
-   Cleanup Policies (automatically delete files matching conditions)
-   Search functionality (across projects, artifact repos etc.)
-   User token support for authentication

## Install and Run Nexus on Cloud Server

### Part 1: Initial Setup

1. Create Ubuntu Server (Droplet) - min 4GB RAM & 2 CPUs
2. Open SSH port 22
3. Install Java 8
4. Download and Install Nexus
5. Create "nexus" user and group (Best Practice: Run applications with own user)

### With Nexus installation you get:

-   Subdirectories depending on your Nexus configuration
-   IP address logs of who accessed Nexus
-   Nexus application logs
-   Storage for uploaded files and metadata
-   Folder that can be used for backup

### Part 2: Configuration

1. Make Nexus own the folders
2. Start with nexus user
3. Open port 8081 with firewall
4. Access from browser

**Best Practice Note:**

-   Services should not run with root user permissions
-   Create dedicated user for each service (e.g., "nexus" user)
-   User should have only permissions needed for that specific service

## Login & Default Repositories

-   Login with default "admin" user (automatically created)
-   Default repositories are created during installation with different TYPES

### Repository Types

-   **Proxy**: Intermediary to another repository
-   **Group**: Contains a group of repositories
-   **Hosted**: For hosting company-internal artifacts

### Repository Formats

-   maven2: for java jar artifacts
-   nuget: for .NET packages
-   docker: for Docker images

Each technology (Python, Java, Docker) has its artifact type (jar, image), which can be saved in its own artifact repository format.

## Publish Artifact to Repository

-   Can push artifacts to Nexus using different tools
-   Use built-in commands of build tools (Maven, Gradle)
-   Need to configure each build tool with Nexus credentials and address

## Nexus REST API

-   Interact with Nexus using REST API to:
    -   Query information
    -   Download artifacts
    -   Upload artifacts
-   How to interact:
    -   Use tools like curl or wget
    -   Need to provide Nexus user credentials
    -   Example API endpoints:
        -   List repositories
        -   List components

**Important Note:**

-   Always reference the documentation
-   Don't try to learn API by heart because:
    1. APIs change frequently
    2. Too many endpoints to memorize

## Component vs Asset

-   **Components**: Abstract concept of what we're uploading (any type/format)
-   **Assets**: Actual physical packages/files
-   Relationship: 1 component = 1 or more assets

## Cleanup Policies & Scheduled Tasks

### Cleanup Policies

-   Clean up old artifacts to save space
-   Define rules like:
    -   Delete artifacts older than X days
    -   Delete artifacts not downloaded in Y days

### Scheduled Tasks

-   Create custom cleanup policies per repository
-   Nexus allows scheduling tasks to be applied to repositories on configurable schedules
