# Build Tools & Package Manager

## What are Build and Package Manager Tools?

### Part 1

-   Used to package applications into single movable files (artifacts)
-   "Building the code" means:
    -   Compiling the code
    -   Compressing the code
    -   Packaging hundreds of files into one file

**What is an "artifact"?**

-   Includes application code and all its dependencies
-   Stored in artifact repositories (e.g., Nexus, JFrog Artifactory) for:
    -   Multiple deployments
    -   Backup purposes

### Part 2

Artifact files differ by programming language:

-   **Java**: JAR or WAR files (includes code + dependencies)
-   **JavaScript**: ZIP or TAR files

## Different Build Tools by Language

Build tools covered in this module:

-   **Java**: Gradle and Maven
-   **JavaScript**: npm

## Building Artifacts

-   Build tools provide CLI commands to create artifacts
-   **Java Build Process**:
    -   Installs dependencies
    -   Compiles and compresses code
    -   Configuration:
        -   Maven: XML
        -   Gradle: Groovy

**Output Locations**:

-   Gradle: `build` folder
-   Maven: `target` folder

## Build Tools in Development

Developers use build tools to:

-   Run apps locally
-   Manage dependencies
-   Execute tests

**Dependency Management**:

-   Defined in:
    -   Maven: `pom.xml`
    -   Gradle: `build.gradle`
-   Find Java libraries in Maven Central Repository

## JavaScript Applications

### Key Differences:

-   No standard artifact type (typically ZIP/TAR)
-   Uses package managers (npm/yarn) rather than build tools
-   `package.json` file manages dependencies

### npm Commands:

-   `npm install`: Installs dependencies
-   `npm start`: Starts application
-   `npm test`: Runs tests
-   `npm publish`: Publishes artifact

### Deployment Process:

1. Copy ZIP/TAR to server
2. Unpack
3. Install dependencies (`npm install`)
4. Run application

## Frontend Packaging

Frontend code requires transpiling (e.g., with Webpack) to:

-   Convert JSX/ES6+ to browser-compatible code
-   Minify and bundle code

## Build Tools Across Languages

| Language   | Tools              |
| ---------- | ------------------ |
| Java       | Maven, Gradle      |
| JavaScript | npm, yarn, webpack |
| Python     | pip                |
| C/C++      | conan              |
| C#         | NuGet              |
| Go         | dep                |
| Ruby       | RubyGems           |
| Rust       | Cargo              |
| PHP        | Composer           |

Common patterns:

1. Dependency file (e.g., `package.json`, `pom.xml`)
2. Central repository for dependencies
3. CLI tools for:
    - Installing dependencies
    - Testing
    - Building
    - Publishing

## Publishing Artifacts

1. Build JAR/ZIP package
2. Push to artifact repository
3. Downloadable via tools like curl/wget

## Docker Impact (Change in how we use artifacts)

-   Replaces multiple artifact types with Docker images
-   Benefits:
    -   Single artifact type
    -   No need to install dependencies on server
    -   Self-contained execution environment

**Note**: Still need to build applications - more on Docker in later modules!

## Why DevOps Engineers Need This Knowledge

1. Help developers understand deployment requirements
2. Configure CI/CD pipelines to:
    - Execute tests
    - Build Docker images
    - Deploy to servers
3. Key processes to automate:
    - Dependency installation
    - Testing
    - Building
    - Pushing to repositories
