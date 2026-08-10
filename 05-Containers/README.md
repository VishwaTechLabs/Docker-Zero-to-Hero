<div align="center">

# 🐳 Docker Containers — Complete Runtime Masterclass

### 🚀 Zero to Hero | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Containers-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/)
[![Lifecycle](https://img.shields.io/badge/Lifecycle-Hands--On-orange)](#-container-lifecycle)
[![Networking](https://img.shields.io/badge/Networking-Production-blue)](#-container-networking)
[![Security](https://img.shields.io/badge/Security-Container%20Hardening-success)](#-container-security)
[![Labs](https://img.shields.io/badge/Labs-20+-purple)](#-hands-on-labs)
[![VishwaTech Labs](https://img.shields.io/badge/Training-VishwaTech%20Labs-black)](#)

**Learn how containers actually run, communicate, consume resources, fail, recover, and get operated in real DevOps environments.**

[📘 Docker Containers](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/) •
[⚙️ Docker Run](https://docs.docker.com/reference/cli/docker/container/run/) •
[📜 Docker Logs](https://docs.docker.com/reference/cli/docker/container/logs/) •
[🔍 Docker Inspect](https://docs.docker.com/reference/cli/docker/inspect/) •
[🌐 Docker Networking](https://docs.docker.com/engine/network/)

</div>

---

# 🎯 What You Will Learn

This module moves from **Docker images** into the **runtime world of containers**.

```text
IMAGE
  │
  │ docker run
  ▼
CONTAINER
  │
  ├── Process
  ├── Filesystem
  ├── Environment
  ├── Network
  ├── Ports
  ├── Volumes
  ├── Logs
  ├── Resources
  ├── Health
  └── Security
```

By the end of this module, you should be able to:

- Create and run containers
- Understand container lifecycle
- Start, stop, restart and remove containers
- Run foreground and detached containers
- Publish ports
- Pass environment variables
- Execute commands inside containers
- Read and follow logs
- Inspect runtime configuration
- Copy files
- Monitor CPU and memory
- Configure restart policies
- Configure health checks
- Connect containers to networks
- Persist data with volumes
- Troubleshoot failed containers
- Apply container security practices

---

# 🧠 1. What Is a Container?

A container is a **runnable instance of an image**.

Simple:

```text
IMAGE
  │
  │ docker run
  ▼
CONTAINER
```

Think:

```text
📐 Blueprint
    ↓
 Docker Image
    ↓
🏠 Running House
    ↓
 Docker Container
```

A container has a runtime lifecycle and can have its own:

```text
Process
Filesystem
Environment
Network
Ports
Volumes
Resource configuration
```

Official reference:

👉 [What is a container?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/)

---

# 🏗️ 2. Image vs Container

| Image | Container |
|---|---|
| Read-only template | Runnable instance |
| Build artifact | Runtime object |
| Stored locally/registry | Created on a Docker host |
| Versioned/tagged | Named/identified |
| Used to create containers | Runs application processes |

Visual:

```text
                 IMAGE
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
   Container 1 Container 2 Container 3
```

One image can create many containers.

---

# 🔥 3. Container Lifecycle

The most important lifecycle model:

```text
                 IMAGE
                   │
                   ▼
               CREATE
                   │
                   ▼
               CREATED
                   │
                   ▼
                START
                   │
                   ▼
               RUNNING
              ↙    ↓     ↘
          PAUSE   STOP   RESTART
            │      │
            ▼      ▼
         PAUSED  STOPPED
                   │
                   ▼
                  RM
                   │
                   ▼
                REMOVED
```

Important commands:

```bash
docker create
docker start
docker run
docker stop
docker restart
docker pause
docker unpause
docker kill
docker rm
```

---

# 🚀 4. `docker run`

The most commonly used container command.

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

Environment:

```bash
docker run -d \
  --name web \
  -e APP_ENV=production \
  nginx
```

---

# 🧩 5. What Does `docker run` Actually Do?

When you execute:

```bash
docker run nginx
```

Docker conceptually performs:

```text
1. Find image locally
       ↓
2. Pull image if necessary
       ↓
3. Create container
       ↓
4. Configure filesystem
       ↓
5. Configure networking
       ↓
6. Configure environment
       ↓
7. Configure runtime settings
       ↓
8. Start application process
```

Important:

> `docker run` creates **and starts** a new container.

---

# 🏗️ 6. `docker create` vs `docker run`

### `docker create`

Creates the container but does not start it.

```bash
docker create --name web nginx
```

Check:

```bash
docker ps -a
```

You should see:

```text
STATUS
Created
```

Start it:

```bash
docker start web
```

### `docker run`

Equivalent concept:

```text
create + start
```

Example:

```bash
docker run --name web nginx
```

---

# 🟢 7. Foreground vs Detached Mode

Foreground:

```bash
docker run nginx
```

The container's attached output is connected to your terminal.

Detached:

```bash
docker run -d nginx
```

Docker returns the container ID and leaves the container running in the background.

Typical production-style command:

```bash
docker run -d \
  --name web \
  -p 8080:80 \
  nginx
```

---

# 🔌 8. Port Publishing

Container applications listen on container ports.

Example:

```text
Nginx
Container Port = 80
```

Publish:

```bash
docker run -p 8080:80 nginx
```

Meaning:

```text
HOST
8080
  │
  │ Port Publishing
  ▼
CONTAINER
80
```

Syntax:

```bash
-p HOST_PORT:CONTAINER_PORT
```

Examples:

```bash
-p 8080:80
-p 3000:3000
-p 5432:5432
-p 8081:8080
```

---

# 🌐 9. Bind Published Ports Carefully

This:

```bash
-p 8080:80
```

can publish the port on host interfaces according to Docker's port publishing behavior.

For local-only access, you can explicitly bind to localhost:

```bash
-p 127.0.0.1:8080:80
```

Visual:

```text
127.0.0.1:8080
       │
       ▼
Container:80
```

This is useful when a service should not be directly exposed externally.

---

# 📋 10. `docker ps`

Running containers:

```bash
docker ps
```

All containers:

```bash
docker ps -a
```

Only container IDs:

```bash
docker ps -q
```

Latest created:

```bash
docker ps -l
```

Useful:

```bash
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

---

# 🏷️ 11. Container Names

Create a meaningful name:

```bash
docker run -d --name frontend nginx
```

Then use:

```bash
docker stop frontend
docker start frontend
docker logs frontend
docker inspect frontend
docker exec -it frontend sh
```

Instead of remembering a long container ID.

---

# 🛑 12. `docker stop`

Gracefully stop:

```bash
docker stop frontend
```

Multiple:

```bash
docker stop frontend backend database
```

Concept:

```text
RUNNING
   │
   │ docker stop
   ▼
STOPPED
```

A graceful stop gives the main process an opportunity to shut down cleanly.

---

# 💀 13. `docker kill`

Forcefully terminate a container:

```bash
docker kill frontend
```

Compare:

```text
docker stop
    ↓
Graceful termination

docker kill
    ↓
Immediate termination
```

Use `kill` when immediate termination is specifically required.

---

# ▶️ 14. `docker start`

Start an existing stopped container:

```bash
docker start frontend
```

Attach:

```bash
docker start -a frontend
```

Interactive:

```bash
docker start -ai frontend
```

Important:

```text
start = existing container
run   = new container
```

---

# 🔄 15. `docker restart`

Restart:

```bash
docker restart frontend
```

With timeout:

```bash
docker restart -t 10 frontend
```

Concept:

```text
RUNNING
   ↓
STOP
   ↓
START
   ↓
RUNNING
```

---

# ⏸️ 16. Pause and Unpause

Pause:

```bash
docker pause frontend
```

Resume:

```bash
docker unpause frontend
```

Concept:

```text
RUNNING
   ↓ pause
PAUSED
   ↓ unpause
RUNNING
```

This is different from stopping a container.

---

# 🗑️ 17. Remove Containers

Remove stopped container:

```bash
docker rm frontend
```

Force:

```bash
docker rm -f frontend
```

Multiple:

```bash
docker rm frontend backend database
```

Important:

```text
docker stop
    ≠
docker rm
```

Stopping changes runtime state.

Removing deletes the container object.

---

# 📜 18. Container Logs

View:

```bash
docker logs frontend
```

Follow:

```bash
docker logs -f frontend
```

Last 100 lines:

```bash
docker logs --tail 100 frontend
```

Timestamps:

```bash
docker logs -t frontend
```

Recent logs:

```bash
docker logs --since 10m frontend
```

Combined:

```bash
docker logs -f --tail 100 frontend
```

Official reference:

👉 [Docker logs](https://docs.docker.com/reference/cli/docker/container/logs/)

---

# 🧠 19. Where Do Container Logs Come From?

For the default logging setup, Docker captures output from the container's standard output/error streams.

Application:

```text
Application
   │
   ├── stdout
   └── stderr
        │
        ▼
     Docker
        │
        ▼
   Logging Driver
```

Therefore, applications should generally log useful operational information to stdout/stderr when using container-native logging patterns.

---

# 🖥️ 20. `docker exec`

Run a command inside a running container:

```bash
docker exec frontend ls
```

Open shell:

```bash
docker exec -it frontend sh
```

If Bash exists:

```bash
docker exec -it frontend bash
```

Environment:

```bash
docker exec frontend env
```

Check user:

```bash
docker exec frontend id
```

Check processes:

```bash
docker exec frontend ps
```

---

# 🚪 21. `docker attach` vs `docker exec`

### `docker exec`

Starts a **new process** inside a running container:

```bash
docker exec -it frontend sh
```

### `docker attach`

Attaches your terminal to the container's main process:

```bash
docker attach frontend
```

Concept:

```text
exec
  ↓
New process

attach
  ↓
Existing main process
```

For troubleshooting, `exec` is usually the safer and more predictable tool.

---

# 🔍 22. `docker inspect`

Inspect:

```bash
docker inspect frontend
```

Useful information:

```text
Container ID
Image
State
Environment
Mounts
Networks
IP address
Ports
Restart policy
Health
Runtime configuration
```

Use inspect when you need facts rather than guesses.

---

# 📦 23. `docker cp`

Host → container:

```bash
docker cp index.html frontend:/usr/share/nginx/html/
```

Container → host:

```bash
docker cp frontend:/etc/nginx/nginx.conf .
```

Useful for troubleshooting and temporary file extraction.

For production configuration, prefer reproducible image/configuration workflows rather than manually modifying running containers.

---

# 📊 24. `docker stats`

Live resource monitoring:

```bash
docker stats
```

Specific:

```bash
docker stats frontend
```

Typical metrics:

```text
CPU %
Memory Usage / Limit
Memory %
Network I/O
Block I/O
PIDs
```

Visual:

```text
Container
   │
   ├── CPU
   ├── Memory
   ├── Network
   ├── Disk I/O
   └── Processes
```

---

# 🧮 25. CPU and Memory Limits

Containers can be configured with resource constraints.

Example memory limit:

```bash
docker run -d \
  --name memory-demo \
  --memory 512m \
  nginx
```

CPU limit:

```bash
docker run -d \
  --name cpu-demo \
  --cpus 1.0 \
  nginx
```

Concept:

```text
Host
│
├── Container A → 512 MB
├── Container B → 1 CPU
└── Container C → configured resources
```

Exact behavior depends on the host OS and Docker runtime configuration.

---

# 🔁 26. Restart Policies

Restart policies can help containers automatically restart under defined conditions.

Example:

```bash
docker run -d \
  --name web \
  --restart unless-stopped \
  nginx
```

Common policies:

```text
no
on-failure
always
unless-stopped
```

Examples:

```bash
--restart no
--restart on-failure:5
--restart always
--restart unless-stopped
```

Use restart policies deliberately. They are not a replacement for proper orchestration and health-aware deployment design.

---

# ❤️ 27. Container Health Checks

A container can have a health status when a health check is configured.

Example Dockerfile:

```dockerfile
FROM nginx:stable-alpine

HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
  CMD wget --spider -q http://localhost/ || exit 1
```

Concept:

```text
Container
   │
   ▼
Health Check
   │
   ├── healthy
   ├── unhealthy
   └── starting
```

Check:

```bash
docker inspect frontend
```

Health checks are useful for application-level health, but orchestration platforms may provide additional readiness/liveness mechanisms.

---

# 🌐 28. Container Networking

List networks:

```bash
docker network ls
```

Create:

```bash
docker network create app-network
```

Run:

```bash
docker run -d \
  --name backend \
  --network app-network \
  mybackend:1.0
```

Another:

```bash
docker run -d \
  --name database \
  --network app-network \
  postgres:17
```

Concept:

```text
        app-network
       /            \
      ▼              ▼
 backend          database
```

Containers on the same user-defined network can communicate using container/service names according to Docker networking behavior.

---

# 🔗 29. Connect a Running Container to a Network

Create:

```bash
docker network create app-network
```

Connect:

```bash
docker network connect app-network frontend
```

Inspect:

```bash
docker network inspect app-network
```

Disconnect:

```bash
docker network disconnect app-network frontend
```

---

# 💾 30. Container Storage

A container's writable layer is ephemeral.

If you remove the container:

```text
Container
   │
   ├── Writable Layer
   │
   └── Temporary Runtime Data
```

that writable layer is removed with the container.

For persistent application data:

```text
Container
    │
    ▼
Volume
    │
    ▼
Persistent Data
```

---

# 💾 31. Use a Named Volume

Create:

```bash
docker volume create dbdata
```

Run:

```bash
docker run -d \
  --name database \
  -v dbdata:/var/lib/postgresql/data \
  postgres:17
```

Inspect:

```bash
docker volume inspect dbdata
```

---

# 🗂️ 32. Bind Mount

Example:

```bash
docker run -d \
  --name web \
  -v "$(pwd)/html:/usr/share/nginx/html:ro" \
  -p 8080:80 \
  nginx
```

Concept:

```text
Host Directory
      │
      │ bind mount
      ▼
Container Directory
```

`ro` means:

```text
Read Only
```

---

# 🔐 33. Environment Variables

Pass:

```bash
docker run -d \
  --name app \
  -e APP_ENV=production \
  -e APP_PORT=8080 \
  myapp:1.0
```

Inspect:

```bash
docker inspect app
```

Or:

```bash
docker exec app env
```

> 🔐 Never place production credentials directly in Git-tracked command examples or Dockerfiles.

---

# 🔑 34. Secrets

Avoid:

```bash
docker run -e DB_PASSWORD=SuperSecret123 ...
```

in shared shell history, documentation, CI logs, or source control.

Prefer an appropriate secret-management mechanism for your environment.

For local development, use controlled environment files carefully.

For production, consider:

```text
Cloud Secret Manager
Kubernetes Secrets
CI/CD Secret Store
Docker-supported secret mechanisms
```

---

# 🛡️ 35. Container Security

A secure container should follow least privilege.

```text
Trusted Image
     ↓
Minimal Packages
     ↓
Non-root User
     ↓
Read-only Where Possible
     ↓
Drop Unnecessary Capabilities
     ↓
No Secrets in Image
     ↓
Network Restrictions
     ↓
Image Scanning
     ↓
Runtime Monitoring
```

---

# 👤 36. Non-Root Container

Check current user:

```bash
docker exec app id
```

A Dockerfile can specify:

```dockerfile
USER appuser
```

Why?

```text
Root process
    ↓
Greater potential impact

Non-root process
    ↓
Reduced privilege
```

Non-root does not automatically make an application secure, but it is an important hardening layer.

---

# 🔒 37. Read-Only Root Filesystem

For compatible applications:

```bash
docker run -d \
  --name secure-app \
  --read-only \
  myapp:1.0
```

If the application needs temporary writable space, provide an appropriate temporary filesystem or writable volume.

---

# 🧱 38. Linux Capabilities

Linux capabilities divide traditional root privileges into more granular permissions.

For example, you may see:

```bash
--cap-drop ALL
```

and add only specifically required capabilities.

Example:

```bash
docker run \
  --cap-drop ALL \
  myapp:1.0
```

> ⚠️ Only use restrictive settings after verifying the application actually works with them.

---

# 🧪 39. Real-Time Scenario — Container Exited

Problem:

```bash
docker ps
```

Nothing appears.

First:

```bash
docker ps -a
```

Suppose:

```text
STATUS
Exited (1)
```

Now:

```bash
docker logs myapp
```

Then:

```bash
docker inspect myapp
```

Check:

```text
Command
Entrypoint
Environment
Mounts
Network
Exit Code
Health
```

Troubleshooting flow:

```text
Container missing?
       ↓
docker ps -a
       ↓
Exited?
       ↓
docker logs
       ↓
Inspect
       ↓
Fix configuration
       ↓
Recreate container
```

---

# 🚨 40. Real-Time Scenario — Port Conflict

Run:

```bash
docker run -d -p 8080:80 nginx
```

Then try again:

```bash
docker run -d -p 8080:80 nginx
```

You may receive a port allocation error because the host port is already being used.

Solution:

```bash
docker run -d -p 8081:80 nginx
```

Or identify the existing container:

```bash
docker ps
```

---

# 🚨 41. Real-Time Scenario — Container Cannot Reach Database

Architecture:

```text
Frontend
   ↓
Backend
   ↓
Database
```

Check:

```bash
docker network ls
```

Then:

```bash
docker network inspect app-network
```

Verify all containers are attached.

Test DNS/connectivity from the backend container:

```bash
docker exec -it backend sh
```

Then use whatever network diagnostic tools are available in the image.

Important concept:

```text
Same user-defined network
        +
Correct service/container name
        +
Correct application port
        ↓
Expected connectivity
```

---

# 🧪 42. Hands-On Labs

## Lab 01 — Create a Container

```bash
docker create --name web nginx
docker ps -a
docker start web
```

---

## Lab 02 — Run Detached

```bash
docker run -d --name nginx1 nginx
docker ps
```

---

## Lab 03 — Port Publishing

```bash
docker run -d \
  --name nginx2 \
  -p 8080:80 \
  nginx
```

Open:

```text
http://localhost:8080
```

---

## Lab 04 — Container Lifecycle

Practice:

```bash
docker stop nginx2
docker start nginx2
docker restart nginx2
docker pause nginx2
docker unpause nginx2
docker stop nginx2
docker rm nginx2
```

---

## Lab 05 — Logs

```bash
docker run -d --name log-demo nginx
docker logs log-demo
docker logs -f log-demo
```

---

## Lab 06 — Exec

```bash
docker run -d --name exec-demo nginx
docker exec -it exec-demo sh
```

Inside:

```bash
ls
env
id
```

Exit:

```bash
exit
```

---

## Lab 07 — Inspect

```bash
docker inspect exec-demo
```

Identify:

```text
IP
Ports
Mounts
Environment
State
```

---

## Lab 08 — Copy Files

Create:

```text
index.html
```

Copy:

```bash
docker cp index.html exec-demo:/usr/share/nginx/html/
```

---

## Lab 09 — Environment Variables

```bash
docker run -d \
  --name env-demo \
  -e APP_ENV=training \
  -e APP_VERSION=1.0 \
  nginx
```

Verify:

```bash
docker exec env-demo env
```

---

## Lab 10 — Network

```bash
docker network create training-network
```

Run:

```bash
docker run -d \
  --name web \
  --network training-network \
  nginx
```

Inspect:

```bash
docker network inspect training-network
```

---

## Lab 11 — Volume

```bash
docker volume create training-data
```

Run:

```bash
docker run -d \
  --name volume-demo \
  -v training-data:/data \
  nginx
```

Inspect:

```bash
docker volume inspect training-data
```

---

## Lab 12 — Restart Policy

```bash
docker run -d \
  --name restart-demo \
  --restart unless-stopped \
  nginx
```

Inspect:

```bash
docker inspect restart-demo
```

---

## Lab 13 — Resource Limits

```bash
docker run -d \
  --name resource-demo \
  --memory 512m \
  --cpus 1.0 \
  nginx
```

Monitor:

```bash
docker stats resource-demo
```

---

## Lab 14 — Health Check

Create a Dockerfile with a health check.

Build:

```bash
docker build -t health-demo:1.0 .
```

Run:

```bash
docker run -d --name health-demo health-demo:1.0
```

Inspect:

```bash
docker inspect health-demo
```

---

## Lab 15 — Read-Only Root Filesystem

Run a compatible image:

```bash
docker run -d \
  --name readonly-demo \
  --read-only \
  nginx
```

Observe application behavior and determine what writable paths are required.

---

## Lab 16 — Container Security

Create a non-root image.

Verify:

```bash
docker exec app id
```

Then experiment with:

```bash
--cap-drop ALL
```

Document what breaks and why.

---

## Lab 17 — Debug Failed Container

Run:

```bash
docker run --name broken nginx invalid-command
```

Diagnose:

```bash
docker ps -a
docker logs broken
docker inspect broken
```

---

## Lab 18 — Three-Tier Containers

Create:

```text
frontend
backend
database
```

Attach all to:

```text
app-network
```

Document the communication flow.

---

## Lab 19 — Resource Monitoring

Run several containers:

```bash
docker stats
```

Record:

```text
CPU
Memory
Network
Block I/O
PIDs
```

---

## Lab 20 — Production Container Review

Review a container deployment:

```text
☑ Versioned image
☑ Non-root user
☑ Resource limits
☑ Restart policy
☑ Health check
☑ Network isolation
☑ Persistent data strategy
☑ No hard-coded secrets
☑ Minimal exposed ports
☑ Logs available
☑ Image scanned
```

---

# 🏗️ 43. Production Container Pattern

A simplified production-style design:

```text
                   🌐 Internet
                       │
                       ▼
                 Load Balancer
                       │
                       ▼
                ┌──────────────┐
                │ App Network  │
                └──────┬───────┘
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        Frontend             Backend
                                 │
                                 ▼
                              Database
                                 │
                                 ▼
                              Volume
```

For production orchestration, tools such as Kubernetes or managed container services typically provide capabilities beyond a single Docker host.

---

# 🎓 44. Interview Questions

## Beginner

1. What is a Docker container?
2. Image vs container?
3. What does `docker run` do?
4. What does `docker create` do?
5. `docker create` vs `docker run`?
6. What does detached mode mean?
7. What does `docker ps` show?
8. What does `docker ps -a` show?
9. How do you stop a container?
10. How do you remove a container?

## Intermediate

11. `docker stop` vs `docker kill`?
12. `docker start` vs `docker run`?
13. What does `docker exec` do?
14. `docker exec` vs `docker attach`?
15. What does `docker inspect` show?
16. How do you view logs?
17. What is port publishing?
18. What is a restart policy?
19. What is a health check?
20. What is a named volume?

## Advanced

21. How do you troubleshoot an exited container?
22. How do you troubleshoot a port conflict?
23. How do containers communicate?
24. How would you restrict container privileges?
25. Why run containers as non-root?
26. What is a read-only root filesystem?
27. What are Linux capabilities?
28. How do resource limits protect a Docker host?
29. How would you design a secure three-tier container application?
30. Explain your complete container troubleshooting methodology.

---

# 🏆 45. Knowledge Checklist

Before moving to **06-Dockerfile**, you should be able to:

- [ ] Explain container lifecycle
- [ ] Use `docker run`
- [ ] Use `docker create`
- [ ] Start/stop/restart containers
- [ ] Pause/unpause containers
- [ ] Kill containers
- [ ] Remove containers
- [ ] Run detached containers
- [ ] Publish ports
- [ ] Pass environment variables
- [ ] Read logs
- [ ] Execute commands inside containers
- [ ] Inspect containers
- [ ] Copy files
- [ ] Monitor resources
- [ ] Configure restart policies
- [ ] Configure health checks
- [ ] Create networks
- [ ] Connect containers to networks
- [ ] Use volumes
- [ ] Troubleshoot exited containers
- [ ] Troubleshoot port conflicts
- [ ] Apply non-root security
- [ ] Understand read-only filesystems
- [ ] Understand resource limits

---

# ⚡ 46. Container Command Cheat Sheet

```bash
# Create / Run
docker create --name NAME IMAGE
docker run IMAGE
docker run -d --name NAME IMAGE

# List
docker ps
docker ps -a
docker ps -q

# Lifecycle
docker start CONTAINER
docker stop CONTAINER
docker restart CONTAINER
docker pause CONTAINER
docker unpause CONTAINER
docker kill CONTAINER
docker rm CONTAINER

# Debug
docker logs CONTAINER
docker logs -f CONTAINER
docker exec -it CONTAINER sh
docker inspect CONTAINER
docker top CONTAINER
docker stats CONTAINER

# Files
docker cp SOURCE CONTAINER:DESTINATION
docker cp CONTAINER:SOURCE DESTINATION

# Network
docker network ls
docker network create NETWORK
docker network inspect NETWORK
docker network connect NETWORK CONTAINER
docker network disconnect NETWORK CONTAINER

# Storage
docker volume ls
docker volume create VOLUME
docker volume inspect VOLUME

# Resources
docker stats
docker inspect CONTAINER

# Cleanup
docker rm CONTAINER
docker container prune
```

---

# 🗺️ 47. What's Next?

Your Docker journey:

```text
01 Fundamentals
       ↓
02 Installation
       ↓
03 Docker CLI
       ↓
04 Images
       ↓
05 Containers  ← 🟢 YOU ARE HERE
       ↓
06 Dockerfile
```

## 👉 [06 — Dockerfile](../06-Dockerfile/)

Next we move into **image engineering**:

```text
Dockerfile
    ↓
FROM
    ↓
WORKDIR
    ↓
COPY
    ↓
ADD
    ↓
RUN
    ↓
ENV / ARG
    ↓
EXPOSE
    ↓
USER
    ↓
HEALTHCHECK
    ↓
ENTRYPOINT / CMD
    ↓
docker build
    ↓
Production Image
```

---

<div align="center">

# 🐳 Containers Are Where Images Come Alive

### Run • Operate • Debug • Secure • Monitor

## VishwaTech Labs

**Docker • DevOps • Cloud • GitHub Actions • Terraform • Ansible • Kubernetes • Security**

### By Vishwanath Gowda H

⭐ Learn → Practice → Debug → Secure → Deploy

</div>
