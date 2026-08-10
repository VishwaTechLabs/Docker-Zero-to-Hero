# Docker --- Zero to Production Documentation

### VishwaTech Labs by Vishwanath Gowda H

> A practical, student-friendly Docker reference covering fundamentals,
> CLI, Dockerfiles, images, containers, networking, storage, Compose,
> registries, security, troubleshooting, and CI/CD.

------------------------------------------------------------------------

## 1. What is Docker?

Docker is a platform for packaging and running applications as
containers.

A **container** packages an application together with the files,
libraries, runtime, configuration, and dependencies it needs to run
consistently.

### Why Docker?

Without containers:

``` text
Developer Laptop
    ↓
"Works on my machine"
    ↓
Different OS / runtime / library
    ↓
Application fails
```

With Docker:

``` text
Source Code
    ↓
Dockerfile
    ↓
Docker Image
    ↓
Container
    ↓
Same application behavior across environments
```

### Main benefits

-   Consistent environments
-   Fast application startup
-   Isolation
-   Portable deployments
-   Efficient resource usage
-   Easy CI/CD integration
-   Easy scaling
-   Reproducible application packaging

------------------------------------------------------------------------

# 2. Containers vs Virtual Machines

## Virtual Machine

``` text
Physical Server
│
├── Host OS
│
├── Hypervisor
│   ├── VM 1
│   │   ├── Guest OS
│   │   └── Application
│   │
│   └── VM 2
│       ├── Guest OS
│       └── Application
```

## Containers

``` text
Physical Server
│
├── Host OS
│
├── Docker Engine
│   ├── Container 1
│   ├── Container 2
│   └── Container 3
```

Containers share the host kernel while remaining isolated from one
another.

### Simple comparison

  Feature      VM                  Container
  ------------ ------------------- -------------------------
  Guest OS     Usually yes         No separate guest OS
  Startup      Generally slower    Generally fast
  Size         Usually larger      Usually smaller
  Isolation    Strong              Process-level isolation
  Packaging    VM image            Container image
  Common use   Full OS workloads   Application workloads

------------------------------------------------------------------------

# 3. Docker Architecture

``` text
Developer
    │
    ▼
Docker CLI
    │
    ▼
Docker Engine
    │
    ├── Images
    ├── Containers
    ├── Networks
    ├── Volumes
    └── Build system
```

Important concepts:

-   Docker Client / CLI
-   Docker Engine
-   Docker daemon
-   Images
-   Containers
-   Networks
-   Volumes
-   Registries
-   Build system

------------------------------------------------------------------------

# 4. Docker Terminology

## Image

A read-only package used to create containers.

Example:

``` text
nginx:latest
python:3.12
ubuntu:24.04
node:22
```

## Container

A running or stopped instance of an image.

``` text
Image
  ↓
docker run
  ↓
Container
```

## Dockerfile

A text file containing instructions used to build an image.

## Registry

A service that stores and distributes container images.

Examples:

-   Docker Hub
-   Amazon ECR
-   GitHub Container Registry
-   Azure Container Registry
-   Google Artifact Registry

## Volume

Persistent storage managed by Docker.

## Network

Provides communication between containers and external systems.

------------------------------------------------------------------------

# 5. Installing Docker

Use the official Docker installation documentation for your operating
system:

-   Docker Desktop: https://docs.docker.com/desktop/
-   Docker Engine: https://docs.docker.com/engine/install/

After installation:

``` bash
docker version
docker info
docker --help
```

Test Docker:

``` bash
docker run hello-world
```

Expected flow:

``` text
Docker CLI
   ↓
Docker Engine
   ↓
Pull hello-world image
   ↓
Create container
   ↓
Run container
   ↓
Display message
```

------------------------------------------------------------------------

# 6. Docker CLI Basics

Check version:

``` bash
docker version
```

Display engine information:

``` bash
docker info
```

Help:

``` bash
docker --help
```

Command-specific help:

``` bash
docker run --help
```

------------------------------------------------------------------------

# 7. Docker Images

List images:

``` bash
docker images
```

Modern equivalent:

``` bash
docker image ls
```

Pull an image:

``` bash
docker pull nginx
```

Pull a specific tag:

``` bash
docker pull nginx:1.27
```

Inspect an image:

``` bash
docker image inspect nginx
```

Remove an image:

``` bash
docker rmi nginx
```

Remove unused images:

``` bash
docker image prune
```

List image history:

``` bash
docker history nginx
```

------------------------------------------------------------------------

# 8. Docker Image Naming

