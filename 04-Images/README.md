<div align="center">

# 🐳 Docker Images — Complete Deep Dive

### 🚀 Zero to Hero | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Images-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/)
[![Layers](https://img.shields.io/badge/Architecture-Layers-blueviolet)](#-image-layers)
[![Hands-On](https://img.shields.io/badge/Labs-20+-orange)](#-hands-on-labs)
[![Security](https://img.shields.io/badge/Security-Image%20Hygiene-success)](#-image-security)
[![DevOps](https://img.shields.io/badge/Track-DevOps-purple)](#)
[![VishwaTech Labs](https://img.shields.io/badge/Training-VishwaTech%20Labs-black)](#)

**Understand Docker images from pull → layers → tags → digests → build → cache → registry → production optimization.**

[📘 Docker Images](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/) •
[🏗️ Docker Build](https://docs.docker.com/build/) •
[🔍 Image Inspect](https://docs.docker.com/reference/cli/docker/image/inspect/) •
[📦 Docker Hub](https://hub.docker.com/)

</div>

---

# 🎯 What You Will Learn

```text
Docker Image
     │
     ├── Image vs Container
     ├── Image Naming
     ├── Tags
     ├── Digests
     ├── Layers
     ├── Image Metadata
     ├── Image History
     ├── Build Context
     ├── Build Cache
     ├── Multi-Platform Images
     ├── Registries
     ├── Image Security
     └── Image Optimization
```

By the end of this module, you should be able to:

- Explain exactly what a Docker image is
- Explain image layers
- Understand tags and digests
- Pull and inspect images
- Build your own images
- Understand build context
- Understand cache behavior
- Read image history
- Tag images for registries
- Push and pull images
- Reduce image size
- Apply image-security best practices

---

# 🧠 1. What Is a Docker Image?

A Docker image is a **read-only template used to create containers**.

Think:

```text
IMAGE
  │
  │ docker run
  ▼
CONTAINER
```

A single image can create many containers:

```text
                 🐳 IMAGE
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       Container Container Container
           1          2          3
```

Official reference:

👉 [What is a Docker image?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/)

---

# 🏗️ 2. Image vs Container

| Docker Image | Docker Container |
|---|---|
| Read-only template | Runnable instance |
| Used to create containers | Created from an image |
| Stored locally/registry | Has runtime state |
| Immutable by design | Runtime filesystem can change |
| Versioned/tagged | Started/stopped |
| Example: `nginx:latest` | Example: `my-nginx` |

Simple analogy:

```text
📐 Blueprint
    ↓
 IMAGE
    ↓
🏠 House
    ↓
 CONTAINER
```

---

# 🔥 3. Why Images Matter

Images make application delivery repeatable.

Without an image:

```text
Application
   +
Manual dependencies
   +
Manual configuration
   +
Different servers
   ↓
Deployment inconsistency
```

With an image:

```text
Application
   +
Dependencies
   +
Runtime
   +
Configuration defaults
   ↓
Docker Image
   ↓
Repeatable deployment
```

---

# 🧱 4. Image Layers

One of the most important Docker concepts.

A Docker image is built from **layers**.

Example Dockerfile:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

Conceptually:

```text
┌─────────────────────────────┐
│ Application Source Layer    │
├─────────────────────────────┤
│ Dependencies Layer          │
├─────────────────────────────┤
│ Working Directory Metadata  │
├─────────────────────────────┤
│ Python Base Image Layers    │
└─────────────────────────────┘
```

Docker's image documentation explains images as consisting of layers, with each layer representing filesystem changes. citeturn0search0

---

# 🧩 5. Why Layers Are Useful

Suppose you have:

```text
Base Image
    ↓
Dependencies
    ↓
Application
```

You change only:

```text
Application
```

Docker can often reuse unchanged earlier build layers.

```text
Base Image        ✅ Reuse
Dependencies      ✅ Reuse
Application       🔄 Rebuild
```

This is one reason Dockerfile instruction ordering matters.

---

# 🏗️ 6. Image Layer Example

Consider:

```dockerfile
FROM ubuntu:24.04

RUN apt-get update

RUN apt-get install -y nginx

COPY index.html /var/www/html/
```

Conceptually:

```text
Layer 4 → COPY index.html
Layer 3 → Install nginx
Layer 2 → apt update
Layer 1 → Ubuntu base image
```

When source code changes:

```text
Layer 1  ✅
Layer 2  ✅
Layer 3  ✅
Layer 4  🔄
```

---

# 🧠 7. Important Layer Concept

A Dockerfile instruction does not necessarily map one-to-one to the exact final layer representation in every build implementation, especially with modern BuildKit optimizations.

For teaching, however, this mental model is extremely useful:

```text
Dockerfile instructions
          ↓
Filesystem changes
          ↓
Build layers / cache
          ↓
Final image
```

---

# 🏷️ 8. Image Tags

Tags provide human-readable references to images.

Examples:

```text
nginx:latest
nginx:1.27
python:3.12
python:3.12-slim
myapp:v1.0.0
myapp:production
```

Format:

```text
REPOSITORY:TAG
```

Example:

```text
nginx:1.27
```

Where:

```text
Repository = nginx
Tag        = 1.27
```

---

# ⚠️ 9. Why `latest` Is Not a Version

This is important for students.

```text
myapp:latest
```

does not inherently mean:

```text
newest
```

It is simply a tag name.

A registry owner can move a tag to point to a different image.

For production:

```text
❌ myapp:latest
```

Prefer controlled versioning:

```text
✅ myapp:1.4.2
```

And for strict immutability:

```text
myapp@sha256:<digest>
```

---

# 🔐 10. Image Digests

An image digest identifies image content using a cryptographic digest.

Example:

```text
nginx@sha256:abc123...
```

Conceptually:

```text
IMAGE
  │
  ▼
Content
  │
  ▼
SHA-256 digest
  │
  ▼
Immutable content reference
```

### Tag vs Digest

| Tag | Digest |
|---|---|
| Human-friendly | Content-addressed |
| Can move | Identifies specific content |
| Easy for development | Useful for immutable deployments |
| Example `v1.0` | Example `sha256:...` |

---

# 🧭 11. Image Name Anatomy

A fully qualified image reference can look like:

```text
registry.example.com/team/myapp:v1.2.0
```

Breakdown:

```text
registry.example.com
        │
        ▼
      team
        │
        ▼
      myapp
        │
        ▼
      v1.2.0
```

Another example:

```text
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:1.0
```

---

# 🌍 12. Image Registries

Registries store and distribute images.

Common examples:

- [Docker Hub](https://hub.docker.com/)
- [Amazon ECR](https://aws.amazon.com/ecr/)
- [GitHub Container Registry](https://github.com/features/packages)
- [Azure Container Registry](https://azure.microsoft.com/products/container-registry/)
- [Google Artifact Registry](https://cloud.google.com/artifact-registry/)

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
   ↓
docker pull
   ↓
Server / Kubernetes
```

---

# 📥 13. Pull an Image

```bash
docker pull nginx
```

Specific tag:

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

List:

```bash
docker image ls
```

---

# 🔍 14. Inspect an Image

```bash
docker image inspect nginx
```

Short legacy form:

```bash
docker inspect nginx
```

You can inspect metadata such as:

- Architecture
- OS
- Environment
- Entrypoint
- CMD
- Root filesystem information
- Configuration
- Image identifiers

---

# 📜 15. Image History

Run:

```bash
docker history nginx
```

This helps you understand how an image was built.

Typical information includes:

```text
IMAGE
CREATED
CREATED BY
SIZE
COMMENT
```

Teaching model:

```text
Dockerfile
   ↓
Build operations
   ↓
Image history
   ↓
Layers
```

---

# 🧮 16. Image Size

Check image sizes:

```bash
docker image ls
```

Example concept:

```text
REPOSITORY   TAG       SIZE
python       3.12      ...
nginx        1.27      ...
ubuntu       24.04     ...
```

Smaller does not automatically mean safer, but reducing unnecessary packages can reduce attack surface and transfer time.

---

# 🏗️ 17. Build Your First Image

Create:

```text
Dockerfile
```

Example:

```dockerfile
FROM nginx:stable-alpine

COPY index.html /usr/share/nginx/html/index.html
```

Build:

```bash
docker build -t my-nginx:1.0 .
```

Check:

```bash
docker image ls
```

Run:

```bash
docker run -d \
  --name my-nginx \
  -p 8080:80 \
  my-nginx:1.0
```

---

# 📦 18. What Does `.` Mean in `docker build`?

Command:

```bash
docker build -t myapp:1.0 .
```

The final:

```text
.
```

means:

> Use the current directory as the build context.

Conceptually:

```text
Current Directory
       │
       ▼
Build Context
       │
       ▼
Dockerfile
       │
       ▼
Docker Build
       │
       ▼
Image
```

---

# 🚫 19. `.dockerignore`

The build context can become unnecessarily large.

Create:

```text
.dockerignore
```

Example:

```text
.git
.github
node_modules
__pycache__
*.log
.env
.venv
dist
build
```

Benefits:

- Smaller build context
- Faster builds
- Avoid accidental secret/file inclusion
- Cleaner builds

---

# ⚡ 20. Build Cache

Docker builds can reuse previously generated build results when the relevant inputs have not changed.

Example:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

Good order:

```text
Base image
   ↓
Dependency files
   ↓
Install dependencies
   ↓
Application source
```

Why?

```text
Application code changes
        ↓
Dependency files unchanged
        ↓
Dependency installation may be reused
        ↓
Faster rebuild
```

---

# ❌ 21. Poor Dockerfile Ordering

Avoid unnecessarily doing:

```dockerfile
COPY . .

RUN pip install -r requirements.txt
```

if application source changes frequently.

A better pattern is:

```dockerfile
COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .
```

This often gives better cache reuse.

---

# 🏎️ 22. `--no-cache`

Force a build without using the normal build cache:

```bash
docker build --no-cache -t myapp:1.0 .
```

Useful when:

- Debugging stale build behavior
- Testing clean builds
- Intentionally forcing rebuilds

Do not use it blindly for every build because it can make builds significantly slower.

---

# 🔨 23. BuildKit / Modern Docker Build

Modern Docker uses BuildKit/buildx capabilities for efficient builds and advanced features.

Check build capabilities:

```bash
docker buildx version
```

List builders:

```bash
docker buildx ls
```

Build with buildx:

```bash
docker buildx build -t myapp:1.0 .
```

Official reference:

👉 [Docker Build](https://docs.docker.com/build/)

---

# 🌎 24. Multi-Platform Images

Modern applications may need images for different CPU architectures.

Examples:

```text
linux/amd64
linux/arm64
```

Why?

```text
Intel/AMD Server
      ↓
linux/amd64

ARM Server / Apple Silicon
      ↓
linux/arm64
```

Build a multi-platform image:

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t USERNAME/myapp:1.0 \
  --push .
```

A registry can store the platform-specific manifests/images under a multi-platform image reference.

---

# 🧬 25. Image Manifest

A registry can associate one image reference with multiple platform-specific images.

Conceptually:

```text
myapp:1.0
    │
    ▼
Manifest / Index
    │
    ├── linux/amd64
    │
    └── linux/arm64
```

This allows a client to select the appropriate platform image.

---

# 🔐 26. Image Security

Never assume:

```text
Small image = Secure image
```

Security requires multiple controls.

```text
Trusted Source
      ↓
Minimal Image
      ↓
Pinned / Controlled Versions
      ↓
Non-root User
      ↓
Dependency Management
      ↓
Image Scanning
      ↓
Signed / Verified Artifacts
      ↓
Secure Registry
```

---

# 🛡️ 27. Trusted Base Images

Prefer known, maintained base images.

Examples:

```dockerfile
FROM python:3.12-slim
```

or:

```dockerfile
FROM nginx:stable-alpine
```

For controlled production environments, consider pinning to a digest:

```dockerfile
FROM python:3.12-slim@sha256:<digest>
```

Always balance strict immutability with your patch/update process.

---

# 👤 28. Run as Non-Root

A Docker image should not automatically run the application as root if the application can safely run as an unprivileged user.

Example:

```dockerfile
RUN useradd --create-home appuser

USER appuser
```

Why?

```text
Root process
   ↓
Higher impact if compromised

Non-root process
   ↓
Reduced privileges
```

---

# 🔑 29. Never Put Secrets in Images

Never:

```dockerfile
ENV API_KEY=super-secret-key
```

Never:

```dockerfile
COPY .env /app/.env
```

Never:

```dockerfile
ARG PASSWORD=secret123
```

Instead use:

```text
CI/CD Secret Store
Cloud Secret Manager
Runtime Secrets
Kubernetes Secrets
Docker/Compose secret mechanisms
```

---

# 🔎 30. Scan Images

Image scanning helps identify known vulnerabilities in image components and dependencies.

Possible tools include:

- Docker Scout
- Trivy
- Cloud registry scanning
- Enterprise security scanners

Example workflow:

```text
Build
  ↓
Scan
  ↓
Find vulnerabilities
  ↓
Fix
  ↓
Rebuild
  ↓
Scan again
  ↓
Publish
```

Official Docker security tooling:

👉 [Docker Scout](https://docs.docker.com/scout/)

---

# 🏷️ 31. Tagging Strategy

Development:

```text
myapp:dev
```

Release:

```text
myapp:1.0.0
```

Environment:

```text
myapp:staging
myapp:production
```

Git commit:

```text
myapp:8f42abc
```

Production best practice:

```text
Human-friendly tag
        +
Immutable digest
```

---

# 📌 32. Image Immutability

A powerful production concept:

```text
Build once
    ↓
Scan once
    ↓
Publish
    ↓
Deploy exact artifact
```

Instead of:

```text
Build
   ↓
Deploy
   ↓
"latest" changes
   ↓
Unexpected deployment
```

Prefer:

```text
myapp:1.4.2
        +
sha256:digest
```

---

# 📤 33. Tag for Docker Hub

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

---

# ☁️ 34. Tag for Amazon ECR

Example:

```bash
docker tag myapp:1.0 \
  ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com/myapp:1.0
```

Then authenticate using AWS-supported ECR authentication and push:

```bash
docker push \
  ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com/myapp:1.0
```

Official reference:

👉 [Amazon ECR User Guide](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)

---

# 🔄 35. Complete Image Lifecycle

```text
                 Dockerfile
                     │
                     ▼
                  BUILD
                     │
                     ▼
               Docker Image
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
        TEST       SCAN       TAG
          │          │          │
          └──────────┼──────────┘
                     ▼
                   PUSH
                     │
                     ▼
                 Registry
                     │
                     ▼
                  PULL
                     │
                     ▼
                 DEPLOY
                     │
                     ▼
               Kubernetes/ECS
```

---

# 🧪 36. Hands-On Labs

## Lab 01 — Pull Images

```bash
docker pull nginx
docker pull python:3.12-slim
docker image ls
```

---

## Lab 02 — Inspect Image

```bash
docker image inspect nginx
```

---

## Lab 03 — History

```bash
docker history nginx
```

---

## Lab 04 — Build First Image

Create:

```text
index.html
Dockerfile
```

Dockerfile:

```dockerfile
FROM nginx:stable-alpine
COPY index.html /usr/share/nginx/html/
```

Build:

```bash
docker build -t my-nginx:1.0 .
```

---

## Lab 05 — Run Custom Image

```bash
docker run -d \
  --name custom-nginx \
  -p 8080:80 \
  my-nginx:1.0
```

---

## Lab 06 — Tag an Image

```bash
docker tag my-nginx:1.0 USERNAME/my-nginx:1.0
```

---

## Lab 07 — Push to Docker Hub

```bash
docker login
docker push USERNAME/my-nginx:1.0
```

---

## Lab 08 — Pull Your Image

```bash
docker pull USERNAME/my-nginx:1.0
```

---

## Lab 09 — Image History

Build an image with multiple instructions and inspect:

```bash
docker history my-nginx:1.0
```

---

## Lab 10 — Build Context

Create:

```text
large-file/
secret-demo/
.git/
```

Then use `.dockerignore` to exclude unnecessary files.

---

## Lab 11 — Cache Experiment

Build:

```bash
docker build -t cache-demo:1 .
```

Change only application source and rebuild:

```bash
docker build -t cache-demo:2 .
```

Observe which steps can be reused.

---

## Lab 12 — No Cache

```bash
docker build --no-cache -t cache-demo:clean .
```

Compare build behavior.

---

## Lab 13 — Multi-Platform Build

Use a suitable Dockerfile and builder:

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t USERNAME/multiarch-demo:1.0 \
  --push .
```

---

## Lab 14 — Image Size Optimization

Create:

```text
large Dockerfile
optimized Dockerfile
```

Compare:

```bash
docker image ls
```

Discuss:

```text
Base image
Packages
Layers
Build context
Multi-stage builds
```

---

## Lab 15 — Non-Root Image

Create an image that runs the application as a non-root user.

Verify:

```bash
docker run --rm myapp id
```

---

## Lab 16 — Image Scan

Build an image and scan it with your chosen approved scanner.

Record:

```text
Image
Tag
Scanner
Findings
Severity
Fix
Rescan result
```

---

## Lab 17 — Digest

Inspect the image and identify its immutable content digest.

Discuss:

```text
Tag
vs
Digest
```

---

## Lab 18 — ECR Image

Build:

```bash
docker build -t myapp:1.0 .
```

Tag:

```bash
docker tag myapp:1.0 ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com/myapp:1.0
```

Authenticate and push using AWS-supported ECR commands.

---

## Lab 19 — Image Cleanup

Check:

```bash
docker system df
```

Then carefully remove unused images:

```bash
docker image prune
```

---

## Lab 20 — Production Image Review

Take an existing Dockerfile and review:

```text
☑ Trusted base image
☑ Version controlled
☑ Small runtime
☑ .dockerignore
☑ Efficient layer order
☑ No secrets
☑ Non-root user
☑ Health strategy
☑ Scan result
☑ Meaningful tag
```

---

# 🧠 37. Real-Time Scenario — "My Image Is 1.5 GB!"

Suppose:

```text
myapp:latest
Size = 1.5 GB
```

Investigate:

```bash
docker history myapp:latest
```

Then ask:

```text
1. Is the base image too large?
2. Are build tools included?
3. Are package caches included?
4. Is the build context too large?
5. Are unnecessary files copied?
6. Can multi-stage builds help?
7. Can unnecessary OS packages be removed?
```

Optimization path:

```text
1.5 GB
  ↓
Analyze layers
  ↓
Reduce dependencies
  ↓
Use appropriate base image
  ↓
Improve .dockerignore
  ↓
Multi-stage build
  ↓
Smaller runtime image
```

---

# 💥 38. Real-Time Scenario — "It Works With `latest`"

Developer:

```text
myapp:latest works!
```

Later:

```text
myapp:latest behaves differently!
```

Possible issue:

```text
latest
  ↓
Tag moved
  ↓
Different image content
```

Better:

```text
myapp:1.4.2
       +
sha256:digest
```

Production principle:

> **Know exactly which artifact you deployed.**

---

# 🧪 39. Student Challenge

Build a Python application image.

Requirements:

```text
☑ Dockerfile
☑ .dockerignore
☑ Versioned tag
☑ Non-root user
☑ No secrets
☑ Efficient layer order
☑ Image scan
☑ Push to registry
```

Expected:

```text
Source Code
    ↓
Dockerfile
    ↓
Build
    ↓
Image
    ↓
Scan
    ↓
Tag
    ↓
Push
    ↓
Registry
```

---

# 🎓 40. Interview Questions

## Beginner

1. What is a Docker image?
2. Image vs container?
3. What is an image tag?
4. What is an image digest?
5. What is a registry?
6. What does `docker pull` do?
7. What does `docker build` do?
8. What does `docker image inspect` do?
9. What does `docker history` show?
10. What is `.dockerignore`?

## Intermediate

11. What are Docker image layers?
12. Why are layers useful?
13. How does build cache work?
14. Why does Dockerfile instruction order matter?
15. What is the build context?
16. Why should you avoid `latest` in production?
17. What is a content digest?
18. How do you reduce image size?
19. What is a multi-platform image?
20. What is a manifest/index?

## Advanced

21. Explain tag vs digest.
22. How would you make a Docker image reproducible?
23. How do you secure a base image?
24. How do you scan an image?
25. How would you reduce a 1.5 GB production image?
26. Why should secrets not be stored in image layers?
27. What happens if a Dockerfile layer changes?
28. How does Docker build cache affect CI/CD?
29. How would you design an image promotion process?
30. Explain Build → Scan → Sign/Verify → Push → Deploy.

---

# 🏆 41. Knowledge Checklist

Before moving to **05-Containers**, you should be able to:

- [ ] Explain images
- [ ] Explain image layers
- [ ] Explain tags
- [ ] Explain digests
- [ ] Explain image references
- [ ] Pull images
- [ ] Build images
- [ ] Inspect images
- [ ] Read image history
- [ ] Tag images
- [ ] Push images
- [ ] Pull from private registries
- [ ] Explain build context
- [ ] Use `.dockerignore`
- [ ] Explain build cache
- [ ] Use `--no-cache`
- [ ] Explain multi-platform images
- [ ] Explain manifests/indexes
- [ ] Apply image security practices
- [ ] Run applications as non-root
- [ ] Avoid secrets in images
- [ ] Scan images
- [ ] Optimize image size
- [ ] Explain immutable deployments

---

# ⚡ 42. Image Command Cheat Sheet

```bash
# Pull
docker pull IMAGE[:TAG]

# List
docker image ls

# Inspect
docker image inspect IMAGE

# History
docker history IMAGE

# Build
docker build -t NAME:TAG .

# Build without cache
docker build --no-cache -t NAME:TAG .

# Tag
docker tag SOURCE TARGET

# Push
docker push IMAGE:TAG

# Remove
docker rmi IMAGE

# Disk usage
docker system df

# Buildx
docker buildx version
docker buildx ls
docker buildx build ...
```

---

# 🗺️ 43. What's Next?

Your journey:

```text
01 Fundamentals
       ↓
02 Installation
       ↓
03 Docker CLI
       ↓
04 Docker Images  ← 🟢 YOU ARE HERE
       ↓
05 Containers
```

## 👉 [05 — Docker Containers](../05-Containers/)

Next we go deep into the **runtime side**:

```text
Container Lifecycle
       ↓
Create
       ↓
Start
       ↓
Run
       ↓
Stop
       ↓
Restart
       ↓
Pause
       ↓
Kill
       ↓
Remove
       ↓
Logs
       ↓
Exec
       ↓
Inspect
       ↓
Environment
       ↓
Resources
       ↓
Container Security
```

---

<div align="center">

# 🐳 Images Are the Heart of Container Delivery

### Build Once • Scan • Version • Publish • Deploy

## VishwaTech Labs

**Docker • DevOps • Cloud • GitHub Actions • Terraform • Ansible • Kubernetes • Security**

### By Vishwanath Gowda H

⭐ Learn → Practice → Build → Secure → Deploy

</div>
