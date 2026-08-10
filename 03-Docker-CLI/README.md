<div align="center">

# 🐳 Docker CLI — Complete Command Masterclass

### 🚀 Zero to Hero | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-CLI-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/reference/cli/docker/)
[![Commands](https://img.shields.io/badge/Commands-Hands--On-orange)](#-docker-cli-command-map)
[![Labs](https://img.shields.io/badge/Labs-20+-success)](#-hands-on-labs)
[![DevOps](https://img.shields.io/badge/Track-DevOps-purple)](#)
[![VishwaTech Labs](https://img.shields.io/badge/Training-VishwaTech%20Labs-black)](#)

**Master the Docker CLI — from your first `docker version` to real-world container operations.**

[📘 Docker CLI Reference](https://docs.docker.com/reference/cli/docker/) •
[🐳 Docker Run](https://docs.docker.com/reference/cli/docker/container/run/) •
[📦 Docker Image](https://docs.docker.com/reference/cli/docker/image/) •
[🌐 Docker Network](https://docs.docker.com/reference/cli/docker/network/) •
[💾 Docker Volume](https://docs.docker.com/reference/cli/docker/volume/)

</div>

---

# 🎯 What You Will Learn

This module turns Docker concepts into practical command-line skills.

```text
Docker CLI
    │
    ├── System
    ├── Images
    ├── Containers
    ├── Networks
    ├── Volumes
    ├── Registry
    ├── Build
    ├── Compose
    ├── Logs
    ├── Inspect
    ├── Resource Monitoring
    └── Cleanup
```

By the end, you should confidently understand:

- Docker CLI architecture
- Command syntax
- Images
- Containers
- Port publishing
- Environment variables
- Logs
- Exec
- Inspect
- Networks
- Volumes
- Image tagging
- Registry operations
- Container cleanup
- Resource monitoring
- Troubleshooting

---

# 🧠 1. Docker CLI Architecture

When you execute:

```bash
docker ps
```

the flow is conceptually:

```text
👨‍💻 You
  │
  │ docker ps
  ▼
┌─────────────────┐
│   Docker CLI    │
└────────┬────────┘
         │
      Docker API
         │
         ▼
┌─────────────────┐
│ Docker Daemon   │
│    dockerd      │
└────────┬────────┘
         │
         ▼
      Containers
```

The CLI is the client interface used to communicate with the Docker daemon/API. The daemon performs the actual management operations. 

Official reference: [Docker CLI](https://docs.docker.com/reference/cli/docker/)

---

# 🧩 2. Docker Command Structure

Modern Docker CLI commands follow a logical hierarchy:

```text
docker
   │
   ├── container
   ├── image
   ├── network
   ├── volume
   ├── system
   ├── build
   ├── compose
   ├── login
   ├── push
   ├── pull
   └── version
```

Example:

```bash
docker container ls
```

Short form:

```bash
docker ps
```

Example:

```bash
docker image ls
```

Short form:

```bash
docker images
```

For teaching, learn both forms, but prefer the modern object-oriented command structure when documenting automation and advanced workflows.

---

# 🔥 3. Docker CLI Command Map

## System

```bash
docker version
docker info
docker context ls
docker system df
```

## Images

```bash
docker image ls
docker pull
docker build
docker tag
docker inspect
docker history
docker rmi
```

## Containers

```bash
docker run
docker create
docker start
docker stop
docker restart
docker pause
docker unpause
docker kill
docker rm
docker ps
docker logs
docker exec
docker inspect
docker cp
docker top
docker stats
```

## Networks

```bash
docker network ls
docker network create
docker network inspect
docker network connect
docker network disconnect
docker network rm
```

## Volumes

```bash
docker volume ls
docker volume create
docker volume inspect
docker volume rm
```

## Registry

```bash
docker login
docker logout
docker pull
docker push
docker tag
```

## Cleanup

```bash
docker container prune
docker image prune
docker network prune
docker volume prune
docker system prune
```

---

# 🟢 4. `docker version`

Check Docker client and server information:

```bash
docker version
```

Useful for confirming:

```text
Client
Server
API Version
OS/Architecture
Docker Engine version
```

Short:

```bash
docker --version
```

### Interview Question

**Q: Why might `docker version` show client information but fail on server information?**

Possible reason:

```text
Docker CLI
    ↓
Cannot connect
    ↓
Docker daemon unavailable
```

---

# 🔎 5. `docker info`

Run:

```bash
docker info
```

It provides detailed information about the Docker environment.

Useful areas include:

- Containers
- Images
- Storage driver
- Plugins
- Docker root directory
- CPUs
- Memory
- Operating system
- Architecture
- Server configuration

---

# 🆘 6. `docker --help`

Get top-level help:

```bash
docker --help
```

Command-specific help:

```bash
docker run --help
```

```bash
docker container --help
```

```bash
docker network --help
```

This is one of the most important habits for a DevOps engineer:

> **When you don't remember an option, ask the CLI.**

---

# 📦 7. `docker pull`

Download an image from a registry:

```bash
docker pull nginx
```

Specific version:

```bash
docker pull nginx:1.27
```

Python:

```bash
docker pull python:3.12-slim
```

PostgreSQL:

```bash
docker pull postgres:17
```

Workflow:

```text
Registry
   │
   │ docker pull
   ▼
Local Docker Image Store
```

---

# 🖼️ 8. `docker image ls`

List local images:

```bash
docker image ls
```

Traditional shorthand:

```bash
docker images
```

Typical columns:

```text
REPOSITORY
TAG
IMAGE ID
CREATED
SIZE
```

Filter:

```bash
docker image ls nginx
```

---

# 🔍 9. `docker image inspect`

Inspect an image:

```bash
docker image inspect nginx
```

Useful for understanding:

- Architecture
- Environment
- Entrypoint
- CMD
- Layers
- Metadata
- Configuration

For readable output with tools such as `jq`:

```bash
docker image inspect nginx | jq
```

---

# 🧱 10. `docker history`

Show image layers/history:

```bash
docker history nginx
```

This helps students understand:

```text
Dockerfile instruction
        ↓
Image layer
        ↓
Final image
```

---

# 🏗️ 11. `docker build`

Build an image from a Dockerfile:

```bash
docker build -t myapp:1.0 .
```

Break it down:

```text
docker build
     │
     ├── -t myapp:1.0
     │       Tag
     │
     └── .
          Build context
```

Another example:

```bash
docker build -t vishwatech-app:v1 .
```

---

# 🏷️ 12. Image Tags

Tag an image:

```bash
docker tag myapp:1.0 myrepo/myapp:1.0
```

Common versioning:

```text
myapp:1.0
myapp:1.1
myapp:2.0
myapp:v1.0.0
myapp:2026-08-10
```

For production deployments, avoid depending only on:

```text
latest
```

Prefer controlled version tags and, where appropriate, immutable image digests.

---

# 🗑️ 13. `docker rmi`

Remove an image:

```bash
docker rmi nginx
```

Specific tag:

```bash
docker rmi nginx:1.27
```

If a container still depends on an image, Docker may prevent removal until the dependency is handled.

---

# 🐳 14. `docker run`

This is one of the most important Docker commands.

Basic:

```bash
docker run nginx
```

Detached:

```bash
docker run -d nginx
```

Named:

```bash
docker run -d --name web nginx
```

Port:

```bash
docker run -d --name web -p 8080:80 nginx
```

Environment variable:

```bash
docker run -d \
  --name web \
  -e APP_ENV=production \
  nginx
```

---

# 🧠 15. Understand `docker run`

Command:

```bash
docker run -d --name web -p 8080:80 nginx
```

Means:

| Part | Meaning |
|---|---|
| `docker` | Docker CLI |
| `run` | Create and start a container |
| `-d` | Detached mode |
| `--name web` | Container name |
| `-p 8080:80` | Host port → container port |
| `nginx` | Image |

---

# 🏃 16. `docker ps`

Running containers:

```bash
docker ps
```

All containers:

```bash
docker ps -a
```

Latest created container:

```bash
docker ps -l
```

Show IDs only:

```bash
docker ps -q
```

Useful combination:

```bash
docker ps -a
```

---

# 🛑 17. `docker stop`

Stop gracefully:

```bash
docker stop web
```

Multiple:

```bash
docker stop web api db
```

General flow:

```text
RUNNING
   │
   │ docker stop
   ▼
STOPPED
```

---

# ▶️ 18. `docker start`

Start a stopped container:

```bash
docker start web
```

Attach:

```bash
docker start -a web
```

Interactive:

```bash
docker start -ai web
```

---

# 🔄 19. `docker restart`

Restart:

```bash
docker restart web
```

With timeout:

```bash
docker restart -t 10 web
```

---

# ⏸️ 20. `docker pause` / `unpause`

Pause:

```bash
docker pause web
```

Resume:

```bash
docker unpause web
```

Useful when demonstrating container process suspension.

---

# 💀 21. `docker kill`

Forcefully terminate a container:

```bash
docker kill web
```

Difference:

```text
docker stop
   ↓
Graceful shutdown

docker kill
   ↓
Immediate termination signal
```

Use `kill` when a container is not responding or when immediate termination is specifically required.

---

# 🗑️ 22. `docker rm`

Remove a stopped container:

```bash
docker rm web
```

Force remove:

```bash
docker rm -f web
```

Multiple:

```bash
docker rm web api db
```

Important:

```text
docker stop ≠ docker rm
```

Stopping does not remove the container.

---

# 📜 23. `docker logs`

View logs:

```bash
docker logs web
```

Follow:

```bash
docker logs -f web
```

Last 100 lines:

```bash
docker logs --tail 100 web
```

With timestamps:

```bash
docker logs -t web
```

Since a specific time:

```bash
docker logs --since 10m web
```

---

# 🖥️ 24. `docker exec`

Execute a command inside a running container:

```bash
docker exec web ls
```

Open shell:

```bash
docker exec -it web sh
```

If Bash exists:

```bash
docker exec -it web bash
```

Check environment:

```bash
docker exec web env
```

Check process:

```bash
docker exec web ps
```

---

# 🔬 25. `docker inspect`

Inspect a container:

```bash
docker inspect web
```

Useful for:

```text
Network
Mounts
Environment
Ports
State
IP address
Image
Runtime configuration
```

Example:

```bash
docker inspect web
```

---

# 📋 26. `docker top`

Show processes inside a container:

```bash
docker top web
```

Useful for troubleshooting:

```text
Container
    ↓
docker top
    ↓
Application processes
```

---

# 📊 27. `docker stats`

Live resource usage:

```bash
docker stats
```

Specific container:

```bash
docker stats web
```

Useful metrics include:

```text
CPU %
Memory usage
Memory %
Network I/O
Block I/O
PIDs
```

---

# 📁 28. `docker cp`

Copy files between host and container.

Host → container:

```bash
docker cp app.conf web:/etc/app/app.conf
```

Container → host:

```bash
docker cp web:/var/log/app.log .
```

Use carefully in production; immutable image/configuration patterns are usually preferable to manually modifying running containers.

---

# 🌐 29. Docker Networks

List:

```bash
docker network ls
```

Create:

```bash
docker network create app-network
```

Inspect:

```bash
docker network inspect app-network
```

Connect:

```bash
docker network connect app-network web
```

Disconnect:

```bash
docker network disconnect app-network web
```

Remove:

```bash
docker network rm app-network
```

---

# 🔌 30. Port Publishing

Basic:

```bash
docker run -p 8080:80 nginx
```

Bind to localhost:

```bash
docker run -p 127.0.0.1:8080:80 nginx
```

Meaning:

```text
127.0.0.1:8080
       │
       ▼
Container:80
```

Binding to localhost can be useful when a service should not be directly exposed on all host interfaces.

---

# 🌍 31. Environment Variables

Pass one:

```bash
docker run -e APP_ENV=dev myapp
```

Multiple:

```bash
docker run \
  -e APP_ENV=production \
  -e PORT=8080 \
  myapp
```

Read inside:

```bash
docker exec myapp env
```

Using an environment file:

```bash
docker run --env-file .env myapp
```

> 🔐 Never commit real production secrets to `.env` files in Git.

---

# 💾 32. Docker Volumes

List:

```bash
docker volume ls
```

Create:

```bash
docker volume create appdata
```

Inspect:

```bash
docker volume inspect appdata
```

Run with volume:

```bash
docker run \
  --name db \
  -v appdata:/data \
  mydatabase
```

Remove:

```bash
docker volume rm appdata
```

---

# 🗂️ 33. Bind Mounts

Linux/macOS:

```bash
docker run \
  -v "$(pwd):/app" \
  myapp
```

PowerShell:

```powershell
docker run -v ${PWD}:/app myapp
```

Concept:

```text
Host Directory
      │
      │ Bind Mount
      ▼
Container Directory
```

---

# 🔐 34. Read-Only Mounts

Example:

```bash
docker run \
  -v "$(pwd)/config:/app/config:ro" \
  myapp
```

`ro` means:

```text
Read Only
```

This can reduce accidental modification of mounted configuration.

---

# 🧹 35. Cleanup Commands

### Remove stopped containers

```bash
docker container prune
```

### Remove unused images

```bash
docker image prune
```

### Remove unused networks

```bash
docker network prune
```

### Remove unused volumes

```bash
docker volume prune
```

### General cleanup

```bash
docker system prune
```

### More aggressive cleanup

```bash
docker system prune -a
```

> ⚠️ **Important:** prune commands can permanently delete unused Docker resources. Always understand what will be removed before running them.

---

# 📊 36. `docker system df`

Check Docker disk usage:

```bash
docker system df
```

Verbose:

```bash
docker system df -v
```

Useful before cleanup:

```text
docker system df
       ↓
Understand usage
       ↓
Identify unused resources
       ↓
Prune carefully
```

---

# 🔐 37. Registry Login

Login:

```bash
docker login
```

Login to a specific registry:

```bash
docker login registry.example.com
```

Logout:

```bash
docker logout
```

Use secure authentication methods supported by the registry. Never put passwords directly into Dockerfiles or Git repositories.

---

# 📤 38. Push an Image

Build:

```bash
docker build -t myapp:1.0 .
```

Tag:

```bash
docker tag myapp:1.0 USERNAME/myapp:1.0
```

Login:

```bash
docker login
```

Push:

```bash
docker push USERNAME/myapp:1.0
```

Workflow:

```text
Dockerfile
    ↓
docker build
    ↓
Local Image
    ↓
docker tag
    ↓
docker push
    ↓
Registry
```

---

# 📥 39. Pull an Image

```bash
docker pull USERNAME/myapp:1.0
```

Run:

```bash
docker run USERNAME/myapp:1.0
```

---

# 🧭 40. Docker Context

Contexts allow the Docker CLI to work with different Docker endpoints.

List:

```bash
docker context ls
```

Show current:

```bash
docker context show
```

Inspect:

```bash
docker context inspect
```

Switch:

```bash
docker context use <context-name>
```

This becomes useful when working with multiple Docker environments.

---

# 🧪 41. Real-Time Scenario — Debugging a Broken Container

Suppose:

```bash
docker ps
```

does not show your application.

First:

```bash
docker ps -a
```

Then:

```bash
docker logs myapp
```

Then:

```bash
docker inspect myapp
```

Then check:

```bash
docker image inspect myimage
```

Then verify configuration:

```bash
docker exec -it myapp sh
```

Diagnostic flow:

```text
Container missing?
       ↓
docker ps -a
       ↓
Exited?
       ↓
docker logs
       ↓
Configuration?
       ↓
docker inspect
       ↓
Need shell?
       ↓
docker exec
```

---

# 🚨 42. Common Docker Errors

## Error: Port already allocated

Example:

```text
Bind for 0.0.0.0:8080 failed
```

Check what is using the port.

Then choose another host port:

```bash
docker run -p 8081:80 nginx
```

---

## Error: Container name already in use

Example:

```text
Conflict. The container name is already in use
```

Check:

```bash
docker ps -a
```

Remove or rename the existing container.

```bash
docker rm <container>
```

---

## Error: Image not found

Check:

```bash
docker image ls
```

Pull:

```bash
docker pull <image>
```

---

## Error: Container exits immediately

Run:

```bash
docker ps -a
docker logs <container>
```

Check:

```text
CMD
ENTRYPOINT
Environment
Configuration
Application process
```

---

# 🏗️ 43. Real-Time Three-Tier Example

```text
                🌐 Browser
                    │
                    ▼
              Frontend :3000
                    │
                    ▼
              Backend :8080
                    │
                    ▼
              PostgreSQL :5432
```

Create network:

```bash
docker network create app-network
```

Run database:

```bash
docker run -d \
  --name db \
  --network app-network \
  postgres:17
```

Run backend:

```bash
docker run -d \
  --name backend \
  --network app-network \
  mybackend:1.0
```

Run frontend:

```bash
docker run -d \
  --name frontend \
  --network app-network \
  -p 3000:3000 \
  myfrontend:1.0
```

Concept:

```text
frontend
    │
    │ app-network
    ▼
backend
    │
    │ app-network
    ▼
database
```

---

# 🧪 44. Hands-On Labs

## Lab 01 — Docker Version

```bash
docker version
docker info
```

---

## Lab 02 — Hello World

```bash
docker run hello-world
docker ps -a
```

---

## Lab 03 — Pull Images

```bash
docker pull nginx
docker pull python:3.12-slim
docker image ls
```

---

## Lab 04 — Run Nginx

```bash
docker run -d --name nginx1 -p 8080:80 nginx
docker ps
```

---

## Lab 05 — Container Lifecycle

```bash
docker stop nginx1
docker start nginx1
docker restart nginx1
docker rm -f nginx1
```

---

## Lab 06 — Logs

```bash
docker run -d --name nginx1 -p 8080:80 nginx
docker logs nginx1
docker logs -f nginx1
```

---

## Lab 07 — Exec

```bash
docker exec -it nginx1 sh
```

Inside:

```bash
ls
env
```

Exit:

```bash
exit
```

---

## Lab 08 — Inspect

```bash
docker inspect nginx1
```

---

## Lab 09 — Environment Variables

```bash
docker run \
  --name env-demo \
  -e APP_ENV=development \
  -e APP_VERSION=1.0 \
  nginx
```

---

## Lab 10 — Custom Network

```bash
docker network create app-network
docker run -d --name web --network app-network nginx
docker network inspect app-network
```

---

## Lab 11 — Volume

```bash
docker volume create appdata
docker run -d --name volume-demo -v appdata:/data nginx
docker volume inspect appdata
```

---

## Lab 12 — Bind Mount

```bash
docker run \
  -d \
  --name bind-demo \
  -v "$(pwd):/app" \
  nginx
```

---

## Lab 13 — Image Build

Create:

```text
Dockerfile
```

Then:

```bash
docker build -t myapp:1.0 .
```

---

## Lab 14 — Tag and Push

```bash
docker tag myapp:1.0 USERNAME/myapp:1.0
docker login
docker push USERNAME/myapp:1.0
```

---

## Lab 15 — Resource Monitoring

```bash
docker stats
```

---

## Lab 16 — Disk Usage

```bash
docker system df
```

---

## Lab 17 — Cleanup

```bash
docker container prune
docker image prune
docker network prune
```

---

## Lab 18 — Debug a Broken Container

Create a container with an invalid command:

```bash
docker run --name broken nginx invalid-command
```

Then diagnose:

```bash
docker ps -a
docker logs broken
docker inspect broken
```

---

## Lab 19 — Three-Tier Network

Create:

```text
frontend
backend
database
```

Attach all three to:

```text
app-network
```

Demonstrate communication.

---

## Lab 20 — Docker CLI Challenge

Without copying commands:

```text
1. Pull nginx
2. Run it
3. Name it
4. Publish port 8080
5. Check it
6. Read logs
7. Enter the container
8. Inspect it
9. Stop it
10. Start it
11. Restart it
12. Remove it
13. Remove the image
```

---

# 🎓 45. Interview Questions

## Beginner

1. What is Docker CLI?
2. What is the Docker daemon?
3. What does `docker run` do?
4. What is the difference between `docker ps` and `docker ps -a`?
5. What is `docker pull`?
6. What is `docker push`?
7. What is `docker build`?
8. What is `docker tag`?
9. What is `docker logs`?
10. What is `docker exec`?

## Intermediate

11. `docker stop` vs `docker kill`?
12. `docker stop` vs `docker rm`?
13. `docker start` vs `docker restart`?
14. What does `-d` mean?
15. What does `-p 8080:80` mean?
16. How do you pass environment variables?
17. How do you mount a volume?
18. How do you create a Docker network?
19. How do containers communicate?
20. How do you troubleshoot an exited container?

## Advanced

21. What is Docker context?
22. How do you reduce image/storage usage?
23. How would you debug a container that cannot reach another container?
24. How do you securely authenticate to a private registry?
25. What is the difference between a bind mount and volume?
26. How do you inspect container networking?
27. How do you monitor container resource usage?
28. What is the risk of `docker system prune -a`?
29. How does Docker CLI communicate with `dockerd`?
30. Explain a complete Docker build → tag → push → deploy workflow.

---

# 🧠 46. Knowledge Checklist

Before moving to **04-Images**, you should be able to:

- [ ] Use `docker version`
- [ ] Use `docker info`
- [ ] Use `docker pull`
- [ ] List images
- [ ] Build an image
- [ ] Tag an image
- [ ] Run a container
- [ ] List containers
- [ ] Stop/start/restart containers
- [ ] Remove containers
- [ ] Read logs
- [ ] Execute commands inside containers
- [ ] Inspect containers
- [ ] Copy files
- [ ] Monitor resources
- [ ] Create networks
- [ ] Create volumes
- [ ] Publish ports
- [ ] Pass environment variables
- [ ] Push/pull registry images
- [ ] Perform basic troubleshooting
- [ ] Safely perform cleanup

---

# ⚡ 47. Docker CLI Cheat Sheet

```bash
# System
docker version
docker info
docker --help

# Images
docker pull IMAGE
docker image ls
docker image inspect IMAGE
docker history IMAGE
docker build -t NAME:TAG .
docker tag SOURCE TARGET
docker rmi IMAGE

# Containers
docker run IMAGE
docker ps
docker ps -a
docker stop CONTAINER
docker start CONTAINER
docker restart CONTAINER
docker kill CONTAINER
docker rm CONTAINER
docker logs CONTAINER
docker exec -it CONTAINER sh
docker inspect CONTAINER
docker top CONTAINER
docker stats

# Network
docker network ls
docker network create NETWORK
docker network inspect NETWORK
docker network connect NETWORK CONTAINER
docker network disconnect NETWORK CONTAINER
docker network rm NETWORK

# Volumes
docker volume ls
docker volume create VOLUME
docker volume inspect VOLUME
docker volume rm VOLUME

# Registry
docker login
docker logout
docker pull IMAGE
docker tag IMAGE REGISTRY/IMAGE:TAG
docker push REGISTRY/IMAGE:TAG

# Cleanup
docker container prune
docker image prune
docker network prune
docker volume prune
docker system df
docker system prune
```

---

# 🗺️ 48. What's Next?

You've now learned:

```text
01 Fundamentals
      ↓
02 Installation
      ↓
03 Docker CLI  ← YOU ARE HERE
      ↓
04 Images
```

## 👉 [04 — Docker Images](../04-Images/)

Next you will go deep into:

```text
Image Architecture
      ↓
Image Layers
      ↓
Tags
      ↓
Digests
      ↓
Dockerfile → Image
      ↓
Build Context
      ↓
Build Cache
      ↓
Image Inspection
      ↓
Image Optimization
      ↓
Registry
```

---

<div align="center">

# 🐳 Master the CLI. Master Docker.

### VishwaTech Labs

**Docker • DevOps • Cloud • GitHub Actions • Terraform • Ansible • Kubernetes • Security**

### By Vishwanath Gowda H

⭐ Learn → Practice → Debug → Automate → Deploy

</div>