General format:

``` text
registry/namespace/repository:tag
```

Example:

``` text
docker.io/library/nginx:latest
```

Private registry example:

``` text
123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp:v1
```

### Tags

Tags identify image versions.

Examples:

``` text
myapp:latest
myapp:v1
myapp:v1.0.0
myapp:production
myapp:2026-08-10
```

For production, prefer meaningful immutable versioning and/or image
digests rather than relying only on `latest`.

------------------------------------------------------------------------

# 9. Docker Containers

Run a container:

``` bash
docker run nginx
```

Run in detached mode:

``` bash
docker run -d nginx
```

Give the container a name:

``` bash
docker run -d --name web nginx
```

Publish a port:

``` bash
docker run -d --name web -p 8080:80 nginx
```

Meaning:

``` text
Host Port 8080
       │
       ▼
Container Port 80
```

------------------------------------------------------------------------

# 10. Container Lifecycle

``` text
Created
   ↓
Running
   ↓
Stopped
   ↓
Restarted
   ↓
Removed
```

List running containers:

``` bash
docker ps
```

List all containers:

``` bash
docker ps -a
```

Stop:

``` bash
docker stop web
```

Start:

``` bash
docker start web
```

Restart:

``` bash
docker restart web
```

Pause:

``` bash
docker pause web
```

Unpause:

``` bash
docker unpause web
```

Remove:

``` bash
docker rm web
```

Force remove:

``` bash
docker rm -f web
```

------------------------------------------------------------------------

# 11. Container Logs

``` bash
docker logs web
```

Follow logs:

``` bash
docker logs -f web
```

Show timestamps:

``` bash
docker logs -t web
```

Show recent lines:

``` bash
docker logs --tail 100 web
```

------------------------------------------------------------------------

# 12. Execute Commands Inside Containers

Open a shell:

``` bash
docker exec -it web /bin/bash
```

If Bash is unavailable:

``` bash
docker exec -it web /bin/sh
```

Run a single command:

``` bash
docker exec web ls
```

Check processes:

``` bash
docker top web
```

Inspect container:

``` bash
docker inspect web
```

------------------------------------------------------------------------

# 13. Environment Variables

Pass an environment variable:

``` bash
docker run -e APP_ENV=dev myapp
```

Multiple variables:

``` bash
docker run \
  -e APP_ENV=production \
  -e PORT=8080 \
  myapp
```

Check container environment:

``` bash
docker exec web env
```

### Important

Do not bake passwords, API keys, private keys, or tokens into
Dockerfiles or images.

Use runtime secrets/configuration mechanisms instead.

------------------------------------------------------------------------

# 14. Dockerfile

A Dockerfile describes how to build an image.

Example:

``` dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["python", "app.py"]
```

Build:

``` bash
docker build -t my-python-app:1.0 .
```

Run:

``` bash
docker run -d -p 8000:8000 my-python-app:1.0
```

------------------------------------------------------------------------

# 15. Dockerfile Instructions

The major Dockerfile instructions are:

``` text
FROM
RUN
CMD
ENTRYPOINT
COPY
ADD
WORKDIR
ENV
ARG
EXPOSE
USER
VOLUME
LABEL
HEALTHCHECK
SHELL
STOPSIGNAL
ONBUILD
```

------------------------------------------------------------------------

# 16. FROM

Defines the base image or starts a build stage.

``` dockerfile
FROM ubuntu:24.04
```

Python:

``` dockerfile
FROM python:3.12-slim
```

Node:

``` dockerfile
FROM node:22-alpine
```

Multi-stage:

``` dockerfile
FROM node:22 AS build
```

A Dockerfile normally starts with `FROM`, except for allowed parser
directives, comments, and globally scoped `ARG`.

------------------------------------------------------------------------

# 17. RUN

Executes commands during image build.

``` dockerfile
RUN apt-get update
```

Better:

``` dockerfile
RUN apt-get update && \
    apt-get install -y curl && \
    rm -rf /var/lib/apt/lists/*
```

`RUN` creates image filesystem changes during the build.

------------------------------------------------------------------------

# 18. COPY

Copies files from the build context into the image.

``` dockerfile
COPY app.py /app/
```

Copy everything:

``` dockerfile
COPY . /app/
```

Copy dependencies first:

``` dockerfile
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .
```

This can improve build-cache reuse when application source changes more
often than dependency files.

------------------------------------------------------------------------

# 19. ADD

`ADD` can copy local content and has additional capabilities such as
remote sources and archive handling.

Example:

