<div align="center">

# 🐳 Docker Hub — Complete Zero-to-Hero Masterclass

### 📦 Build → Tag → Login → Push → Pull → Secure | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/)
[![Docker Hub](https://img.shields.io/badge/Docker-Hub-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/docker-hub/)
[![Registry](https://img.shields.io/badge/Registry-Container-blue)](#-docker-hub-architecture)
[![Security](https://img.shields.io/badge/Security-Production-red)](#-docker-hub-security)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-blueviolet)](#-github-actions--docker-hub)

**A dedicated practical guide to Docker Hub for developers, DevOps engineers, trainers, and CI/CD pipelines.**

</div>

---

# 🎯 What Is Docker Hub?

Docker Hub is a hosted container registry used to store, share, discover, and distribute container images.

Architecture:

```text
Developer
    │
    ▼
Dockerfile
    │
    ▼
docker build
    │
    ▼
Local Image
    │
    ├── docker tag
    │
    └── docker login
             │
             ▼
        Docker Hub
             │
       ┌─────┴─────┐
       ▼           ▼
    docker pull   CI/CD
       │           │
       ▼           ▼
   Deployment   Kubernetes
```

Official documentation:

- [Docker Hub documentation](https://docs.docker.com/docker-hub/)
- [Docker CLI](https://docs.docker.com/reference/cli/docker/)

---

# 📦 1. Repository Structure

A Docker Hub image reference commonly looks like:

```text
docker.io/USERNAME/REPOSITORY:TAG
```

Example:

```text
docker.io/vishwatechlabs/python-app:1.0.0
```

Breakdown:

```text
docker.io
    ↓
Registry

vishwatechlabs
    ↓
Namespace

python-app
    ↓
Repository

1.0.0
    ↓
Tag
```

---

# 🏷️ 2. Image Tags

Examples:

```text
myapp:1.0.0
myapp:1.1.0
myapp:2.0.0
myapp:latest
myapp:<git-sha>
```

Recommended production strategy:

```text
Release Tag
+
Commit SHA
+
Digest
```

Avoid depending blindly on:

```text
latest
```

because tags can move.

---

# 🐳 3. Build an Image

Example:

```bash
docker build -t myapp:1.0.0 .
```

Check:

```bash
docker images
```

---

# 🏷️ 4. Tag for Docker Hub

```bash
docker tag \
  myapp:1.0.0 \
  <DOCKERHUB_USERNAME>/myapp:1.0.0
```

Example:

```bash
docker tag \
  myapp:1.0.0 \
  vishwatechlabs/myapp:1.0.0
```

Use your actual Docker Hub username.

---

# 🔐 5. Login

```bash
docker login
```

For automated workflows, use a secure token-based authentication mechanism rather than putting passwords into scripts.

Official:

[Docker login](https://docs.docker.com/reference/cli/docker/login/)

---

# 🚀 6. Push

```bash
docker push \
  <DOCKERHUB_USERNAME>/myapp:1.0.0
```

Flow:

```text
Local Image
    ↓
Tag
    ↓
Login
    ↓
Push
    ↓
Docker Hub
```

---

# 📥 7. Pull

```bash
docker pull \
  <DOCKERHUB_USERNAME>/myapp:1.0.0
```

Run:

```bash
docker run --rm \
  <DOCKERHUB_USERNAME>/myapp:1.0.0
```

---

# 🔍 8. Inspect

```bash
docker image inspect \
  <DOCKERHUB_USERNAME>/myapp:1.0.0
```

List:

```bash
docker image ls
```

---

# 🔢 9. Versioning Strategy

Recommended:

```text
myapp:1.0.0
myapp:1.0.1
myapp:1.1.0
myapp:2.0.0
```

CI:

```text
myapp:<commit-sha>
```

Release:

```text
myapp:1.4.2
```

This creates traceability between:

```text
Git
 ↓
Image
 ↓
Registry
 ↓
Deployment
```

---

# 🧬 10. Image Digest

A registry can identify an exact image manifest by digest:

```text
myapp@sha256:<digest>
```

Concept:

```text
Tag
 ↓
Human-friendly

Digest
 ↓
Exact content identity
```

For high-assurance deployment, record and/or pin the digest rather than relying only on a mutable tag.

---

# 🌐 11. Public vs Private Repositories

### Public

Useful for:

```text
Open source
Learning
Public demos
Reusable images
```

### Private

Useful for:

```text
Enterprise applications
Internal services
Proprietary software
Production images
```

Use the repository visibility and access controls appropriate to the application.

---

# 🔐 12. Docker Hub Security

Recommended:

```text
☑ Strong authentication
☑ Personal access tokens for automation
☑ Private production repositories
☑ Least privilege
☑ Protected credentials
☑ Image scanning
☑ Controlled tags
☑ Image signing strategy
☑ Audit/review access
```

Never put:

```text
Docker Hub password
Docker token
Cloud credentials
API keys
```

inside a Dockerfile.

---

# 🔑 13. Personal Access Tokens

For automation, use a Docker Hub access token where appropriate.

Concept:

```text
GitHub Actions
      ↓
Docker Hub Token
      ↓
Docker Hub
```

Store the token in:

```text
GitHub Secrets
```

not source code.

---

# 🐙 14. GitHub Actions + Docker Hub

Example:

```yaml
name: Docker Hub

on:
  push:
    branches:
      - main

permissions:
  contents: read

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Set up Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build and Push
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: |
            ${{ secrets.DOCKERHUB_USERNAME }}/myapp:${{ github.sha }}
```

For production, use a controlled release strategy rather than automatically treating `latest` as the production artifact.

---

# 🏗️ 15. Build Once, Promote Many

Recommended:

```text
Git Commit
    ↓
Build
    ↓
Test
    ↓
Scan
    ↓
Docker Hub
    ↓
Dev
    ↓
Staging
    ↓
Production
```

The goal is to promote the same tested artifact.

---

# 🔍 16. Image Scanning

Before publishing production images:

```text
Build
 ↓
Scan
 ↓
Review
 ↓
Push
```

Example:

```bash
trivy image myapp:1.0.0
```

Do not treat vulnerability scanning as a guarantee of security; combine it with secure configuration and runtime controls.

---

# 📦 17. Multi-Platform Images

Modern deployments may require:

```text
linux/amd64
linux/arm64
```

Buildx example:

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t <USERNAME>/myapp:1.0.0 \
  --push .
```

The registry can store the resulting multi-platform image index/manifest structure.

---

# 🧪 18. Hands-On Labs

### Lab 01 — Create Docker Hub Account

Create/configure your Docker Hub account and verify authentication.

### Lab 02 — Create Repository

Create:

```text
myapp
```

### Lab 03 — Build

```bash
docker build -t myapp:1.0.0 .
```

### Lab 04 — Tag

```bash
docker tag myapp:1.0.0 USER/myapp:1.0.0
```

### Lab 05 — Login

```bash
docker login
```

### Lab 06 — Push

```bash
docker push USER/myapp:1.0.0
```

### Lab 07 — Pull

```bash
docker pull USER/myapp:1.0.0
```

### Lab 08 — Versioning

Create:

```text
1.0.0
1.1.0
2.0.0
```

### Lab 09 — Commit SHA Tag

Create an image tagged with the Git commit SHA.

### Lab 10 — Digest

Identify and record the digest of a published image.

### Lab 11 — Private Repository

Create a private repository and test authorized pull access.

### Lab 12 — Docker Hub Token

Create a token for automation and store it securely.

### Lab 13 — GitHub Actions

Build and push automatically.

### Lab 14 — Trivy

Scan the image before push.

### Lab 15 — Multi-Platform

Publish:

```text
amd64
arm64
```

### Lab 16 — Kubernetes Pull

Pull the Docker Hub image from Kubernetes.

### Lab 17 — Image Promotion

Promote the same image through:

```text
dev → staging → production
```

### Lab 18 — Production Checklist

Review:

```text
Authentication
Authorization
Scanning
Tags
Digest
Secrets
```

---

# 🚨 19. Troubleshooting

## `unauthorized`

Check:

```text
Login
Username
Token
Repository
Permissions
```

## `denied`

Check:

```text
Namespace
Repository ownership
Token permissions
Repository visibility
```

## `manifest unknown`

Check:

```text
Repository
Tag
Architecture
```

## Kubernetes `ImagePullBackOff`

Check:

```text
Image name
Tag
Repository visibility
Registry credentials
Network
```

---

# 🏆 20. Production Checklist

```text
☑ Private production repository
☑ Token-based automation
☑ Secrets stored outside source code
☑ Least privilege
☑ Image scanning
☑ SBOM
☑ Controlled release tags
☑ Digest tracking
☑ Multi-platform where required
☑ Registry access review
☑ CI/CD audit trail
```

---

# ⚡ Cheat Sheet

```bash
# Build
docker build -t myapp:1.0.0 .

# Tag
docker tag myapp:1.0.0 USER/myapp:1.0.0

# Login
docker login

# Push
docker push USER/myapp:1.0.0

# Pull
docker pull USER/myapp:1.0.0

# Run
docker run --rm USER/myapp:1.0.0

# Inspect
docker image inspect USER/myapp:1.0.0

# Scan
trivy image USER/myapp:1.0.0

# Multi-platform
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t USER/myapp:1.0.0 \
  --push .
```

---

<div align="center">

# 🐳 Docker Hub

### Build → Tag → Secure → Push → Pull → Deploy

**VishwaTech Labs By Vishwanath Gowda H**

</div>
