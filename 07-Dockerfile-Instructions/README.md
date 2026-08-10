<div align="center">

# 🐳 Dockerfile Instructions — Complete Reference

### 🚀 Zero to Hero | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Dockerfile-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/reference/dockerfile/)
[![Reference](https://img.shields.io/badge/Reference-Complete-orange)](#-instruction-reference)
[![Security](https://img.shields.io/badge/Security-Hardening-success)](#-security-checklist)
[![Labs](https://img.shields.io/badge/Labs-25+-blueviolet)](#-hands-on-labs)
[![DevOps](https://img.shields.io/badge/Track-DevOps-purple)](#)

**A practical instruction-by-instruction Dockerfile reference for students, DevOps engineers, CI/CD engineers, cloud engineers, and Kubernetes practitioners.**

[📘 Official Dockerfile Reference](https://docs.docker.com/reference/dockerfile/) •
[🏗️ Docker Build](https://docs.docker.com/build/) •
[🔐 Build Secrets](https://docs.docker.com/build/building/secrets/) •
[🧱 Multi-stage Builds](https://docs.docker.com/build/building/multi-stage/)

</div>

---

# 🎯 Module Objective

In the previous module, you learned how to write a complete Dockerfile.

Now we go **instruction by instruction**.

```text
Dockerfile
│
├── FROM
├── RUN
├── CMD
├── ENTRYPOINT
├── COPY
├── ADD
├── WORKDIR
├── ENV
├── ARG
├── EXPOSE
├── USER
├── VOLUME
├── HEALTHCHECK
├── SHELL
├── STOPSIGNAL
├── ONBUILD
└── parser directives
```

For every important instruction, this guide explains:

```text
Syntax
   ↓
Purpose
   ↓
When it executes
   ↓
Example
   ↓
Common mistake
   ↓
Best practice
   ↓
Interview question
```

---

# 🧠 1. Dockerfile Execution Model

Before learning instructions, understand the two major phases:

```text
                 Dockerfile
                     │
                     ▼
               docker build
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
     Build-time              Metadata
       actions                / config
          │                     │
          └──────────┬──────────┘
                     ▼
                  IMAGE
                     │
                     │ docker run
                     ▼
                CONTAINER
                     │
                     ▼
               Runtime process
```

### Build-time examples

```dockerfile
RUN apt-get update
RUN pip install -r requirements.txt
COPY app.py /app/
```

### Runtime behavior examples

```dockerfile
CMD ["python", "app.py"]
ENTRYPOINT ["python"]
ENV APP_ENV=production
USER appuser
```

The distinction is essential when designing images.

---

# ⭐ 2. `FROM`

## Purpose

Defines the base image or starts a new build stage.

## Syntax

```dockerfile
FROM image
```

or:

```dockerfile
FROM image:tag
```

or:

```dockerfile
FROM image@sha256:<digest>
```

Example:

```dockerfile
FROM python:3.12-slim
```

## When?

At build time.

## Example

```dockerfile
FROM nginx:stable-alpine
```

## Multi-stage

```dockerfile
FROM node:22 AS builder

FROM nginx:stable-alpine
```

## Common mistake

```dockerfile
FROM latest
```

This is not a meaningful complete image reference in the normal sense.

Better:

```dockerfile
FROM nginx:stable-alpine
```

## Best practice

Choose a maintained, trusted base image and control the version intentionally.

---

# 🔨 3. `RUN`

## Purpose

Executes commands during the image build.

## Syntax

```dockerfile
RUN command
```

or:

```dockerfile
RUN ["executable", "param1", "param2"]
```

Example:

```dockerfile
RUN apt-get update
```

Better package pattern:

```dockerfile
RUN apt-get update \
    && apt-get install -y --no-install-recommends curl \
    && rm -rf /var/lib/apt/lists/*
```

## When?

Build time.

```text
docker build
      ↓
RUN
      ↓
Image filesystem/configuration
```

## Common mistake

```dockerfile
RUN cd /app
RUN python app.py
```

The `cd` does not persist across separate `RUN` instructions.

Better:

```dockerfile
WORKDIR /app
RUN python app.py
```

## Important

Do not use `RUN` to start the long-running application.

Bad:

```dockerfile
RUN python app.py
```

Use:

```dockerfile
CMD ["python", "app.py"]
```

---

# 📦 4. `COPY`

## Purpose

Copies files/directories from the build context or supported build sources into the image.

## Syntax

```dockerfile
COPY source destination
```

Example:

```dockerfile
COPY app.py /app/
```

With working directory:

```dockerfile
WORKDIR /app
COPY . .
```

## Cache-friendly example

```dockerfile
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
```

## Common mistake

Trying to copy a file outside the build context:

```dockerfile
COPY ../secret.txt /app/
```

This is not how normal Docker build contexts work.

## Best practice

Use `COPY` for ordinary local file copying.

---

# ➕ 5. `ADD`

## Purpose

Copies files into the image and supports additional semantics beyond ordinary `COPY`.

Historically, `ADD` has been used for local archives and URL-related sources.

## Example

```dockerfile
ADD application.tar.gz /app/
```

## `COPY` vs `ADD`

| COPY | ADD |
|---|---|
| Simple file copy | Additional semantics |
| Clearer intent | More behavior |
| Preferred for ordinary copying | Use deliberately |

### Rule for students

```text
Normal local file?
       ↓
COPY

Need ADD-specific behavior?
       ↓
ADD
```

---

# 📂 6. `WORKDIR`

## Purpose

Sets the working directory for subsequent instructions.

## Syntax

```dockerfile
WORKDIR /app
```

Example:

```dockerfile
WORKDIR /app

COPY . .
RUN python -m pip install -r requirements.txt
```

## Why not?

```dockerfile
RUN cd /app
```

Because each shell-form `RUN` instruction is a separate build step.

Use:

```dockerfile
WORKDIR /app
```

## Runtime impact

`WORKDIR` also establishes the working directory for subsequent runtime instructions such as `CMD`/`ENTRYPOINT` unless overridden.

---

# 🌍 7. `ENV`

## Purpose

Sets environment variables in the image configuration.

## Syntax

```dockerfile
ENV NAME=value
```

Example:

```dockerfile
ENV APP_ENV=production
```

Multiple:

```dockerfile
ENV APP_ENV=production \
    PORT=8080
```

Runtime override:

```bash
docker run -e APP_ENV=staging myapp:1.0
```

## Common mistake

```dockerfile
ENV DB_PASSWORD=secret123
```

Do not store secrets in image configuration.

Use appropriate runtime secret-management mechanisms.

---

# 🧪 8. `ARG`

## Purpose

Defines a build-time variable.

## Syntax

```dockerfile
ARG NAME=default
```

Build:

```bash
docker build \
  --build-arg APP_VERSION=2.0 \
  -t myapp:2.0 .
```

Dockerfile:

```dockerfile
ARG APP_VERSION=1.0

RUN echo "Building version ${APP_VERSION}"
```

## Scope

An `ARG` declared before the first `FROM` can be used in `FROM` expressions.

Example:

```dockerfile
ARG PYTHON_VERSION=3.12

FROM python:${PYTHON_VERSION}-slim
```

An `ARG` may also need to be declared again inside a stage if you want to use it after `FROM`.

## Security warning

Do not treat `ARG` as a secret store.

Build arguments can be exposed through build metadata/history depending on the build.

For secrets, use BuildKit secret mechanisms.

---

# 🆚 9. `ARG` vs `ENV`

| ARG | ENV |
|---|---|
| Build-time variable | Image environment |
| Passed during build | Available to runtime by default |
| Useful for build configuration | Useful for application environment |
| Not a secret mechanism | Not a secret mechanism |

Example:

```dockerfile
ARG APP_VERSION=1.0
ENV APP_ENV=production
```

Think:

```text
ARG
 ↓
Build configuration

ENV
 ↓
Image/runtime environment
```

---

# 📡 10. `EXPOSE`

## Purpose

Documents the port on which a containerized application listens.

Example:

```dockerfile
EXPOSE 8080
```

Multiple:

```dockerfile
EXPOSE 8080
EXPOSE 8443
```

## Critical distinction

This:

```dockerfile
EXPOSE 8080
```

does **not** publish the port to the host.

Publish:

```bash
docker run -p 8080:8080 myapp:1.0
```

Mental model:

```text
EXPOSE
   ↓
Image metadata/documentation

-p
   ↓
Runtime port publishing
```

---

# 👤 11. `USER`

## Purpose

Sets the user/group used for subsequent build instructions and the default runtime user.

Example:

```dockerfile
RUN useradd --create-home appuser

USER appuser
```

Check:

```bash
docker run --rm myapp id
```

## Security principle

Prefer non-root execution when the application supports it.

## Common mistake

Assuming:

```dockerfile
USER appuser
```

alone fixes every security problem.

Security requires:

```text
Image
+
Application
+
Filesystem
+
Capabilities
+
Network
+
Secrets
+
Runtime configuration
```

---

# 💾 12. `VOLUME`

## Purpose

Declares a mount point intended for persistent/external storage.

Example:

```dockerfile
VOLUME ["/data"]
```

## Important

Do not confuse:

```dockerfile
VOLUME ["/data"]
```

with:

```bash
docker run -v mydata:/data myapp
```

The runtime command explicitly controls how storage is attached.

## Production thinking

Document and manage persistent storage deliberately rather than assuming the container writable layer is durable.

---

# ❤️ 13. `HEALTHCHECK`

## Purpose

Defines a health test for a container.

Example:

```dockerfile
HEALTHCHECK --interval=30s \
            --timeout=5s \
            --start-period=10s \
            --retries=3 \
            CMD wget --spider -q http://localhost/ || exit 1
```

Possible state:

```text
starting
healthy
unhealthy
```

Inspect:

```bash
docker inspect myapp
```

## Important

A health check does not automatically fix a broken application.

It provides health information that runtime/orchestration tooling can use.

---

# 🚀 14. `CMD`

## Purpose

Provides the default command or default arguments for a container.

Example:

```dockerfile
CMD ["python", "app.py"]
```

Run:

```bash
docker run myapp:1.0
```

The default command is used.

Override:

```bash
docker run --rm myapp:1.0 python other.py
```

## Common mistake

Using shell form unnecessarily:

```dockerfile
CMD python app.py
```

Prefer:

```dockerfile
CMD ["python", "app.py"]
```

for clearer process and signal behavior.

---

# 🎯 15. `ENTRYPOINT`

## Purpose

Configures the main executable for the container.

Example:

```dockerfile
ENTRYPOINT ["python"]
```

Combined:

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Run:

```bash
docker run myapp:1.0
```

Conceptually:

```bash
python app.py
```

Override default argument:

```bash
docker run myapp:1.0 other.py
```

Conceptually:

```bash
python other.py
```

---

# 🆚 16. `ENTRYPOINT` vs `CMD`

### Pattern 1

```dockerfile
CMD ["python", "app.py"]
```

Simple default command.

### Pattern 2

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Fixed executable with replaceable default arguments.

Visual:

```text
ENTRYPOINT
    │
    └── python
          +
CMD
    │
    └── app.py
          ↓
     python app.py
```

Interview answer:

> `ENTRYPOINT` defines the main executable behavior, while `CMD` provides default command/arguments that can be overridden at runtime.

---

# 🧩 17. Exec Form

Example:

```dockerfile
CMD ["python", "app.py"]
```

This is JSON/exec form.

Advantages include clearer argument handling and direct process execution.

Preferred for many production application containers.

---

# 🐚 18. Shell Form

Example:

```dockerfile
CMD python app.py
```

Shell form runs through the shell behavior associated with the image/platform.

Use deliberately when shell features are required.

Do not choose shell form merely because it is shorter.

---

# 🛑 19. `STOPSIGNAL`

## Purpose

Sets the system call signal used to stop a container.

Example:

```dockerfile
STOPSIGNAL SIGTERM
```

Use when your application has a specific expected shutdown signal.

The exact signal and application behavior should be tested.

---

# 🐚 20. `SHELL`

## Purpose

Overrides the default shell used by shell-form instructions.

Example:

```dockerfile
SHELL ["/bin/bash", "-c"]
```

Useful when:

```text
Bash-specific syntax
Complex shell operations
Windows PowerShell/CMD behavior
```

Example Linux:

```dockerfile
SHELL ["/bin/bash", "-c"]

RUN source /opt/app/env && echo "$APP_ENV"
```

Use only when the selected shell exists in the base image.

---

# 🔥 21. `ONBUILD`

## Purpose

Adds a trigger instruction that executes when the image is later used as a base for another build.

Example:

```dockerfile
FROM python:3.12-slim

ONBUILD COPY requirements.txt /app/
ONBUILD RUN pip install -r /app/requirements.txt
```

If another Dockerfile uses:

```dockerfile
FROM my-python-base
```

the `ONBUILD` triggers can execute in the child build.

## Caution

`ONBUILD` can hide build behavior from users of the base image.

Use it only when the behavior is intentional and well documented.

---

# 🧭 22. Parser Directives

Dockerfile parser directives provide instructions to the builder before normal Dockerfile processing.

Common examples include:

```dockerfile
# syntax=docker/dockerfile:1
```

and:

```dockerfile
# escape=`
```

Parser directives must appear in the appropriate location near the beginning of the Dockerfile.

Example:

```dockerfile
# syntax=docker/dockerfile:1

FROM alpine:3.20
```

Use the official Dockerfile reference for current syntax and supported directives.

---

# 🧱 23. `FROM` with Multiple Stages

Example:

```dockerfile
FROM golang:1.24 AS builder

WORKDIR /src
COPY . .
RUN go build -o app .

FROM alpine:3.20

COPY --from=builder /src/app /app

CMD ["/app"]
```

Concept:

```text
Stage 1
Builder
   │
   │ compiled artifact
   ▼
Stage 2
Runtime
```

This reduces the need to ship build tools in the final runtime image.

---

# 🧮 24. Instruction Ordering

Poor:

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
requirements.txt
       ↓
Changes less often

Application source
       ↓
Changes frequently
```

This often improves build-cache reuse.

---

# 📁 25. `.dockerignore`

Although `.dockerignore` is not a Dockerfile instruction, it is essential to Dockerfile engineering.

Example:

```text
.git
.github
.env
*.log
node_modules
__pycache__
.venv
dist
build
coverage
```

Benefits:

```text
Smaller context
      ↓
Faster transfer
      ↓
Less accidental content
      ↓
Cleaner builds
```

Do not blindly exclude files that the build actually requires.

---

# 🔐 26. Secrets — What NOT to Do

### ❌ Bad

```dockerfile
ENV AWS_SECRET_ACCESS_KEY=secret
```

### ❌ Bad

```dockerfile
ARG TOKEN=secret
```

### ❌ Bad

```dockerfile
COPY .env /app/
```

### Better concept

```text
CI/CD Secret Store
       ↓
Build Secret Mechanism
       ↓
Temporary build access
       ↓
Final image
without intentionally embedding secret
```

See:

👉 [Docker Build secrets](https://docs.docker.com/build/building/secrets/)

---

# 🛡️ 27. Security Checklist by Instruction

| Instruction | Security Focus |
|---|---|
| `FROM` | Trusted/maintained base |
| `RUN` | Minimize packages, clean metadata |
| `COPY` | Avoid copying secrets |
| `ADD` | Use deliberately |
| `ENV` | Never store secrets |
| `ARG` | Never treat as secret |
| `USER` | Prefer non-root |
| `EXPOSE` | Documentation only |
| `VOLUME` | Understand persistence |
| `HEALTHCHECK` | Correct endpoint/command |
| `CMD` | Predictable process |
| `ENTRYPOINT` | Predictable executable |
| `SHELL` | Use only required shell |
| `ONBUILD` | Document hidden triggers |

---

# 🐍 28. Complete Python Example

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

HEALTHCHECK --interval=30s \
            --timeout=5s \
            --start-period=10s \
            --retries=3 \
            CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')" || exit 1

CMD ["python", "app.py"]
```

---

# 🟨 29. Complete Node.js Example

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

USER node

EXPOSE 3000

CMD ["npm", "start"]
```

For a real production application, review whether development dependencies, build tools, and source files are actually needed in the runtime image.

---

# 🔵 30. Complete Nginx Example

```dockerfile
FROM nginx:stable-alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80

HEALTHCHECK --interval=30s \
            --timeout=5s \
            --retries=3 \
            CMD wget --spider -q http://localhost/ || exit 1
```

Build:

```bash
docker build -t website:1.0 .
```

Run:

```bash
docker run -d \
  --name website \
  -p 8080:80 \
  website:1.0
```

---

# 🚀 31. Production Dockerfile Pattern

A general pattern:

```dockerfile
# syntax=docker/dockerfile:1

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

HEALTHCHECK --interval=30s \
            --timeout=5s \
            --start-period=10s \
            --retries=3 \
            CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')" || exit 1

ENTRYPOINT ["python"]
CMD ["app.py"]
```

Review every line against the real application before production use.

---

# 🔍 32. How to Read Any Dockerfile

When you see an unfamiliar Dockerfile, read in this order:

```text
1. FROM
   ↓
2. ARG
   ↓
3. WORKDIR
   ↓
4. COPY / ADD
   ↓
5. RUN
   ↓
6. ENV
   ↓
7. USER
   ↓
8. EXPOSE
   ↓
9. HEALTHCHECK
   ↓
10. ENTRYPOINT / CMD
```

Then ask:

```text
What is the base?
What gets installed?
What files enter the image?
Which user runs it?
What port does it use?
How is it started?
How is it checked?
Are there secrets?
Is it cache-efficient?
Is it unnecessarily large?
```

---

# 🚨 33. Troubleshooting by Instruction

## `FROM` failure

Check:

```text
Image name
Tag
Registry access
Network
Architecture
```

## `RUN` failure

Check:

```text
Package manager
Command
Shell
Dependencies
Permissions
```

## `COPY` failure

Check:

```text
Source path
Build context
.dockerignore
Filename case
```

## `USER` failure

Check:

```text
User exists
Group exists
File ownership
Directory permissions
```

## `CMD`/`ENTRYPOINT` failure

Check:

```text
Executable
Arguments
PATH
File permissions
Working directory
```

## Health check failure

Check:

```text
Endpoint
Port
Application startup time
Command availability
DNS
Credentials
```

---

# 🧪 34. Hands-On Labs

## Lab 01 — `FROM`

Create three images:

```text
Ubuntu
Python
Nginx
```

Compare:

```bash
docker image ls
```

---

## Lab 02 — `RUN`

Install a package.

Then inspect:

```bash
docker history image:tag
```

---

## Lab 03 — `WORKDIR`

Create:

```dockerfile
WORKDIR /app
RUN pwd
```

Build and inspect the output.

---

## Lab 04 — `COPY`

Copy:

```text
app.py
requirements.txt
```

into:

```text
/app
```

---

## Lab 05 — `ADD`

Demonstrate a legitimate `ADD` use case.

Then explain why `COPY` is preferable for ordinary file copying.

---

## Lab 06 — `ENV`

Create:

```dockerfile
ENV APP_ENV=development
```

Verify:

```bash
docker run --rm image env
```

---

## Lab 07 — `ARG`

Build:

```bash
docker build \
  --build-arg APP_VERSION=2.0 \
  -t arg-demo:2.0 .
```

---

## Lab 08 — `ARG` + `FROM`

Create:

```dockerfile
ARG PYTHON_VERSION=3.12
FROM python:${PYTHON_VERSION}-slim
```

Build with different versions where supported.

---

## Lab 09 — `USER`

Create a non-root user.

Verify:

```bash
docker run --rm image id
```

---

## Lab 10 — `EXPOSE`

Add:

```dockerfile
EXPOSE 8080
```

Then demonstrate that publishing still requires runtime configuration:

```bash
docker run -p 8080:8080 image
```

---

## Lab 11 — `CMD`

Create:

```dockerfile
CMD ["echo", "Docker"]
```

Override it at runtime.

---

## Lab 12 — `ENTRYPOINT`

Create:

```dockerfile
ENTRYPOINT ["echo"]
CMD ["Docker"]
```

Test default and overridden arguments.

---

## Lab 13 — `ENTRYPOINT` + `CMD`

Build:

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Run:

```bash
docker run image
docker run image another.py
```

---

## Lab 14 — `HEALTHCHECK`

Add a working health check.

Then intentionally break the endpoint.

Observe:

```bash
docker inspect container
```

---

## Lab 15 — `SHELL`

Use Bash-specific syntax where Bash is available.

Compare with the default shell.

---

## Lab 16 — `STOPSIGNAL`

Configure:

```dockerfile
STOPSIGNAL SIGTERM
```

Test graceful shutdown behavior.

---

## Lab 17 — `ONBUILD`

Create a parent image containing an `ONBUILD` trigger.

Create a child image using the parent.

Document what happened.

---

## Lab 18 — `.dockerignore`

Create large/unnecessary files.

Build before and after adding exclusions.

---

## Lab 19 — Cache Optimization

Compare:

```dockerfile
COPY . .
RUN pip install ...
```

against:

```dockerfile
COPY requirements.txt .
RUN pip install ...
COPY . .
```

Document the difference.

---

## Lab 20 — Secure Dockerfile

Review and fix:

```text
Root user
Hard-coded secret
Untrusted base
Huge context
Unnecessary packages
Poor cache order
```

---

## Lab 21 — Python Production Dockerfile

Build a Python application with:

```text
Controlled base
Dependencies
Non-root
Healthcheck
No secrets
```

---

## Lab 22 — Node Production Dockerfile

Build a Node.js application.

Evaluate:

```text
Dependency installation
Runtime dependencies
Non-root
Image size
Startup command
```

---

## Lab 23 — Nginx Static Site

Build:

```text
index.html
```

into an Nginx image.

---

## Lab 24 — Multi-stage Introduction

Build an application in a builder stage.

Copy only the artifact to the runtime stage.

---

## Lab 25 — Dockerfile Code Review

Take any Dockerfile and answer:

```text
1. Is the base trusted?
2. Is the version controlled?
3. Is the context small?
4. Are dependencies cache-friendly?
5. Are secrets absent?
6. Does it run non-root?
7. Is the process correct?
8. Is the image unnecessarily large?
9. Is a health check appropriate?
10. Is it CI/CD ready?
```

---

# 🎓 35. Interview Questions

## Beginner

1. What is `FROM`?
2. What is `RUN`?
3. What is `COPY`?
4. What is `ADD`?
5. What is `WORKDIR`?
6. What is `ENV`?
7. What is `ARG`?
8. What is `EXPOSE`?
9. What is `USER`?
10. What is `CMD`?

## Intermediate

11. `CMD` vs `ENTRYPOINT`?
12. `COPY` vs `ADD`?
13. `ARG` vs `ENV`?
14. What is exec form?
15. What is shell form?
16. What is `HEALTHCHECK`?
17. What is `VOLUME`?
18. What is `SHELL`?
19. What is `STOPSIGNAL`?
20. What is `ONBUILD`?

## Advanced

21. Why does Dockerfile instruction ordering matter?
22. How do you optimize Docker build cache?
23. Why should secrets not be passed with `ARG`?
24. How do you build a non-root image?
25. How would you debug a failed `COPY`?
26. How would you debug a failed `RUN`?
27. Why is exec-form `CMD` often preferred?
28. How do health checks work?
29. How do multi-stage builds reduce final image contents?
30. Design a secure production Dockerfile and explain every instruction.

---

# 🏆 36. Knowledge Checklist

Before moving to the next module:

- [ ] `FROM`
- [ ] `RUN`
- [ ] `COPY`
- [ ] `ADD`
- [ ] `WORKDIR`
- [ ] `ENV`
- [ ] `ARG`
- [ ] `EXPOSE`
- [ ] `USER`
- [ ] `VOLUME`
- [ ] `HEALTHCHECK`
- [ ] `CMD`
- [ ] `ENTRYPOINT`
- [ ] Exec form
- [ ] Shell form
- [ ] `SHELL`
- [ ] `STOPSIGNAL`
- [ ] `ONBUILD`
- [ ] Parser directives
- [ ] Build context
- [ ] `.dockerignore`
- [ ] Cache optimization
- [ ] Secrets
- [ ] Non-root images
- [ ] Production Dockerfile review

---

# ⚡ 37. Dockerfile Quick Reference

```dockerfile
# Base
FROM image:tag

# Build variable
ARG NAME=value

# Build command
RUN command

# Environment
ENV NAME=value

# Working directory
WORKDIR /app

# Copy
COPY source destination

# Additional copy semantics
ADD source destination

# User
USER appuser

# Port metadata
EXPOSE 8080

# Storage declaration
VOLUME ["/data"]

# Health
HEALTHCHECK CMD command

# Main executable
ENTRYPOINT ["executable"]

# Default command
CMD ["command", "arg"]

# Shell
SHELL ["/bin/bash", "-c"]

# Stop signal
STOPSIGNAL SIGTERM

# Build trigger
ONBUILD COPY . /app
```

---

# 🗺️ 38. What's Next?

Your Docker course:

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
06 Dockerfile
       ↓
07 Dockerfile Instructions  ← 🟢 YOU ARE HERE
       ↓
08 Build Cache & Layers
       ↓
09 Multi-Stage Builds
       ↓
10 Docker Networking
       ↓
11 Docker Volumes
       ↓
12 Docker Compose
       ↓
13 Docker Registry
       ↓
14 Docker Security
       ↓
15 Docker + GitHub Actions
```

## 👉 [08 — Build Cache & Layers](../08-Build-Cache-and-Layers/)

Next we will focus specifically on:

```text
Docker Build
     ↓
Layers
     ↓
Cache
     ↓
Cache Invalidation
     ↓
BuildKit
     ↓
Build Context
     ↓
Build Secrets
     ↓
Build Performance
     ↓
CI/CD Optimization
```

---

<div align="center">

# 🐳 Know Every Instruction. Build With Confidence.

### VishwaTech Labs

**Docker • GitHub Actions • Terraform • Ansible • Kubernetes • DevOps • Cloud Security**

### By Vishwanath Gowda H

⭐ Learn → Practice → Build → Secure → Automate → Deploy

</div>