``` dockerfile
ADD app.tar.gz /app/
```

For ordinary local file copying, prefer `COPY` because its intent is
clearer.

------------------------------------------------------------------------

# 20. WORKDIR

Sets the working directory.

``` dockerfile
WORKDIR /app
```

Then:

``` dockerfile
COPY . .
```

means:

``` text
Copy into /app
```

Explicitly setting `WORKDIR` is a good practice.

------------------------------------------------------------------------

# 21. ENV

Defines environment variables that persist in the image/container
configuration.

``` dockerfile
ENV APP_ENV=production
ENV PORT=8080
```

Use:

``` bash
docker run -e APP_ENV=dev myapp
```

Runtime environment configuration can override image defaults.

------------------------------------------------------------------------

# 22. ARG

Defines build-time variables.

``` dockerfile
ARG APP_VERSION=1.0
```

Build:

``` bash
docker build \
  --build-arg APP_VERSION=2.0 \
  -t myapp:2.0 .
```

### ARG vs ENV

  ARG                        ENV
  -------------------------- --------------------------------------------
  Build-time                 Available in image/container configuration
  Used during build          Used by build/runtime
  Not intended as a secret   Not a secret-management system

Never use `ARG` as a place to hide credentials.

------------------------------------------------------------------------

# 23. CMD

Defines the default command.

``` dockerfile
CMD ["python", "app.py"]
```

A later command passed to `docker run` can replace the default `CMD`.

Example:

``` bash
docker run myapp python test.py
```

------------------------------------------------------------------------

# 24. ENTRYPOINT

Defines the main executable.

``` dockerfile
ENTRYPOINT ["python"]
```

Then:

``` dockerfile
CMD ["app.py"]
```

Running:

``` bash
docker run myapp
```

results conceptually in:

``` text
python app.py
```

### CMD vs ENTRYPOINT

  CMD                          ENTRYPOINT
  ---------------------------- --------------------------
  Default command/arguments    Main executable
  Easier to override           More fixed executable
  Often used with ENTRYPOINT   Can be combined with CMD

------------------------------------------------------------------------

# 25. Shell Form vs Exec Form

Exec form:

``` dockerfile
CMD ["python", "app.py"]
```

Shell form:

``` dockerfile
CMD python app.py
```

For predictable signal handling and process behavior, exec form is
generally preferred for application entrypoints.

------------------------------------------------------------------------

# 26. EXPOSE

Documents the port the application listens on.

``` dockerfile
EXPOSE 8080
```

Important:

`EXPOSE` does **not** publish the port to the host.

Publishing is done with:

``` bash
docker run -p 8080:8080 myapp
```

------------------------------------------------------------------------

# 27. USER

Runs subsequent commands/processes as a specific user.

Example:

``` dockerfile
RUN useradd -r appuser

USER appuser
```

Running applications as a non-root user is a strong container-security
practice when the application supports it.

------------------------------------------------------------------------

# 28. HEALTHCHECK

Defines a health test.

Example:

``` dockerfile
HEALTHCHECK --interval=30s --timeout=5s \
  CMD curl -f http://localhost:8080/health || exit 1
```

Check:

``` bash
docker ps
docker inspect mycontainer
```

------------------------------------------------------------------------

# 29. LABEL

Adds metadata.

``` dockerfile
LABEL maintainer="VishwaTech Labs"
LABEL version="1.0"
LABEL description="Demo application"
```

------------------------------------------------------------------------

# 30. VOLUME

Declares a mount point intended for persistent data.

``` dockerfile
VOLUME ["/data"]
```

For many applications, it is clearer to create and attach volumes
explicitly at deployment time.

------------------------------------------------------------------------

# 31. .dockerignore

`.dockerignore` prevents unnecessary files from being sent as build
context.

Example:

``` text
.git
.github
.gitignore
Dockerfile*
README.md
node_modules
__pycache__
*.pyc
.env
.venv
venv
dist
build
coverage
*.log
```

Never accidentally send secrets or huge directories as build context.

------------------------------------------------------------------------

# 32. Docker Build Context

When you run:

``` bash
docker build -t myapp .
```

the final `.` means the current directory is the build context.

Docker can access files in that context for instructions such as:

``` dockerfile
COPY
ADD
```

`.dockerignore` controls what is excluded.

------------------------------------------------------------------------

# 33. Docker Build

Basic:

``` bash
docker build -t myapp .
```

Specific Dockerfile:

``` bash
docker build -f Dockerfile.dev -t myapp:dev .
```

No-cache build:

