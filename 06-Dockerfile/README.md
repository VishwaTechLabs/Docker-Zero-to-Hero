<div align="center">

# 🐳 Dockerfile — Complete Image Engineering Masterclass

### 🚀 Zero to Hero | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Dockerfile-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/reference/dockerfile/)
[![Build](https://img.shields.io/badge/Build-Hands--On-orange)](#-hands-on-labs)
[![Security](https://img.shields.io/badge/Security-First-success)](#-dockerfile-security)
[![Production](https://img.shields.io/badge/Production-Ready-blueviolet)](#-production-dockerfile)
[![DevOps](https://img.shields.io/badge/Track-DevOps-purple)](#)
[![VishwaTech Labs](https://img.shields.io/badge/Training-VishwaTech%20Labs-black)](#)

**Learn how to design, build, optimize, secure, and troubleshoot production-quality Docker images using Dockerfiles.**

[📘 Dockerfile Reference](https://docs.docker.com/reference/dockerfile/) •
[🏗️ Docker Build](https://docs.docker.com/build/) •
[🧪 Docker Workshop](https://docs.docker.com/get-started/workshop/) •
[🔐 Docker Scout](https://docs.docker.com/scout/)

</div>

---

# 🎯 What You Will Learn

This module answers one of the most important Docker questions:

> **How do we turn application source code into a production-ready Docker image?**

```text
Application Source
       │
       ▼
   Dockerfile
       │
       ├── Base Image
       ├── Working Directory
       ├── Dependencies
       ├── Application Code
       ├── Runtime User
       ├── Environment
       ├── Port Metadata
       ├── Health Check
       └── Startup Command
       │
       ▼
   docker build
       │
       ▼
   Docker Image
       │
       ├── Test
       ├── Scan
       ├── Tag
       └── Push
       │
       ▼
    Registry
```

By the end, you should be able to:

- Write Dockerfiles from scratch
- Understand every major Dockerfile instruction
- Choose appropriate base images
- Control the build context
- Use `.dockerignore`
- Install dependencies efficiently
- Configure environment variables
- Understand `ARG` vs `ENV`
- Use `COPY` and `ADD` correctly
- Understand `CMD` vs `ENTRYPOINT`
- Configure `USER`
- Document ports with `EXPOSE`
- Add health checks
- Optimize Dockerfile layer/cache behavior
- Build Python, Node.js and Nginx images
- Apply security practices
- Debug Docker build failures
- Prepare Dockerfiles for CI/CD and production

---

# 🧠 1. What Is a Dockerfile?

A **Dockerfile is a text file containing instructions used by Docker's build system to create an image.**

Example:

```dockerfile
FROM nginx:stable-alpine

COPY index.html /usr/share/nginx/html/

EXPOSE 80
```

Build:

```bash
docker build -t my-nginx:1.0 .
```

Run:

```bash
docker run -d --name web -p 8080:80 my-nginx:1.0
```

Official reference:

👉 [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)

---

# 🏗️ 2. Dockerfile → Image → Container

The most important mental model:

```text
              Dockerfile
                  │
                  │ docker build
                  ▼
            ┌─────────────┐
            │    IMAGE    │
            └──────┬──────┘
                   │
                   │ docker run
                   ▼
            ┌─────────────┐
            │  CONTAINER  │
            └─────────────┘
```

So:

```text
Dockerfile = Instructions
Image      = Build Artifact
Container  = Running Instance
```

---

# 📁 3. Basic Project Structure

A clean Docker application may look like:

```text
my-app/
│
├── Dockerfile
├── .dockerignore
├── README.md
├── requirements.txt
├── src/
│   └── app.py
└── tests/
```

For Node.js:

```text
my-node-app/
│
├── Dockerfile
├── .dockerignore
├── package.json
├── package-lock.json
├── src/
└── tests/
```

---

# 🧱 4. Minimal Dockerfile

Example:

```dockerfile
FROM nginx:stable-alpine

COPY index.html /usr/share/nginx/html/

EXPOSE 80
```

Build:

```bash
docker build -t my-web:1.0 .
```

Run:

```bash
docker run -d \
  --name my-web \
  -p 8080:80 \
  my-web:1.0
```

Open:

```text
http://localhost:8080
```

---

# 🗺️ 5. Dockerfile Instruction Map

```text
Dockerfile
│
├── FROM
├── ARG
├── RUN
├── ENV
├── WORKDIR
├── COPY
├── ADD
├── USER
├── EXPOSE
├── VOLUME
├── HEALTHCHECK
├── ENTRYPOINT
├── CMD
├── SHELL
└── STOPSIGNAL
```

Some instructions are primarily build-time configuration; others affect image metadata or container runtime behavior.

---

# ⭐ 6. `FROM`

`FROM` specifies the base image for a build stage.

Example:

```dockerfile
FROM ubuntu:24.04
```

Python:

```dockerfile
FROM python:3.12-slim
```

Node:

```dockerfile
FROM node:22-alpine
```

Nginx:

```dockerfile
FROM nginx:stable-alpine
```

Syntax:

```dockerfile
FROM image:tag
```

---

# 🔥 7. Choosing a Base Image

A base image should be selected based on:

```text
Application Requirements
        +
Runtime Compatibility
        +
Security
        +
Support/Lifecycle
        +
Image Size
        +
Operational Requirements
```

Examples:

```text
Python application
       ↓
python:3.12-slim

Node application
       ↓
node:22-alpine

Static web content
       ↓
nginx:stable-alpine
```

Do not choose an image solely because it is small. Compatibility, maintenance, security, and support matter.

---

# ⚠️ 8. Avoid Blindly Using `latest`

Avoid:

```dockerfile
FROM python:latest
```

Prefer a controlled reference:

```dockerfile
FROM python:3.12-slim
```

For high-reproducibility environments, a digest can be used:

```dockerfile
FROM python:3.12-slim@sha256:<digest>
```

Trade-off:

```text
Mutable tag
    ↓
Easy updates

Digest
    ↓
Exact content reference
```

Your update process should deliberately handle security patches.

---

# 🏗️ 9. `RUN`

`RUN` executes commands during image build.

Example:

```dockerfile
FROM ubuntu:24.04

RUN apt-get update
RUN apt-get install -y nginx
```

Prefer combining related package operations and cleaning package metadata in the same layer where appropriate:

```dockerfile
RUN apt-get update \
    && apt-get install -y --no-install-recommends nginx \
    && rm -rf /var/lib/apt/lists/*
```

---

# 🧠 10. Build Time vs Runtime

This distinction is critical.

### `RUN`

Executes during:

```text
docker build
```

### `CMD`

Provides a default command when:

```text
docker run
```

starts the container.

Example:

```dockerfile
RUN pip install -r requirements.txt
```

means:

```text
BUILD TIME
```

While:

```dockerfile
CMD ["python", "app.py"]
```

means:

```text
RUNTIME
```

---

# 📂 11. `WORKDIR`

Sets the working directory for subsequent instructions.

Example:

```dockerfile
WORKDIR /app
```

Then:

```dockerfile
COPY . .
```

copies the build-context contents into:

```text
/app
```

Instead of repeatedly using:

```dockerfile
RUN cd /app
```

Use:

```dockerfile
WORKDIR /app
```

---

# 📦 12. `COPY`

Copies files/directories from the build context into the image.

Example:

```dockerfile
COPY app.py /app/
```

With `WORKDIR`:

```dockerfile
WORKDIR /app
COPY . .
```

Dependency-first pattern:

```dockerfile
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
```

This often improves cache reuse when source code changes more frequently than dependencies.

---

# 🆚 13. `COPY` vs `ADD`

### COPY

Use `COPY` for normal file/directory copying:

```dockerfile
COPY src/ /app/src/
```

### ADD

`ADD` has additional behaviors beyond ordinary copying, including support for certain archive and URL-related use cases.

For ordinary local file copying:

```text
Prefer COPY
```

Use `ADD` only when its additional behavior is intentionally required.

---

# 🌍 14. `ENV`

Sets environment variables in the image.

Example:

```dockerfile
ENV APP_ENV=production
```

Multiple:

```dockerfile
ENV APP_ENV=production \
    APP_PORT=8080
```

At runtime:

```bash
docker run myapp:1.0
```

The application can read these variables.

Override:

```bash
docker run \
  -e APP_ENV=staging \
  myapp:1.0
```

---

# 🆚 15. `ARG` vs `ENV`

This is a common interview question.

### `ARG`

Build-time variable:

```dockerfile
ARG APP_VERSION=1.0
```

Build:

```bash
docker build \
  --build-arg APP_VERSION=2.0 \
  -t myapp:2.0 .
```

### `ENV`

Environment variable that becomes part of the image configuration:

```dockerfile
ENV APP_ENV=production
```

Simple:

```text
ARG → Build-time
ENV → Image/runtime environment
```

> 🔐 Do not treat `ARG` as a safe place for secrets. Build arguments can be exposed through build metadata/history depending on how the image is built.

---

# 📤 16. `EXPOSE`

Documents the network port the application expects to use.

Example:

```dockerfile
EXPOSE 8080
```

Important:

```text
EXPOSE
   ≠
Publish port
```

`EXPOSE` is image metadata/documentation.

To publish:

```bash
docker run -p 8080:8080 myapp:1.0
```

---

# 👤 17. `USER`

Specifies the user used for subsequent build instructions and/or the default runtime user.

Example:

```dockerfile
RUN useradd --create-home appuser

USER appuser
```

Why?

```text
Root
  ↓
Higher privileges

Non-root
  ↓
Reduced privileges
```

Whenever practical, production applications should avoid unnecessary root execution.

---

# ❤️ 18. `HEALTHCHECK`

Defines a command Docker can use to assess container health.

Example:

```dockerfile
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
    ├── starting
    ├── healthy
    └── unhealthy
```

A health check is not the same thing as application orchestration. Platforms such as Kubernetes provide their own readiness/liveness mechanisms.

---

# 🧰 19. `ENTRYPOINT`

Defines the main executable behavior of a container.

Example:

```dockerfile
ENTRYPOINT ["python"]
```

Then:

```bash
docker run myapp:1.0 app.py
```

Conceptually:

```text
ENTRYPOINT
    +
Runtime arguments
    ↓
Final process
```

---

# 🎯 20. `CMD`

Provides default command/arguments.

Example:

```dockerfile
CMD ["python", "app.py"]
```

If the user runs:

```bash
docker run myapp:1.0
```

Docker uses the CMD.

CMD can be overridden by a command supplied at runtime.

---

# 🆚 21. `CMD` vs `ENTRYPOINT`

Common pattern:

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Then:

```bash
docker run myapp:1.0
```

behaves conceptually like:

```bash
python app.py
```

Override default argument:

```bash
docker run myapp:1.0 other.py
```

becomes conceptually:

```bash
python other.py
```

### Simple interview explanation

```text
ENTRYPOINT
    ↓
Defines the main executable

CMD
    ↓
Provides default arguments/command
```

---

# 📝 22. Exec Form vs Shell Form

### Exec form

```dockerfile
CMD ["python", "app.py"]
```

### Shell form

```dockerfile
CMD python app.py
```

For production container processes, exec form is generally preferred because it gives clearer process and signal behavior.

Likewise:

```dockerfile
ENTRYPOINT ["python", "app.py"]
```

is commonly preferred over:

```dockerfile
ENTRYPOINT python app.py
```

---

# 🛑 23. Why Process 1 Matters

A container is typically designed around a main process.

Example:

```text
Container
    │
    ▼
PID 1
    │
    ▼
Application
```

The main process should correctly handle termination signals and exit when the application is finished.

This matters for:

```text
Graceful shutdown
Deployments
Restarts
Orchestration
Signal handling
```

---

# 🧯 24. `STOPSIGNAL`

Allows a Dockerfile to specify the signal used to stop the container.

Example:

```dockerfile
STOPSIGNAL SIGTERM
```

Use only when it matches the application's shutdown behavior.

---

# 💾 25. `VOLUME`

Declares mount points intended for persistent/external storage.

Example:

```dockerfile
VOLUME ["/data"]
```

Modern application deployments often explicitly manage volumes at runtime or through orchestration configuration.

Do not confuse:

```text
VOLUME instruction
```

with:

```bash
docker run -v ...
```

---

# 🐚 26. `SHELL`

Changes the default shell used by shell-form commands during build.

Linux example:

```dockerfile
SHELL ["/bin/bash", "-c"]
```

This can be useful when your build requires specific shell behavior.

---

# 🏗️ 27. Complete Python Dockerfile

Project:

```text
python-app/
│
├── Dockerfile
├── .dockerignore
├── requirements.txt
└── app.py
```

Dockerfile:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["python", "app.py"]
```

Build:

```bash
docker build -t python-app:1.0 .
```

Run:

```bash
docker run -d \
  --name python-app \
  -p 8000:8000 \
  python-app:1.0
```

---

# 🟢 28. Better Python Production Pattern

Example:

```dockerfile
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN useradd --create-home appuser \
    && chown -R appuser:appuser /app

USER appuser

EXPOSE 8000

CMD ["python", "app.py"]
```

Review:

```text
Small runtime base
        ↓
Environment
        ↓
Dependency layer
        ↓
Application
        ↓
Non-root user
        ↓
Port metadata
        ↓
Runtime command
```

---

# 🟨 29. Node.js Dockerfile

Project:

```text
node-app/
│
├── Dockerfile
├── .dockerignore
├── package.json
├── package-lock.json
└── src/
```

Dockerfile:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

Build:

```bash
docker build -t node-app:1.0 .
```

Run:

```bash
docker run -d \
  --name node-app \
  -p 3000:3000 \
  node-app:1.0
```

---

# 🔵 30. Nginx Dockerfile

Project:

```text
nginx-app/
│
├── Dockerfile
└── index.html
```

Dockerfile:

```dockerfile
FROM nginx:stable-alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

Build:

```bash
docker build -t nginx-app:1.0 .
```

Run:

```bash
docker run -d \
  --name nginx-app \
  -p 8080:80 \
  nginx-app:1.0
```

---

# 🧹 31. `.dockerignore`

Example:

```text
.git
.github
.env
*.log
__pycache__
.pytest_cache
.venv
node_modules
dist
build
coverage
Dockerfile*
README.md
```

Be careful:

> Do not blindly copy a generic `.dockerignore` into every project. The correct exclusions depend on what the build actually needs.

---

# ⚡ 32. Dockerfile Layer Optimization

Less effective pattern:

```dockerfile
FROM python:3.12-slim

COPY . .

RUN pip install -r requirements.txt
```

Better:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .
```

Why?

```text
Dependency files
     ↓
Rarely change

Application source
     ↓
Changes frequently
```

This often lets Docker reuse the expensive dependency-installation step.

---

# 🚫 33. Avoid Huge Build Contexts

Bad:

```text
project/
├── node_modules/
├── .git/
├── logs/
├── videos/
├── backups/
├── source/
└── Dockerfile
```

Better:

```text
project/
├── source/
├── package.json
├── package-lock.json
├── Dockerfile
└── .dockerignore
```

Build:

```bash
docker build -t app:1.0 .
```

The build context should contain only what the build needs.

---

# 🔐 34. Dockerfile Security

### ❌ Never

```dockerfile
ENV DB_PASSWORD=SuperSecret
```

### ❌ Never

```dockerfile
COPY .env /app/.env
```

### ❌ Avoid

```dockerfile
USER root
```

when the application does not require it.

### ❌ Avoid unnecessary packages

```dockerfile
RUN apt-get install -y everything
```

### ✅ Prefer

```text
Trusted base
Minimal dependencies
Non-root runtime
No secrets
Version control
Image scanning
Secure build process
```

---

# 🔑 35. Build Secrets

Some builds need credentials to access private package repositories.

Do **not** put credentials into:

```text
ARG
ENV
COPY
RUN echo secret
```

Use BuildKit-supported secret mechanisms when needed.

Concept:

```text
Secret
   ↓
Build step
   ↓
Used temporarily
   ↓
Not intentionally embedded in final image
```

See:

👉 [Build secrets](https://docs.docker.com/build/building/secrets/)

---

# 🏗️ 36. Multi-Stage Build Introduction

Multi-stage builds allow you to use one stage for building and another for the runtime image.

Example:

```dockerfile
FROM node:22 AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:stable-alpine

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80
```

Architecture:

```text
        BUILD STAGE
   ┌──────────────────┐
   │ Source Code      │
   │ Build Tools      │
   │ Dependencies     │
   └────────┬─────────┘
            │
            │ build artifact
            ▼
       RUNTIME STAGE
   ┌──────────────────┐
   │ Nginx             │
   │ Static Files      │
   └──────────────────┘
```

We'll cover multi-stage builds deeply in the dedicated module.

---

# 🔬 37. Build a Dockerfile

Build:

```bash
docker build -t myapp:1.0 .
```

List:

```bash
docker image ls
```

Inspect:

```bash
docker image inspect myapp:1.0
```

History:

```bash
docker history myapp:1.0
```

Run:

```bash
docker run --rm myapp:1.0
```

---

# 🧪 38. Build Debugging

If a build fails:

```bash
docker build -t myapp:debug .
```

Read the failing step.

Useful:

```bash
docker build --progress=plain -t myapp:debug .
```

Then inspect:

```text
1. Base image
2. Package manager
3. File path
4. Build context
5. COPY source
6. Dependency installation
7. Permissions
8. Environment variables
9. Command syntax
```

---

# 🚨 39. Common Dockerfile Errors

## `COPY failed`

Possible causes:

```text
Wrong source path
File outside build context
.dockerignore excludes file
Incorrect case
```

Check:

```bash
docker build ...
```

and inspect the build context.

---

## `RUN command not found`

Possible causes:

```text
Wrong base image
Package not installed
Wrong shell
Wrong architecture
```

---

## Permission denied

Check:

```text
USER
File ownership
Directory permissions
Volume permissions
```

---

## Container exits immediately

Inspect:

```bash
docker ps -a
docker logs <container>
```

Then review:

```dockerfile
CMD
ENTRYPOINT
```

---

# 🧪 40. Hands-On Labs

## Lab 01 — First Dockerfile

Create:

```dockerfile
FROM nginx:stable-alpine
COPY index.html /usr/share/nginx/html/
EXPOSE 80
```

Build and run.

---

## Lab 02 — Python Dockerfile

Create a Python application.

Use:

```dockerfile
FROM python:3.12-slim
```

Build:

```bash
docker build -t python-demo:1.0 .
```

---

## Lab 03 — Node Dockerfile

Use:

```dockerfile
FROM node:22-alpine
```

Build and run.

---

## Lab 04 — Dockerfile Instructions

Create one Dockerfile demonstrating:

```text
FROM
WORKDIR
COPY
RUN
ENV
EXPOSE
USER
CMD
```

---

## Lab 05 — `ARG` vs `ENV`

Create:

```dockerfile
ARG APP_VERSION=1.0
ENV APP_ENV=dev
```

Build:

```bash
docker build \
  --build-arg APP_VERSION=2.0 \
  -t arg-env-demo:2.0 .
```

---

## Lab 06 — `CMD`

Create:

```dockerfile
CMD ["echo", "Hello Docker"]
```

Build and run.

Override the command:

```bash
docker run --rm cmd-demo echo "Override"
```

---

## Lab 07 — `ENTRYPOINT` + `CMD`

Create:

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Run normally and override the default argument.

---

## Lab 08 — `COPY` vs `ADD`

Demonstrate ordinary file copying with `COPY`.

Then research a legitimate use case for `ADD`.

Document why `COPY` is preferred for normal local file copying.

---

## Lab 09 — `.dockerignore`

Create unnecessary files:

```text
.git
logs/
node_modules/
.env
backup/
```

Exclude them.

Build and discuss the build context.

---

## Lab 10 — Cache Optimization

Create a dependency-first Dockerfile.

Change only application source.

Rebuild and identify reused steps.

---

## Lab 11 — Non-Root Image

Create a user:

```dockerfile
RUN useradd --create-home appuser
USER appuser
```

Verify:

```bash
docker run --rm app id
```

---

## Lab 12 — Healthcheck

Add:

```dockerfile
HEALTHCHECK ...
```

Build and inspect container health.

---

## Lab 13 — Production Python Image

Requirements:

```text
☑ slim base
☑ requirements first
☑ no package cache
☑ non-root
☑ no secrets
☑ versioned tag
```

---

## Lab 14 — Production Node Image

Requirements:

```text
☑ dependency caching
☑ production dependencies
☑ non-root
☑ minimal runtime
```

---

## Lab 15 — Nginx Static Website

Create:

```text
index.html
Dockerfile
```

Build:

```bash
docker build -t website:1.0 .
```

Run:

```bash
docker run -d -p 8080:80 website:1.0
```

---

## Lab 16 — Build Debugging

Intentionally introduce:

```text
Wrong COPY path
Wrong package
Wrong CMD
```

Diagnose each failure.

---

## Lab 17 — Build Context Experiment

Create a large unnecessary directory.

Build once.

Add it to `.dockerignore`.

Build again.

Compare behavior.

---

## Lab 18 — Multi-Stage Introduction

Build an application in one stage and copy only the final artifact into the runtime stage.

---

## Lab 19 — Image Security Review

Review a Dockerfile for:

```text
Secrets
Root
Untrusted base
Too many packages
Bad permissions
Poor cache order
Huge context
```

Fix it.

---

## Lab 20 — Production Dockerfile Challenge

Create a Dockerfile that satisfies:

```text
☑ Controlled base image
☑ Efficient layers
☑ .dockerignore
☑ Non-root
☑ No secrets
☑ Health strategy
☑ Clear startup command
☑ Versioned image
☑ Scan-ready
☑ Production documentation
```

---

# 🏆 41. Production Dockerfile

Example pattern:

```dockerfile
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN useradd --create-home appuser \
    && chown -R appuser:appuser /app

USER appuser

EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')" || exit 1

CMD ["python", "app.py"]
```

> Adapt the health check, user setup, dependency strategy, and application command to the actual application. A Dockerfile is not production-ready merely because it contains these instructions.

---

# 🧠 42. Dockerfile Best Practices

```text
1. Choose a maintained base image
2. Control versions intentionally
3. Keep build context small
4. Use .dockerignore
5. Order instructions for cache efficiency
6. Combine related package operations
7. Remove unnecessary package metadata
8. Avoid secrets in Dockerfiles
9. Run as non-root when possible
10. Keep runtime images minimal
11. Use exec-form CMD/ENTRYPOINT
12. Document ports with EXPOSE
13. Add appropriate health checks
14. Scan images
15. Use multi-stage builds where useful
16. Rebuild regularly for security updates
17. Make image tags meaningful
18. Test images before publishing
```

---

# 🎓 43. Interview Questions

## Beginner

1. What is a Dockerfile?
2. What is `FROM`?
3. What is `RUN`?
4. What is `COPY`?
5. What is `WORKDIR`?
6. What is `ENV`?
7. What is `EXPOSE`?
8. What is `CMD`?
9. What is `ENTRYPOINT`?
10. What is `USER`?

## Intermediate

11. `ARG` vs `ENV`?
12. `COPY` vs `ADD`?
13. `CMD` vs `ENTRYPOINT`?
14. Shell form vs exec form?
15. What is `.dockerignore`?
16. What is build context?
17. How does Docker build cache work?
18. Why should dependency files be copied before application source?
19. Why run containers as non-root?
20. What is a health check?

## Advanced

21. How would you optimize a 1 GB Docker image?
22. How do you prevent secrets from entering an image?
23. Why should `latest` not be your only production reference?
24. How do multi-stage builds reduce runtime image contents?
25. How do you debug a failed `COPY`?
26. How do you debug a container that exits immediately?
27. What is the significance of PID 1?
28. How would you design a secure production Dockerfile?
29. How do Dockerfile choices affect CI/CD build speed?
30. Explain your Dockerfile review checklist.

---

# 🏆 44. Knowledge Checklist

Before moving to **07-Dockerfile-Instructions**, you should be able to:

- [ ] Write a Dockerfile from scratch
- [ ] Explain `FROM`
- [ ] Explain `RUN`
- [ ] Explain `WORKDIR`
- [ ] Explain `COPY`
- [ ] Explain `ADD`
- [ ] Explain `ARG`
- [ ] Explain `ENV`
- [ ] Explain `EXPOSE`
- [ ] Explain `USER`
- [ ] Explain `VOLUME`
- [ ] Explain `HEALTHCHECK`
- [ ] Explain `ENTRYPOINT`
- [ ] Explain `CMD`
- [ ] Explain exec vs shell form
- [ ] Use `.dockerignore`
- [ ] Optimize cache order
- [ ] Build Python image
- [ ] Build Node image
- [ ] Build Nginx image
- [ ] Run non-root
- [ ] Avoid secrets
- [ ] Debug build errors
- [ ] Build production-style images
- [ ] Explain multi-stage builds

---

# ⚡ 45. Dockerfile Cheat Sheet

```dockerfile
# Base image
FROM IMAGE:TAG

# Build-time variable
ARG NAME=value

# Execute during build
RUN command

# Set environment variable
ENV NAME=value

# Working directory
WORKDIR /app

# Copy files
COPY source destination

# Add files with additional semantics
ADD source destination

# Runtime user
USER appuser

# Document port
EXPOSE 8080

# Declare mount point
VOLUME ["/data"]

# Health check
HEALTHCHECK CMD command

# Main executable
ENTRYPOINT ["executable"]

# Default command/arguments
CMD ["command", "arg"]
```

Build:

```bash
docker build -t myapp:1.0 .
```

Inspect:

```bash
docker image inspect myapp:1.0
```

History:

```bash
docker history myapp:1.0
```

Run:

```bash
docker run --rm myapp:1.0
```

---

# 🗺️ 46. What's Next?

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
05 Containers
       ↓
06 Dockerfile  ← 🟢 YOU ARE HERE
       ↓
07 Dockerfile Instructions
       ↓
08 Build Cache & Layers
       ↓
09 Multi-Stage Builds
```

## 👉 [07 — Dockerfile Instructions](../07-Dockerfile-Instructions/)

Next we go **instruction-by-instruction** with:

```text
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
HEALTHCHECK
SHELL
STOPSIGNAL
ONBUILD
```

with syntax, execution timing, examples, mistakes, security considerations, and interview scenarios.

---

<div align="center">

# 🐳 Write It. Build It. Secure It.

### VishwaTech Labs

**Docker • DevOps • Cloud • GitHub Actions • Terraform • Ansible • Kubernetes • Security**

### By Vishwanath Gowda H

⭐ Learn → Practice → Build → Secure → Deploy

</div>
