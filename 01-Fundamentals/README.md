<div align="center">

# 🐳 Docker Fundamentals

### 🚀 Zero to Hero | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Containerization-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Docker Docs](https://img.shields.io/badge/Docker-Official%20Docs-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/)
[![Level](https://img.shields.io/badge/Level-Beginner%20→%20Intermediate-success)](#)
[![Hands-On](https://img.shields.io/badge/Learning-Hands--On-orange)](#-hands-on-lab)
[![DevOps](https://img.shields.io/badge/Track-DevOps-purple)](#)
[![VishwaTech Labs](https://img.shields.io/badge/Training-VishwaTech%20Labs-black)](#)

**Understand Docker from the ground up — before writing your first production Dockerfile.**

[📚 Docker Documentation](https://docs.docker.com/) •
[🧭 Docker Get Started](https://docs.docker.com/get-started/) •
[🧪 Docker Workshop](https://docs.docker.com/get-started/workshop/) •
[🐳 Docker Hub](https://hub.docker.com/)

</div>

---

## 🌟 What You Will Learn

By the end of this module, you should be able to explain:

```text
What is Docker?
Why Docker?
What problem does Docker solve?
What is a container?
What is an image?
What is Docker Engine?
What is Docker CLI?
What is Docker Daemon?
What is a Registry?
How does docker run work?
Container vs Virtual Machine
How Docker fits into DevOps and CI/CD
```

---

# 🧠 1. What Is Docker?

**Docker is an open platform for developing, shipping, and running applications.**

Docker lets us package an application and its required dependencies into a **container**, helping the application behave consistently across development, testing, and production environments.

> 💡 Think of Docker as a way to package an application into a portable, isolated runtime unit.

Official reference: [What is Docker?](https://docs.docker.com/get-started/docker-overview/)

---

# ❌ 2. The Problem Before Containers

Imagine a development team:

```text
Developer Laptop
    │
    ├── Python 3.11
    ├── Node 20
    ├── PostgreSQL 15
    └── Library Version A
             │
             ▼
          "Works!"
             │
             ▼
       Test Server
             │
             ├── Python 3.10
             ├── Node 18
             ├── PostgreSQL 13
             └── Library Version B
             │
             ▼
          "It fails!"
```

The famous problem:

> **"It works on my machine!"** 😄

Docker helps package the application environment so development, testing, and deployment can be much more consistent.

---

# 🚀 3. What Problem Does Docker Solve?

Without containerization:

```text
Application
     +
Dependencies
     +
Runtime
     +
OS Configuration
     +
Environment Differences
          ↓
      Deployment Risk
```

With containers:

```text
Application
     +
Dependencies
     +
Runtime
     ↓
┌──────────────────────┐
│      CONTAINER       │
│                      │
│  Application         │
│  Dependencies        │
│  Runtime             │
│  Configuration       │
└──────────────────────┘
```

### Key benefits

| Benefit | Meaning |
|---|---|
| 📦 Packaging | Package application and dependencies together |
| 🔁 Consistency | Reduce environment differences |
| 🚀 Fast startup | Containers are lightweight processes |
| 🔒 Isolation | Separate application processes and resources |
| 🌍 Portability | Move container images across compatible environments |
| 📈 Scalability | Create multiple container instances |
| 🤖 Automation | Works naturally with CI/CD |
| ☁️ Cloud ready | Commonly used with cloud and orchestration platforms |

---

# 🐳 4. What Is a Container?

A **container is a runnable instance of an image**.

Simple relationship:

```text
             Dockerfile
                 │
                 ▼
           ┌───────────┐
           │   IMAGE   │
           └─────┬─────┘
                 │
             docker run
                 │
                 ▼
           ┌───────────┐
           │ CONTAINER │
           └───────────┘
```

Official reference: [What is a container?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/)

### Container characteristics

- Self-contained
- Isolated
- Independently managed
- Portable
- Runnable from an image

---

# 🧱 5. What Is a Docker Image?

A **Docker image is a read-only template used to create containers.**

Think:

```text
IMAGE = Blueprint
CONTAINER = Running instance of that blueprint
```

Examples:

```text
nginx
python
node
ubuntu
postgres
redis
```

A tagged image can look like:

```text
nginx:latest
python:3.12
node:22
postgres:17
```

---

# 🏠 6. Simple Real-World Analogy

Think about building houses:

```text
🏗️ Blueprint
     ↓
   IMAGE
     ↓
┌─────────────┐
│   HOUSE     │
└─────────────┘
     ↓
 CONTAINER
```

One blueprint can create multiple houses:

```text
             IMAGE
               │
       ┌───────┼───────┐
       ▼       ▼       ▼
    Container Container Container
       1          2        3
```

Similarly, one Docker image can be used to create multiple containers.

---

# 🆚 7. Containers vs Virtual Machines

## Virtual Machine Architecture

```text
┌─────────────────────────────────────┐
│           Physical Server           │
├─────────────────────────────────────┤
│              Host OS                │
├─────────────────────────────────────┤
│            Hypervisor               │
├──────────────┬──────────────────────┤
│    VM 1      │       VM 2           │
│              │                      │
│  Guest OS    │      Guest OS        │
│  Application │      Application     │
└──────────────┴──────────────────────┘
```

## Container Architecture

```text
┌─────────────────────────────────────┐
│           Physical Server           │
├─────────────────────────────────────┤
│              Host OS                │
├─────────────────────────────────────┤
│          Docker / Runtime           │
├──────────────┬──────────────┬───────┤
│ Container 1  │ Container 2  │ C3    │
│ Application  │ Application  │ App   │
└──────────────┴──────────────┴───────┘
```

Docker's documentation explains that containers are isolated processes, while VMs include a full guest operating system. Containers can therefore be much lighter for many application workloads. citeturn0search7

### Comparison

| Feature | Virtual Machine | Container |
|---|---|---|
| Guest OS | ✅ Usually | ❌ No separate guest OS |
| Isolation | Strong | Process/resource isolation |
| Startup | Usually slower | Usually faster |
| Typical size | Larger | Smaller |
| Packaging | VM image | Container image |
| Resource overhead | Higher | Generally lower |
| Common use | Full OS workloads | Application workloads |

> ⚠️ Containers and VMs are not mutually exclusive. Cloud environments commonly run containers inside VMs.

---

# 🏗️ 8. Docker Architecture

Docker uses a **client-server architecture**.

```text
                 👨‍💻 Developer
                      │
                      ▼
               ┌─────────────┐
               │ Docker CLI  │
               │   docker    │
               └──────┬──────┘
                      │
                 Docker API
                      │
                      ▼
               ┌─────────────┐
               │   dockerd   │
               │ Docker      │
               │ Daemon      │
               └──────┬──────┘
                      │
       ┌──────────────┼──────────────┐
       ▼              ▼              ▼
    Images       Containers       Networks
                                      │
                                      ▼
                                   Volumes
```

Docker's official architecture documentation describes the Docker client communicating with the Docker daemon, which manages objects such as images, containers, networks, and volumes. citeturn0search0turn0search6

---

# 🖥️ 9. Docker Client

The Docker CLI is the primary command-line interface many users use to interact with Docker.

Example:

```bash
docker ps
docker images
docker run nginx
```

Conceptually:

```text
docker command
     ↓
Docker API
     ↓
Docker Daemon
```

Official reference: [Docker Engine](https://docs.docker.com/engine/)

---

# ⚙️ 10. Docker Daemon — `dockerd`

The Docker daemon is the long-running server process responsible for managing Docker objects and handling Docker API requests.

Process:

```text
Developer
    │
    │ docker run nginx
    ▼
Docker CLI
    │
    ▼
Docker API
    │
    ▼
dockerd
    │
    ├── Image
    ├── Container
    ├── Network
    └── Volume
```

---

# 🖥️ 11. Docker Desktop

Docker Desktop is an application that makes it easier to install and use Docker on supported desktop environments.

It provides Docker tooling including the Docker CLI, Engine components, Compose, and a graphical interface.

Official reference: [Docker Desktop](https://docs.docker.com/desktop/)

---

# 🗄️ 12. Docker Registry

A **registry stores and distributes container images.**

Examples:

- [Docker Hub](https://hub.docker.com/)
- [Amazon ECR](https://aws.amazon.com/ecr/)
- [GitHub Container Registry](https://github.com/features/packages)
- [Azure Container Registry](https://azure.microsoft.com/products/container-registry/)
- [Google Artifact Registry](https://cloud.google.com/artifact-registry)

Basic workflow:

```text
Developer
    │
    ▼
docker build
    │
    ▼
Docker Image
    │
    ▼
docker push
    │
    ▼
┌─────────────────┐
│ Container       │
│ Registry        │
└─────────────────┘
    │
    ▼
docker pull
    │
    ▼
Server / Kubernetes
```

Official reference: [Docker registries](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-registry/)

---

# 🧩 13. Important Docker Objects

Docker manages several important objects:

```text
                 DOCKER
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
     IMAGE      CONTAINER      NETWORK
       │            │            │
       └────────────┼────────────┘
                    ▼
                  VOLUME
```

### Images

Templates used to create containers.

### Containers

Runnable instances of images.

### Networks

Provide connectivity between containers and other endpoints.

### Volumes

Provide persistent storage managed by Docker.

---

# 🔄 14. Docker Lifecycle

A simplified lifecycle:

```text
             IMAGE
               │
               ▼
          docker create
               │
               ▼
            CREATED
               │
          docker start
               │
               ▼
           RUNNING
          ↙        ↘
 docker stop      docker restart
     │
     ▼
   STOPPED
     │
 docker rm
     │
     ▼
   REMOVED
```

---

# 🔥 15. What Happens When You Run `docker run nginx`?

Command:

```bash
docker run nginx
```

Conceptually:

```text
1️⃣ Docker CLI receives command
              ↓
2️⃣ CLI communicates with Docker daemon
              ↓
3️⃣ Docker checks whether nginx image exists locally
              ↓
4️⃣ If needed, Docker pulls image from registry
              ↓
5️⃣ Docker creates a container
              ↓
6️⃣ Docker configures filesystem/networking
              ↓
7️⃣ Docker starts the container
              ↓
8️⃣ Application process runs
```

Docker documents this flow in its architecture and container overview. citeturn0search0

---

# 🧪 16. Your First Docker Command

Run:

```bash
docker run hello-world
```

Check containers:

```bash
docker ps
```

Check all containers:

```bash
docker ps -a
```

Check images:

```bash
docker images
```

You have now experienced the fundamental Docker workflow:

```text
IMAGE
  ↓
docker run
  ↓
CONTAINER
  ↓
APPLICATION PROCESS
```

---

# 🌐 17. Your First Web Container

Run:

```bash
docker run -d -p 8080:80 docker/welcome-to-docker
```

Then open:

```text
http://localhost:8080
```

Check:

```bash
docker ps
```

You should see port mapping similar to:

```text
0.0.0.0:8080 -> 80/tcp
```

Docker's current beginner documentation uses this style of command for the first container exercise. citeturn0search7turn0search14

---

# 🔌 18. Understanding `-p`

Command:

```bash
docker run -p 8080:80 nginx
```

Meaning:

```text
HOST
Port 8080
    │
    │ Port mapping
    ▼
CONTAINER
Port 80
```

General syntax:

```bash
-p HOST_PORT:CONTAINER_PORT
```

Example:

```bash
-p 3000:3000
-p 8080:80
-p 5432:5432
```

> 💡 `EXPOSE` in a Dockerfile documents a container port; `-p` publishes a container port to the host.

---

# 🧠 19. Docker Vocabulary — Must Know

| Term | Meaning |
|---|---|
| Docker | Platform for building, shipping and running applications |
| Container | Runnable instance of an image |
| Image | Read-only template used to create containers |
| Dockerfile | Instructions for building an image |
| Docker CLI | Command-line client |
| Docker daemon | Server process that manages Docker objects |
| Registry | Image storage/distribution service |
| Docker Hub | Public Docker registry |
| Volume | Persistent Docker-managed storage |
| Network | Container connectivity mechanism |
| Compose | Tool for defining/running multi-container applications |

---

# 🔗 20. Docker → DevOps Connection

Docker becomes much more powerful when combined with the rest of your DevOps toolchain:

```text
                    👨‍💻 Developer
                         │
                         ▼
                      GitHub
                         │
                         ▼
                  GitHub Actions
                         │
                         ▼
                    Docker Build
                         │
                         ▼
                  Security Scan
                         │
                         ▼
                  Container Registry
                         │
               ┌─────────┴─────────┐
               ▼                   ▼
           Kubernetes              ECS
               │
               ▼
          Production
```

And infrastructure/configuration:

```text
Terraform
    │
    ▼
Infrastructure
    │
    ▼
Ansible
    │
    ▼
Configuration
    │
    ▼
Docker
    │
    ▼
Application
    │
    ▼
Kubernetes
```

---

# 🎯 21. Docker in Your 45-Day Course

This Docker module is part of:

```text
45-DAY DEVOPS MASTERCLASS
══════════════════════════════════

Git + GitHub
      ↓
GitHub Actions
      ↓
🐳 DOCKER  ← YOU ARE HERE
      ↓
Terraform
      ↓
Ansible
      ↓
Kubernetes
      ↓
Production CI/CD
```

---

# 🧪 22. Hands-On Lab

## Lab 01 — Run Your First Container

### Step 1 — Verify Docker

```bash
docker version
```

### Step 2 — Run Hello World

```bash
docker run hello-world
```

### Step 3 — List containers

```bash
docker ps -a
```

### Step 4 — List images

```bash
docker images
```

### Step 5 — Run Nginx

```bash
docker run -d --name my-nginx -p 8080:80 nginx
```

### Step 6 — Verify

```bash
docker ps
```

### Step 7 — Open

```text
http://localhost:8080
```

### Step 8 — Check logs

```bash
docker logs my-nginx
```

### Step 9 — Stop

```bash
docker stop my-nginx
```

### Step 10 — Remove

```bash
docker rm my-nginx
```

---

# 📝 23. Student Challenge

Without looking at the commands above, complete:

```text
1. Run hello-world
2. Pull nginx
3. Run nginx in detached mode
4. Publish container port 80 to host port 8080
5. Give the container a meaningful name
6. Check the running container
7. Check its logs
8. Stop it
9. Start it again
10. Remove it
```

### ⭐ Bonus Challenge

Explain this command in your own words:

```bash
docker run -d --name web -p 8080:80 nginx
```

A strong answer should explain:

```text
docker run
-d
--name web
-p 8080:80
nginx
```

---

# 🎓 24. Interview Questions

### Beginner

1. What is Docker?
2. Why do we use Docker?
3. What is a container?
4. What is a Docker image?
5. Image vs container?
6. What is Docker Engine?
7. What is Docker CLI?
8. What is `dockerd`?
9. What is a registry?
10. What is Docker Hub?

### Intermediate

11. Docker vs VM?
12. Why are containers lightweight?
13. What happens when you execute `docker run nginx`?
14. What is port mapping?
15. What is a Docker network?
16. What is a Docker volume?
17. What is Docker Compose?
18. What is the difference between Docker client and Docker daemon?
19. Can multiple containers use the same image?
20. Can one image create multiple containers?

### 💥 Scenario Question

> Your application works on the developer's laptop but fails on the production server because of dependency differences. How can Docker help?

Expected concept:

```text
Application
    +
Dependencies
    +
Runtime
    ↓
Container Image
    ↓
Consistent Deployment
```

---

# 🏆 25. Knowledge Check

Before moving to **02-Installation**, you should be able to answer:

- [ ] What is Docker?
- [ ] Why do we need containers?
- [ ] What is an image?
- [ ] What is a container?
- [ ] Image vs container?
- [ ] Container vs VM?
- [ ] What is Docker Engine?
- [ ] What is Docker CLI?
- [ ] What is `dockerd`?
- [ ] What is a registry?
- [ ] What is Docker Hub?
- [ ] What happens during `docker run`?
- [ ] What does `-p 8080:80` mean?
- [ ] What are Docker objects?
- [ ] How does Docker fit into CI/CD?

---

# 📚 26. Official Learning Resources

| Resource | Link |
|---|---|
| 🐳 Docker Docs | [docs.docker.com](https://docs.docker.com/) |
| 🚀 Get Started | [Docker Get Started](https://docs.docker.com/get-started/) |
| 🧠 What is Docker? | [Docker Overview](https://docs.docker.com/get-started/docker-overview/) |
| 📦 Containers | [What is a container?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/) |
| 🏗️ Docker Engine | [Docker Engine](https://docs.docker.com/engine/) |
| 🧪 Workshop | [Docker Workshop](https://docs.docker.com/get-started/workshop/) |
| 🐳 Docker Hub | [hub.docker.com](https://hub.docker.com/) |

---

# 🗺️ 27. What's Next?

You've learned the **WHY** and **WHAT**.

Now move to:

## 👉 [02 — Installation](../02-Installation/)

There you'll learn:

```text
Docker Desktop
      ↓
Docker Engine
      ↓
Docker CLI
      ↓
docker version
      ↓
docker info
      ↓
hello-world
      ↓
First Real Container
```

---

<div align="center">

## 🚀 Keep Building. Keep Automating. Keep Learning.

### VishwaTech Labs

**Docker • DevOps • Cloud • GitHub Actions • Terraform • Ansible • Kubernetes • Security**

**By Vishwanath Gowda H**

⭐ If this repository helps you learn Docker, star the repository and share it with your DevOps community.

</div>