``` bash
docker build --no-cache -t myapp .
```

Pass build argument:

``` bash
docker build \
  --build-arg APP_VERSION=1.0 \
  -t myapp:1.0 .
```

------------------------------------------------------------------------

# 34. Image Layers and Build Cache

A Docker image is composed of filesystem layers.

For example:

``` dockerfile
FROM python:3.12-slim
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
```

A good layer strategy is:

``` text
Less frequently changing
        ↓
Dependencies
        ↓
More frequently changing
        ↓
Application source
```

This can improve rebuild performance.

------------------------------------------------------------------------

# 35. Multi-Stage Builds

Multi-stage builds allow separate build and runtime stages.

Example:

``` dockerfile
FROM golang:1.24 AS builder

WORKDIR /src

COPY . .

RUN go build -o app .

FROM alpine:latest

WORKDIR /app

COPY --from=builder /src/app .

USER 10001

CMD ["./app"]
```

Benefits:

-   Smaller runtime image
-   Build tools excluded from runtime
-   Reduced attack surface
-   Cleaner production image

------------------------------------------------------------------------

# 36. Docker Networking

Docker provides networking so containers can communicate.

Common network drivers include:

``` text
bridge
host
none
overlay
macvlan
```

List networks:

``` bash
docker network ls
```

Inspect:

``` bash
docker network inspect bridge
```

Create:

``` bash
docker network create app-network
```

------------------------------------------------------------------------

# 37. Bridge Network

Create:

``` bash
docker network create app-network
```

Run:

``` bash
docker run -d \
  --name backend \
  --network app-network \
  mybackend
```

Run another container:

``` bash
docker run -d \
  --name frontend \
  --network app-network \
  myfrontend
```

Containers on the same user-defined bridge network can communicate using
container/service names.

Example concept:

``` text
frontend
   │
   │ HTTP
   ▼
backend:8080
```

------------------------------------------------------------------------

# 38. Port Mapping

General syntax:

``` bash
docker run -p HOST_PORT:CONTAINER_PORT IMAGE
```

Example:

``` bash
docker run -p 8080:80 nginx
```

IPv4 binding:

``` bash
docker run -p 127.0.0.1:8080:80 nginx
```

Be careful when publishing services publicly.

------------------------------------------------------------------------

# 39. Host Network

Example:

``` bash
docker run --network host nginx
```

The container shares the host's network namespace behavior depending on
platform.

Use it only when the networking trade-offs are understood.

------------------------------------------------------------------------

# 40. Docker Volumes

Containers are ephemeral by default.

If application data must survive container removal, use persistent
storage.

Create:

``` bash
docker volume create appdata
```

List:

``` bash
docker volume ls
```

Inspect:

``` bash
docker volume inspect appdata
```

Run:

``` bash
docker run -d \
  --name db \
  -v appdata:/var/lib/data \
  mydatabase
```

------------------------------------------------------------------------

# 41. Bind Mounts

Bind mount a host directory:

``` bash
docker run \
  -v $(pwd):/app \
  myapp
```

On PowerShell, a common form is:

``` powershell
docker run -v ${PWD}:/app myapp
```

Use bind mounts mainly when the application needs direct access to host
files, such as development workflows.

------------------------------------------------------------------------

# 42. Volumes vs Bind Mounts

  Feature                            Volume             Bind Mount
  ---------------------------------- ------------------ ---------------------
  Managed by Docker                  Yes                No
  Host path required                 No                 Yes
  Good for database data             Yes                Sometimes
  Good for source-code development   Sometimes          Yes
  Portability                        Generally better   More host-dependent

------------------------------------------------------------------------

# 43. Docker Compose

Docker Compose defines and runs multi-container applications using a
YAML configuration.

Typical application:

``` text
Frontend
Backend
Database
Cache
```

Example:

``` yaml
services:
  backend:
    build: .
    ports:
      - "8080:8080"

  db:
    image: postgres:17
    environment:
      POSTGRES_PASSWORD: example
```

Start:

``` bash
docker compose up -d
```

Stop:

``` bash
docker compose down
```

List:

``` bash
docker compose ps
```

Logs:

``` bash
docker compose logs -f
```

Build:

``` bash
docker compose build
```

Rebuild and start:

``` bash
docker compose up -d --build
```

------------------------------------------------------------------------

# 44. Compose Example --- Three-Tier Application

