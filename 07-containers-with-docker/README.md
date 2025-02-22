# Containers with Docker

## What is Docker?

-   Open source containerization platform
-   Packages applications into containers
-   Made containers popular (though they existed before Docker)
    **What is a container?**
-   Package containing application + all dependencies + configuration
-   Standardized artifact for development, shipping, and deployment
-   Improves development and deployment efficiency

## Where container artifacts are hosted

-   Container repositories (public and private)
-   Public repository: Docker Hub

## Application Development: Before vs After Docker

### Before Containers

-   Different installation process on each OS
-   Many error-prone steps
-   Difficult to run multiple versions

### After Containers

-   Isolated environment
-   Pre-configured packages
-   Single command installation
-   Easy version management

## Application Deployment: Before vs After Docker

### Before Containers

-   Server configuration needed
-   Dependency conflicts
-   Textual deployment guides
-   Misunderstandings common

### After Containers

-   Devs and Ops collaborate on container packaging
-   No server environment configuration needed (except Container Runtime)

## Docker vs Alternatives

-   Most popular container technology
-   Alternatives exist but less common (e.g., Podman, rkt)

## Docker Image vs Docker Container

| Docker Image            | Docker Container        |
| ----------------------- | ----------------------- |
| Package/file artifact   | Running instance        |
| Not in running state    | Active environment      |
| Multiple layers         | Virtual filesystem      |
| Linux base + app layers | Port binding for access |

## Containers vs Virtual Machines

| Containers              | Virtual Machines       |
| ----------------------- | ---------------------- |
| App-layer abstraction   | Hardware abstraction   |
| Shared OS kernel        | Full OS copy per VM    |
| Smaller size            | Larger size            |
| Faster startup          | Slower startup         |
| OS compatibility limits | Cross-OS compatibility |

**Note**: For Windows/Mac, use Docker Desktop to run Linux containers

## Docker Architecture Components

1. **Docker CLI**: Interface for commands
2. **Docker Daemon**: Manages containers/images
3. **Docker Registry**: Stores images

## Docker Installation

-   **Linux**: Native support
-   **Mac/Windows**: Docker Desktop

## Main Docker Commands

### Basic Commands

```bash
docker run     # Create container from image
docker pull    # Download image
docker start   # Start stopped container
docker stop    # Stop running container
docker images  # List local images
docker ps      # List running containers
docker ps -a   # Show all containers
```

### Image Management

```bash
docker rmi <image>  # Remove image
docker build -t <name> .  # Build image from Dockerfile
docker tag <image> <new_name>  # Tag image with new name
docker push <image>  # Push image to registry
```

### Container Management

```bash
docker exec -it <container> <command>  # Run command in container
docker logs <container>  # View container logs
docker rm <container>  # Remove container
docker inspect <container>  # View container details
docker cp <container>:<path> <local_path>  # Copy files from container to host
docker network ls  # List networks
docker network create <network_name>  # Create network
docker network connect <network_name> <container>  # Connect container to network
docker network disconnect <network_name> <container>  # Disconnect container from network
```

## Ports in Docker

-   **Problem**: Host machine port conflicts
-   **Solution**: Port mapping
    -   host_port:container_port
    -   Example: 3001:80

## Workflow with Docker

1. Build image (Dockerfile)
2. Push to repository
3. Pull on deployment server
4. Run container

## Docker Compose

-   Tool for multi-container applications
-   YAML configuration file
-   Automatically creates shared network
-   Easier than multiple docker run commands

## Dockerfile

-   Text file with image build instructions
-   Each instruction creates a layer
-   Common commands:
    -   `FROM` (base image)
    -   `COPY` (add files)
    -   `RUN` (execute commands)
    -   `CMD` (default command)

### Example Dockerfile

```dockerfile
FROM python:3.9-slim
COPY app.py /app.py
RUN pip install flask
CMD ["python", "/app.py"]
```

**Build command**:

```bash
docker build -t my-flask-app .
```

## Private Docker Repositories

-   Company use: Private registries (e.g., AWS ECR)
-   Differences from Docker Hub:
    1. Authentication required
    2. Tag images with registry address
    3. Push tagged images

**Example**:

```bash
docker pull 523450290.dkr.ecr.eu-central-1.amazonaws.com/my-app:1.0
```

## Docker Volumes

-   Persist data beyond container lifecycle
-   3 Types:

    1. **Host volumes**: Bind mount to host directory

    -   Example: `-v /host/path:/container/path`
    -   Useful for development, not recommended for production

    2. **Named volumes**: Managed by Docker, stored in `/var/lib/docker/volumes`. Referenced by name (recommended for production)

    -   Example: `-v my_volume:/container/path`
    -   Automatically created if not exists
    -   Useful for data persistence across container updates

    3. **Anonymous volumes**: Created without a name, used for temporary data

    -   Example: `-v /container/path`
    -   Not recommended for production, as they are hard to manage

**docker-compose example**:

```yaml
version: "3"
services:
    web:
        image: my-web-app
        volumes:
            - my_volume:/data
volumes:
    my_volume:
```

## Docker Networking

-   Containers can communicate over networks
-   Default network: `bridge`
-   Custom networks can be created
-   Types of networks:
    1. **Bridge**: Default, isolated network
    2. **Host**: Shares host network stack
    3. **Overlay**: For multi-host communication (Docker Swarm)
    4. **Macvlan**: Assigns MAC addresses to containers

## Docker Best Practices

**Security**

-   Use official/trusted base images
-   Specify image versions
-   Use minimal base images (e.g., Alpine)
-   Create dedicated users (not root)
-   Scan for vulnerabilities
-   Don't include sensitive data (use Docker secrets for sensitive data)

**Efficiency**

-   Optimize layer caching
-   Use `.dockerignore`
-   Multi-stage builds