``` yaml
services:

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend

  backend:
    build: ./backend
    environment:
      DB_HOST: db
      DB_PORT: 5432
      DB_NAME: appdb
      DB_USER: appuser
      DB_PASSWORD: example
    depends_on:
      - db

  db:
    image: postgres:17
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: example
    volumes:
      - dbdata:/var/lib/postgresql/data

volumes:
  dbdata:
```

For real production systems, use proper secret management instead of
putting real passwords directly in source-controlled Compose files.

------------------------------------------------------------------------

# 45. Compose Commands

Start:

``` bash
docker compose up
```

Detached:

``` bash
docker compose up -d
```

Build:

``` bash
docker compose build
```

Build without cache:

``` bash
docker compose build --no-cache
```

Stop:

``` bash
docker compose stop
```

Remove resources:

``` bash
docker compose down
```

Remove volumes too:

``` bash
docker compose down -v
```

View services:

``` bash
docker compose ps
```

Logs:

``` bash
docker compose logs
```

Follow logs:

``` bash
docker compose logs -f
```

Execute command:

``` bash
docker compose exec backend sh
```

------------------------------------------------------------------------

# 46. Docker Registry

A registry stores container images.

Workflow:

``` text
Dockerfile
   ↓
docker build
   ↓
Docker Image
   ↓
docker tag
   ↓
docker push
   ↓
Container Registry
```

Login:

``` bash
docker login
```

Tag:

``` bash
docker tag myapp:1.0 username/myapp:1.0
```

Push:

``` bash
docker push username/myapp:1.0
```

Pull:

``` bash
docker pull username/myapp:1.0
```

------------------------------------------------------------------------

# 47. Docker Hub Workflow

``` bash
docker build -t myapp:1.0 .
docker tag myapp:1.0 <dockerhub-user>/myapp:1.0
docker login
docker push <dockerhub-user>/myapp:1.0
```

Never put registry passwords directly in shell history, Dockerfiles, or
Git repositories.

------------------------------------------------------------------------

# 48. AWS ECR Example

Typical workflow:

``` text
Developer
   ↓
Docker Build
   ↓
AWS ECR
   ↓
Kubernetes / ECS / EC2
```

Authenticate using AWS-supported ECR authentication:

``` bash
aws ecr get-login-password --region <region> \
  | docker login \
  --username AWS \
  --password-stdin <account>.dkr.ecr.<region>.amazonaws.com
```

Tag:

``` bash
docker tag myapp:1.0 \
  <account>.dkr.ecr.<region>.amazonaws.com/myapp:1.0
```

Push:

``` bash
docker push \
  <account>.dkr.ecr.<region>.amazonaws.com/myapp:1.0
```

------------------------------------------------------------------------

# 49. Docker Security

Important principles:

## 1. Use trusted base images

Avoid unknown images.

## 2. Pin versions

Prefer:

``` dockerfile
FROM python:3.12-slim
```

with an appropriate controlled versioning strategy rather than blindly
using:

``` dockerfile
FROM python:latest
```

For highly controlled builds, pin image digests.

## 3. Run as non-root

``` dockerfile
USER appuser
```

## 4. Do not store secrets in images

Never:

``` dockerfile
ENV PASSWORD=secret123
```

Never:

``` dockerfile
COPY .env /app/.env
```

## 5. Use `.dockerignore`

Exclude:

``` text
.env
.git
*.pem
*.key
credentials
```

## 6. Scan images

Use an appropriate image vulnerability scanner such as Docker Scout or
another approved security scanner.

## 7. Keep images small

Use slim/minimal runtime images where appropriate.

------------------------------------------------------------------------

# 50. Docker Secrets

Secrets should be handled separately from normal application
configuration.

Possible approaches include:

-   Docker Compose secrets
-   CI/CD secret stores
-   Cloud secret managers
-   Kubernetes Secrets
-   External secret-management platforms

Never commit production secrets to Git.

------------------------------------------------------------------------

# 51. Resource Limits

Containers can be constrained.

Example:

``` bash
docker run \
  --memory="512m" \
  --cpus="1.0" \
  nginx
```

Useful concepts:

``` text
CPU
Memory
PIDs
Disk
Network
```

Resource controls help prevent one workload from consuming excessive
host resources.

------------------------------------------------------------------------

# 52. Restart Policies

Examples:

``` bash
docker run --restart=no nginx
```

``` bash
docker run --restart=on-failure nginx
```

``` bash
docker run --restart=unless-stopped nginx
```

``` bash
docker run --restart=always nginx
```

Choose policies according to the application's operational requirements.

------------------------------------------------------------------------

# 53. Docker Logging

Basic:

``` bash
docker logs container_name
```

Follow:

``` bash
docker logs -f container_name
```

Production environments should have an intentional centralized logging
strategy.

Typical architecture:

``` text
Container
   ↓
Docker logging
   ↓
Log collector
   ↓
Central logging platform
   ↓
Search / Alert / Dashboard
```

------------------------------------------------------------------------

# 54. Docker Health and Observability

Useful commands:

``` bash
docker stats
docker top container
docker inspect container
docker logs container
```

`docker stats` provides live resource usage information.

------------------------------------------------------------------------

# 55. Docker Cleanup

List stopped containers:

``` bash
docker ps -a
```

Remove stopped containers:

``` bash
docker container prune
```

Remove unused images:

``` bash
docker image prune
```

Remove unused networks:

``` bash
docker network prune
```

Remove unused volumes:

``` bash
docker volume prune
```

General cleanup:

``` bash
docker system prune
```

More aggressive cleanup:

``` bash
docker system prune -a
```

Be careful: prune commands delete unused Docker resources.

------------------------------------------------------------------------

# 56. Useful Docker Commands Cheat Sheet

## Images

``` bash
docker images
docker pull IMAGE
docker build -t NAME:TAG .
docker image inspect IMAGE
docker history IMAGE
docker rmi IMAGE
```

## Containers

``` bash
docker ps
docker ps -a
docker run IMAGE
docker start CONTAINER
docker stop CONTAINER
docker restart CONTAINER
docker rm CONTAINER
docker exec -it CONTAINER sh
docker logs CONTAINER
docker inspect CONTAINER
```

## Networks

``` bash
docker network ls
docker network create NETWORK
docker network inspect NETWORK
docker network connect NETWORK CONTAINER
docker network disconnect NETWORK CONTAINER
docker network rm NETWORK
```

## Volumes

``` bash
docker volume ls
docker volume create VOLUME
docker volume inspect VOLUME
docker volume rm VOLUME
```

## Compose

``` bash
docker compose up -d
docker compose down
docker compose ps
docker compose logs -f
docker compose build
docker compose exec SERVICE sh
```

------------------------------------------------------------------------

# 57. Docker Troubleshooting

## Container exits immediately

Check:

``` bash
docker ps -a
docker logs <container>
```

Inspect:

``` bash
docker inspect <container>
```

Common causes:

-   Application process exited
-   Incorrect CMD
-   Incorrect ENTRYPOINT
-   Missing environment variable
-   Configuration error

------------------------------------------------------------------------

## Port is not accessible

Check:

``` bash
docker ps
```

Verify port mapping:

``` bash
docker port <container>
```

Example:

``` bash
docker run -p 8080:80 nginx
```

Then test:

``` text
http://localhost:8080
```

------------------------------------------------------------------------

## Container cannot reach another container

Check:

``` bash
docker network ls
docker network inspect <network>
```

Make sure both containers are attached to the correct user-defined
network.

Use the service/container name rather than assuming `localhost` means
another container.

------------------------------------------------------------------------

## Image build is unexpectedly slow

Check:

-   Build context size
-   `.dockerignore`
-   Dockerfile layer order
-   Dependency caching
-   Unnecessary downloads

Example:

``` text
COPY dependency-file
RUN install dependencies
COPY source-code
```

instead of copying the entire application before dependency
installation.

------------------------------------------------------------------------

## Permission denied

Check:

``` bash
docker exec -it <container> id
docker inspect <container>
```

Review:

-   Container user
-   File ownership
-   Mounted directory permissions
-   Host filesystem permissions

------------------------------------------------------------------------

# 58. Docker Development Workflow

``` text
Developer
    ↓
Write Code
    ↓
Dockerfile
    ↓
docker build
    ↓
Image
    ↓
docker run
    ↓
Test
    ↓
docker compose
    ↓
Integration Test
    ↓
Registry
```

------------------------------------------------------------------------

# 59. Docker + GitHub Actions

Typical CI/CD:

``` text
Git Push
   ↓
GitHub
   ↓
GitHub Actions
   ↓
Checkout
   ↓
Test
   ↓
Docker Build
   ↓
Image Scan
   ↓
Docker Push
   ↓
Registry
   ↓
Deployment
```

Example workflow:

``` yaml
name: Docker CI

on:
  push:
    branches:
      - main

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Build image
        run: docker build -t myapp:${{ github.sha }} .

      - name: Test
        run: docker run --rm myapp:${{ github.sha }} <test-command>
```

For registry publishing, configure authentication through GitHub Actions
secrets or appropriate identity federation.

------------------------------------------------------------------------

# 60. Docker + Terraform + Ansible + Kubernetes

This is the complete DevOps relationship:

``` text
Terraform
    │
    │ Infrastructure
    ▼
Cloud Infrastructure
    │
    ▼
Ansible
    │
    │ Configuration
    ▼
Servers / Nodes
    │
    ▼
Docker
    │
    │ Containerization
    ▼
Container Image
    │
    ▼
Registry
    │
    ▼
Kubernetes
    │
    │ Orchestration
    ▼
Production Application
```

### Responsibilities

  Tool             Primary responsibility
  ---------------- ----------------------------
  GitHub           Source control
  GitHub Actions   CI/CD automation
  Docker           Containerization
  Registry         Image storage/distribution
  Terraform        Infrastructure as Code
  Ansible          Configuration management
  Kubernetes       Container orchestration

------------------------------------------------------------------------

# 61. Docker and Kubernetes Relationship

Docker packages the application.

Kubernetes manages containerized workloads.

``` text
Docker
  ↓
Build Image
  ↓
Registry
  ↓
Kubernetes
  ↓
Pod
  ↓
Container
```

Important teaching point:

**Docker and Kubernetes are not competitors.**

Docker is commonly used to build/package containers, while Kubernetes
orchestrates container workloads.

------------------------------------------------------------------------

# 62. Production Dockerfile Example --- Python

``` dockerfile
# syntax=docker/dockerfile:1

FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN useradd --create-home appuser

USER appuser

EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=5s \
  CMD python -c "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/health')" || exit 1

CMD ["python", "app.py"]
```

Adapt the health check and startup command to the actual application.

------------------------------------------------------------------------

# 63. Production Dockerfile Example --- Node.js

``` dockerfile
# syntax=docker/dockerfile:1

FROM node:22-alpine AS build

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build

FROM node:22-alpine

WORKDIR /app

ENV NODE_ENV=production

COPY package*.json ./

RUN npm ci --omit=dev

COPY --from=build /app/dist ./dist

USER node

EXPOSE 3000

CMD ["node", "dist/server.js"]
```

------------------------------------------------------------------------

# 64. Docker Project Structure

Recommended structure:

``` text
my-docker-project/
│
├── app/
│   ├── app.py
│   └── requirements.txt
│
├── Dockerfile
├── .dockerignore
├── compose.yaml
├── README.md
│
└── .github/
    └── workflows/
        └── docker.yml
```

------------------------------------------------------------------------

# 65. Recommended Docker Lab Sequence

### Lab 1

Install Docker.

### Lab 2

Run `hello-world`.

### Lab 3

Run Nginx.

### Lab 4

Publish ports.

### Lab 5

Create custom Dockerfile.

### Lab 6

Build Python application image.

### Lab 7

Build Node.js application image.

### Lab 8

Use environment variables.

### Lab 9

Create custom Docker network.

### Lab 10

Use volumes.

### Lab 11

Build multi-container application.

### Lab 12

Docker Compose.

### Lab 13

Push image to Docker Hub.

### Lab 14

Push image to AWS ECR.

### Lab 15

Create multi-stage Dockerfile.

### Lab 16

Add health checks.

### Lab 17

Scan image for vulnerabilities.

### Lab 18

Build Docker image with GitHub Actions.

### Lab 19

Push image automatically from GitHub Actions.

### Lab 20

Deploy the image to Kubernetes.

------------------------------------------------------------------------

# 66. Final Real-Time Project

## Project: Production DevOps Application

Build:

``` text
                    GitHub
                       │
                       ▼
               GitHub Actions
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
       Test                    Docker Build
                                    │
                                    ▼
                              Image Registry
                                    │
                                    ▼
                              Kubernetes
                                    │
                         ┌──────────┼──────────┐
                         ▼          ▼          ▼
                        Pod        Pod        Pod
                         │          │          │
                         └──────────┼──────────┘
                                    ▼
                                  Service
                                    │
                                    ▼
                                  Ingress
                                    │
                                    ▼
                                  User
```

Infrastructure:

``` text
Terraform
   ↓
Cloud
   ↓
Network
   ↓
Compute / Kubernetes
```

Configuration:

``` text
Ansible
   ↓
Server configuration
   ↓
Docker / supporting services
```

------------------------------------------------------------------------

# 67. Interview Questions

## Beginner

1.  What is Docker?
2.  What is a container?
3.  What is an image?
4.  What is Dockerfile?
5.  What is Docker Hub?
6.  What is Docker Engine?
7.  What is the difference between image and container?
8.  What does `docker run` do?
9.  What does `-p` mean?
10. What is `.dockerignore`?

## Intermediate

11. CMD vs ENTRYPOINT?
12. COPY vs ADD?
13. ARG vs ENV?
14. What are Docker layers?
15. What is Docker build cache?
16. What is a multi-stage build?
17. What are Docker networks?
18. Bridge vs host networking?
19. Volume vs bind mount?
20. What is Docker Compose?

## Advanced

21. How do you reduce image size?
22. How do you secure Docker images?
23. Why should containers avoid running as root?
24. How do you handle secrets?
25. How do you troubleshoot a container that exits?
26. How do containers communicate?
27. How does Docker integrate with CI/CD?
28. How do you push images to ECR?
29. Docker vs Kubernetes?
30. Explain a production Docker CI/CD architecture.

------------------------------------------------------------------------

# 68. Docker Best Practices

``` text
1. Use trusted base images
2. Prefer minimal runtime images where appropriate
3. Pin versions appropriately
4. Use multi-stage builds
5. Use .dockerignore
6. Keep images small
7. Run as non-root
8. Do not store secrets in images
9. Scan images
10. Add health checks where useful
11. Use meaningful image tags
12. Prefer immutable versioning for deployments
13. Avoid unnecessary packages
14. Order Dockerfile instructions for effective caching
15. Log to stdout/stderr
16. Apply resource limits where appropriate
17. Keep containers focused on a primary application process
18. Regularly update dependencies and base images
19. Test images before publishing
20. Integrate image builds into CI/CD
```

------------------------------------------------------------------------

# 69. Golden Docker Workflow

``` text
                 SOURCE CODE
                      │
                      ▼
                   GitHub
                      │
                      ▼
                GitHub Actions
                      │
             ┌────────┴────────┐
             ▼                 ▼
           TEST             BUILD
                               │
                               ▼
                         Docker Image
                               │
                               ▼
                          Security Scan
                               │
                               ▼
                         Image Registry
                               │
                               ▼
                          Deployment
                               │
                    ┌──────────┴──────────┐
                    ▼                     ▼
               Kubernetes               ECS
```

------------------------------------------------------------------------

# 70. Quick Reference

### Build

``` bash
docker build -t myapp:1.0 .
```

### Run

``` bash
docker run -d --name myapp -p 8080:8080 myapp:1.0
```

### List

``` bash
docker ps
docker images
```

### Logs

``` bash
docker logs -f myapp
```

### Shell

``` bash
docker exec -it myapp sh
```

### Stop

``` bash
docker stop myapp
```

### Remove

``` bash
docker rm myapp
```

### Network

``` bash
docker network create appnet
```

### Volume

``` bash
docker volume create appdata
```

### Compose

``` bash
docker compose up -d
docker compose down
```

### Push

``` bash
docker tag myapp:1.0 USER/myapp:1.0
docker push USER/myapp:1.0
```

------------------------------------------------------------------------

# 71. Official Docker References

-   Docker Documentation: https://docs.docker.com/
-   Docker Engine: https://docs.docker.com/engine/
-   Docker CLI Reference: https://docs.docker.com/reference/cli/docker/
-   Dockerfile Reference: https://docs.docker.com/reference/dockerfile/
-   Docker Compose: https://docs.docker.com/compose/
-   Compose File Reference:
    https://docs.docker.com/reference/compose-file/
-   Docker Hub: https://hub.docker.com/

------------------------------------------------------------------------

# 72. VishwaTech Labs Learning Path

``` text
                 DOCKER
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
      Images    Containers   Networks
        │           │           │
        └───────────┼───────────┘
                    ▼
                 Volumes
                    │
                    ▼
               Dockerfile
                    │
                    ▼
             Docker Compose
                    │
                    ▼
                Registry
                    │
                    ▼
              GitHub Actions
                    │
                    ▼
               Terraform
                    │
                    ▼
                Ansible
                    │
                    ▼
               Kubernetes
                    │
                    ▼
             PRODUCTION 🚀
```

## Course Goal

By completing this documentation and labs, a student should be able to:

> **Build, package, run, network, persist, secure, publish, automate,
> and deploy containerized applications using Docker and modern DevOps
> tooling.**

------------------------------------------------------------------------

### Author / Training Brand

**VishwaTech Labs By Vishwanath Gowda H**

Docker • DevOps • Cloud • GitHub Actions • Terraform • Ansible •
Kubernetes • Security
