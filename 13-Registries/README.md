<div align="center">

# 📦 Docker Registry — Complete Zero-to-Hero Masterclass

### 🚀 Build → Tag → Authenticate → Push → Pull → Deploy | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Registry-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/docker-hub/)
[![Docker Hub](https://img.shields.io/badge/Docker-Hub-2496ED?logo=docker&logoColor=white)](https://hub.docker.com/)
[![Amazon ECR](https://img.shields.io/badge/AWS-ECR-FF9900?logo=amazonaws&logoColor=white)](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)
[![Security](https://img.shields.io/badge/Registry-Security-success)](#-registry-security)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-blueviolet)](#-github-actions--registry)
[![Labs](https://img.shields.io/badge/Labs-25+-purple)](#-hands-on-labs)

**Learn how container images move from a developer workstation into a registry and finally into a deployment environment.**

[📘 Docker Registry](https://docs.docker.com/engine/reference/commandline/push/) •
[🐳 Docker Hub](https://docs.docker.com/docker-hub/) •
[☁️ Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) •
[🔐 Docker Login](https://docs.docker.com/reference/cli/docker/login/)

</div>

---

# 🎯 What You Will Learn

A registry is the distribution layer for container images.

The basic lifecycle:

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
Image
   │
   ├── tag
   ▼
Registry
   │
   ├── push
   │
   └── pull
   │
   ▼
Deployment
   │
   ├── Docker
   ├── Kubernetes
   ├── ECS
   └── Other runtimes
```

You will learn:

- What a container registry is
- Docker Hub
- Public vs private repositories
- Image naming
- Tags
- Digests
- `docker login`
- `docker tag`
- `docker push`
- `docker pull`
- `docker image inspect`
- Image immutability concepts
- Amazon ECR
- Private registry concepts
- Authentication
- CI/CD
- GitHub Actions
- Image promotion
- Registry security
- Vulnerability scanning concepts
- Multi-platform images
- Production workflows
- 25+ hands-on labs

---

# 🧠 1. What Is a Container Registry?

A container registry stores and distributes container images.

Concept:

```text
Build
  ↓
Image
  ↓
Registry
  ↓
Pull
  ↓
Runtime
```

Examples:

```text
Docker Hub
Amazon ECR
GitHub Container Registry
Google Artifact Registry
Azure Container Registry
Private/self-hosted registries
```

A registry is similar to a package repository, but it distributes OCI/container images and related artifacts.

---

# 🏗️ 2. Why Do We Need a Registry?

Without a registry:

```text
Developer Machine
       ↓
How does Kubernetes get the image?
       ↓
Manual copying
```

With a registry:

```text
Developer
   ↓
Build
   ↓
Push
   ↓
Registry
   ↓
Kubernetes / ECS / Docker
   ↓
Pull
```

This creates a repeatable image distribution process.

---

# 📦 3. Image Naming

A typical image reference:

```text
docker.io/username/myapp:1.0.0
```

Breakdown:

```text
docker.io
   │
   └── Registry

username
   │
   └── Namespace / account

myapp
   │
   └── Repository

1.0.0
   │
   └── Tag
```

A shorter Docker Hub reference:

```text
username/myapp:1.0.0
```

---

# 🏷️ 4. Image Tags

Examples:

```text
myapp:1.0.0
myapp:1.1.0
myapp:2.0.0
myapp:latest
```

Tags are human-friendly references.

Important:

> A tag is a mutable reference unless your registry/process enforces immutability.

Therefore production systems should use controlled versioning and/or immutable digests.

---

# 🔐 5. Image Digest

An image can be referenced by digest:

```text
myapp@sha256:<digest>
```

Concept:

```text
Tag
 │
 └── Human-friendly reference

Digest
 │
 └── Content-addressed immutable identifier
```

Example:

```text
myapp:1.0.0
        │
        ▼
sha256:abcd...
```

The exact digest is generated from the image manifest/content and must not be invented manually.

---

# 🆚 6. Tag vs Digest

| Tag | Digest |
|---|---|
| Human-friendly | Content-addressed |
| Easy to read | Long hash |
| Can move | Identifies exact content |
| Good for releases | Excellent for exact deployment |
| Example `1.2.0` | Example `sha256:...` |

Production deployments often benefit from digest pinning when exact artifact identity matters.

---

# 🐳 7. Docker Hub

Docker Hub is a public container registry service.

Official documentation:

[Docker Hub](https://docs.docker.com/docker-hub/)

Common workflow:

```text
Dockerfile
   ↓
docker build
   ↓
docker tag
   ↓
docker login
   ↓
docker push
   ↓
Docker Hub
```

---

# 🔑 8. Docker Login

```bash
docker login
```

Docker asks for registry credentials according to the configured authentication flow.

For Docker Hub, use a secure authentication method such as a personal access token where appropriate rather than putting a password into scripts.

See:

[Docker login](https://docs.docker.com/reference/cli/docker/login/)

---

# 🏷️ 9. Build an Image

Example:

```bash
docker build -t myapp:1.0.0 .
```

Check:

```bash
docker images
```

Suppose:

```text
myapp:1.0.0
```

---

# 🔖 10. Tag for Docker Hub

Suppose Docker Hub username is:

```text
vishwatechlabs
```

Tag:

```bash
docker tag myapp:1.0.0 \
  vishwatechlabs/myapp:1.0.0
```

Then:

```bash
docker images
```

You now have a registry-compatible reference.

Use your actual Docker Hub namespace.

---

# 🚀 11. Push Image

Login:

```bash
docker login
```

Push:

```bash
docker push \
  vishwatechlabs/myapp:1.0.0
```

Flow:

```text
Local Image
    │
    ▼
Registry Authentication
    │
    ▼
Push
    │
    ▼
Docker Hub Repository
```

---

# 📥 12. Pull Image

On another machine:

```bash
docker pull \
  vishwatechlabs/myapp:1.0.0
```

Run:

```bash
docker run --rm \
  vishwatechlabs/myapp:1.0.0
```

This is the core image distribution model.

---

# 🔍 13. Verify Image

List:

```bash
docker image ls
```

Inspect:

```bash
docker image inspect \
  vishwatechlabs/myapp:1.0.0
```

View metadata:

```bash
docker inspect \
  vishwatechlabs/myapp:1.0.0
```

---

# 🧪 14. Image Lifecycle

```text
Dockerfile
    ↓
Build
    ↓
Local Image
    ↓
Tag
    ↓
Login
    ↓
Push
    ↓
Registry
    ↓
Pull
    ↓
Deploy
```

This lifecycle is foundational for:

```text
GitHub Actions
Kubernetes
Terraform
Ansible
Cloud deployments
```

---

# 📚 15. Repository vs Image vs Tag

Understand the difference:

```text
Registry
   │
   └── Repository
          │
          ├── Tag: 1.0.0
          ├── Tag: 1.1.0
          └── Tag: 2.0.0
```

Example:

```text
docker.io/company/payment-api:1.4.2
```

Repository:

```text
company/payment-api
```

Tag:

```text
1.4.2
```

---

# 🏢 16. Public vs Private Registry

### Public

Anyone with access to the registry/repository can potentially pull according to repository permissions.

Good for:

```text
Open-source images
Public demos
Learning projects
```

### Private

Access is restricted.

Good for:

```text
Enterprise applications
Internal APIs
Proprietary software
Production images
```

---

# 🔐 17. Registry Authentication

Typical flow:

```text
Developer / CI
      │
      ▼
Authentication
      │
      ▼
Registry
      │
      ▼
Push / Pull permission
```

Authentication answers:

> Who are you?

Authorization answers:

> What are you allowed to do?

---

# 🛡️ 18. Registry Authorization

Examples:

```text
Developer
   ├── Pull
   └── Push

CI/CD
   ├── Pull
   └── Push

Production Runtime
   └── Pull
```

Use least privilege.

A production runtime normally does not need permission to push images.

---

# ☁️ 19. Amazon ECR

Amazon Elastic Container Registry (ECR) is AWS's managed container registry service.

Official documentation:

[Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)

Architecture:

```text
Developer / CI
      │
      ▼
    AWS ECR
      │
      ▼
ECS / EKS / EC2 / Other Runtime
```

---

# 🏗️ 20. Create an ECR Repository

AWS CLI example:

```bash
aws ecr create-repository \
  --repository-name myapp \
  --region us-east-1
```

Verify:

```bash
aws ecr describe-repositories \
  --repository-names myapp \
  --region us-east-1
```

Use the AWS account, region, and repository naming appropriate to your environment.

---

# 🔑 21. Authenticate Docker to ECR

A common AWS CLI flow:

```bash
aws ecr get-login-password \
  --region us-east-1 \
| docker login \
  --username AWS \
  --password-stdin \
  <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com
```

Then Docker can push/pull according to the IAM permissions of the authenticated principal.

---

# 🏷️ 22. Tag for ECR

Build:

```bash
docker build -t myapp:1.0.0 .
```

Tag:

```bash
docker tag \
  myapp:1.0.0 \
  <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/myapp:1.0.0
```

---

# 🚀 23. Push to ECR

```bash
docker push \
  <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/myapp:1.0.0
```

Flow:

```text
Local Image
    ↓
ECR Authentication
    ↓
ECR Repository
    ↓
Image Manifest + Layers
```

---

# 📥 24. Pull from ECR

Authenticate:

```bash
aws ecr get-login-password \
  --region us-east-1 \
| docker login \
  --username AWS \
  --password-stdin \
  <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com
```

Pull:

```bash
docker pull \
  <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/myapp:1.0.0
```

---

# 🔐 25. ECR IAM Concept

A typical CI principal needs permissions appropriate for:

```text
Authenticate
Check repository
Upload image layers
Push image manifest
```

A runtime pulling from private ECR needs only the permissions necessary to pull.

Avoid giving:

```text
Full AdministratorAccess
```

just to make a lab work.

Use least privilege.

---

# 🐙 26. GitHub Container Registry

GitHub also provides a container registry through GitHub Packages.

Official documentation:

[Working with the Container registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)

Typical reference:

```text
ghcr.io/OWNER/IMAGE:TAG
```

Example:

```bash
docker tag myapp:1.0.0 \
  ghcr.io/my-org/myapp:1.0.0
```

Authentication depends on the GitHub token/permissions used by the workflow or user.

---

# 🏢 27. Private Registry Concept

You can run a private registry service.

Concept:

```text
Developer
   │
   ▼
Private Registry
   │
   ├── team-a/app
   ├── team-b/api
   └── internal/worker
```

The official Docker Distribution project provides registry functionality.

Documentation:

[Docker Distribution](https://distribution.github.io/distribution/)

---

# 🔐 28. Registry Security

Production registry security should include:

```text
Authentication
Authorization
TLS
Least privilege
Private repositories
Image scanning
Audit logging
Retention policies
Immutable release strategy
Secret protection
Access reviews
```

---

# 🛡️ 29. Never Embed Secrets in Images

Bad:

```dockerfile
ENV AWS_ACCESS_KEY_ID=...
ENV AWS_SECRET_ACCESS_KEY=...
```

or:

```dockerfile
COPY .env /app/.env
```

This can expose credentials through image layers or application filesystem contents.

Better:

```text
Secret Manager
      ↓
Runtime / CI
      ↓
Application
```

Build-time secrets should also use appropriate BuildKit secret mechanisms.

---

# 🔍 30. Vulnerability Scanning

A registry/security platform may scan images for known vulnerabilities.

Concept:

```text
Image
  ↓
Scanner
  ↓
OS Packages
Dependencies
Libraries
  ↓
Findings
```

Common tools/ecosystems include:

```text
Docker Scout
Trivy
Grype
Registry-native scanners
```

Scanning does not prove an image is secure.

Treat findings according to:

```text
Severity
Exploitability
Exposure
Application usage
Fix availability
```

---

# 🧬 31. Image Layers in a Registry

An image is not necessarily uploaded as one giant file.

Concept:

```text
Image Manifest
     │
     ├── Layer 1
     ├── Layer 2
     ├── Layer 3
     └── Config
```

Registries store content-addressed artifacts and can reuse layers.

Therefore:

```text
Build
  ↓
Layers
  ↓
Push
  ↓
Registry
```

---

# ⚡ 32. Why Layer Reuse Matters

Suppose:

```text
Image A
├── OS layer
├── Dependencies
└── App
```

Then:

```text
Image B
├── Same OS layer
├── Same dependencies
└── Different App
```

The registry/build system can reuse identical content instead of transferring duplicate content unnecessarily.

This improves:

```text
Build performance
Push performance
Pull performance
Storage efficiency
```

---

# 🏷️ 33. Versioning Strategy

Recommended:

```text
myapp:1.0.0
myapp:1.1.0
myapp:2.0.0
```

CI can also create:

```text
myapp:<commit-sha>
```

Example concept:

```text
myapp:1.4.2
myapp:8f31c9a...
```

Use tags to identify releases and immutable references/digests when exact content identity is required.

---

# 🚫 34. The `latest` Problem

You may see:

```text
myapp:latest
```

But:

```text
latest
   ↓
Can move
   ↓
Different content tomorrow
```

Production deployment should not blindly depend on a moving tag.

Better:

```text
release tag
+
commit SHA
+
digest where appropriate
```

---

# 🧭 35. Image Promotion

A mature pipeline can promote the same built artifact through environments.

Concept:

```text
Build Once
    │
    ▼
Registry
    │
    ├── Development
    │
    ├── Staging
    │
    └── Production
```

Avoid rebuilding different artifacts for each environment when the goal is to promote the same tested artifact.

---

# 🏷️ 36. Environment Tags

One approach:

```text
myapp:dev
myapp:staging
myapp:prod
```

But remember tags can move.

A stronger model is:

```text
Artifact Digest
       │
       ├── dev reference
       ├── staging reference
       └── prod reference
```

All environments can be mapped to the exact same content digest.

---

# 🐙 37. GitHub Actions + Docker Hub

Example:

```yaml
name: Build and Push

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
            ${{ secrets.DOCKERHUB_USERNAME }}/myapp:latest
            ${{ secrets.DOCKERHUB_USERNAME }}/myapp:${{ github.sha }}
```

For production, add controlled release tagging, scanning, provenance/attestation where appropriate, and deployment gates.

---

# ☁️ 38. GitHub Actions + ECR

Concept:

```text
GitHub Actions
      │
      ▼
AWS Authentication
      │
      ▼
ECR Login
      │
      ▼
Buildx
      │
      ▼
Push Image
```

Prefer short-lived/federated AWS authentication such as GitHub Actions OIDC rather than storing long-lived AWS access keys when your organization supports it.

---

# 🔐 39. GitHub OIDC Concept

```text
GitHub Actions
      │
      │ OIDC token
      ▼
AWS IAM
      │
      ▼
Temporary credentials
      │
      ▼
ECR
```

Advantages:

```text
No long-lived AWS secret key
Short-lived credentials
IAM role-based access
Better CI security
```

---

# 🏗️ 40. Complete CI/CD Registry Flow

```text
Developer
   │
   ▼
Git Push
   │
   ▼
GitHub Actions
   │
   ├── Checkout
   ├── Test
   ├── Build
   ├── Scan
   ├── Tag
   └── Push
          │
          ▼
       Registry
          │
          ▼
      Deployment
          │
          ├── Kubernetes
          ├── ECS
          └── Docker
```

---

# 🧪 41. Hands-On Labs

## Lab 01 — Docker Hub Login

```bash
docker login
```

Authenticate securely.

---

## Lab 02 — Build Image

```bash
docker build -t myapp:1.0.0 .
```

---

## Lab 03 — Tag Image

```bash
docker tag \
  myapp:1.0.0 \
  <DOCKERHUB_USER>/myapp:1.0.0
```

---

## Lab 04 — Push

```bash
docker push \
  <DOCKERHUB_USER>/myapp:1.0.0
```

---

## Lab 05 — Pull From Another Machine

```bash
docker pull \
  <DOCKERHUB_USER>/myapp:1.0.0
```

---

## Lab 06 — Private Repository

Create a private repository.

Test:

```text
Login
Push
Logout
Pull
```

---

## Lab 07 — Tag Strategy

Create:

```text
1.0.0
1.1.0
latest
commit-sha
```

Explain the risks of mutable tags.

---

## Lab 08 — Digest

Push an image.

Inspect the registry-provided digest.

Deploy using digest.

---

## Lab 09 — ECR Repository

Create an ECR repository using AWS CLI.

---

## Lab 10 — ECR Authentication

Authenticate Docker to ECR.

---

## Lab 11 — Push to ECR

```bash
docker push ...
```

Verify in AWS.

---

## Lab 12 — Pull From ECR

Pull the private image from another authorized environment.

---

## Lab 13 — ECR IAM

Create a least-privilege role/policy suitable for an image-push workflow.

---

## Lab 14 — GitHub Container Registry

Build and push an image to GHCR using GitHub Actions.

---

## Lab 15 — GitHub Actions + Docker Hub

Create a CI workflow:

```text
Checkout
 ↓
Build
 ↓
Login
 ↓
Push
```

---

## Lab 16 — GitHub Actions + ECR

Use GitHub OIDC to authenticate to AWS.

Push image to ECR.

---

## Lab 17 — Multi-Tag Push

Push:

```text
release tag
commit SHA
```

from one build.

---

## Lab 18 — Image Scan

Scan an image.

Classify findings.

Document remediation.

---

## Lab 19 — Registry Security

Review:

```text
Authentication
Authorization
TLS
Secrets
Access
Retention
Scanning
```

---

## Lab 20 — Image Promotion

Implement:

```text
dev
 ↓
staging
 ↓
production
```

using the same artifact digest.

---

## Lab 21 — Kubernetes Pull

Push an image to a private registry.

Configure Kubernetes authentication/image pull access.

Deploy the image.

---

## Lab 22 — ECS Pull

Push an image to ECR.

Deploy it through an AWS container runtime/service.

---

## Lab 23 — Registry Failure

Simulate:

```text
Wrong credentials
Wrong repository
Wrong tag
Wrong region
```

Troubleshoot.

---

## Lab 24 — Immutable Deployment

Deploy by digest.

Record:

```text
Image
Digest
Deployment
```

---

## Lab 25 — Production Registry Challenge

Build:

```text
GitHub
 ↓
Actions
 ↓
Buildx
 ↓
Test
 ↓
Scan
 ↓
Push
 ↓
ECR
 ↓
Kubernetes/ECS
```

Document the complete workflow.

---

# 🚨 42. Troubleshooting

## `unauthorized`

Check:

```text
docker login
Registry
Username
Token
Repository permissions
```

---

## `denied: requested access`

Check:

```text
Repository name
Namespace
IAM/registry permissions
Authentication
```

---

## `manifest unknown`

Usually means the requested image reference does not exist.

Check:

```text
Repository
Tag
Registry
Region
```

---

## ECR Login Failure

Check:

```text
AWS CLI identity
Region
ECR endpoint
IAM permissions
Expired/invalid credentials
```

Useful:

```bash
aws sts get-caller-identity
```

---

## Image Pull Failure in Kubernetes

Check:

```text
Image reference
Registry access
imagePullSecrets / workload identity
IAM permissions
Network connectivity
Tag/digest
```

---

# 🔐 43. Production Registry Checklist

```text
☑ Private production repositories
☑ Least-privilege permissions
☑ TLS
☑ Strong authentication
☑ Short-lived CI credentials
☑ OIDC where supported
☑ Vulnerability scanning
☑ Image signing/verification strategy where required
☑ SBOM/provenance strategy
☑ Immutable release process
☑ Controlled tags
☑ Digest tracking
☑ Retention policy
☑ Audit logs
☑ Registry backups/availability strategy
☑ Pull permissions separated from push permissions
```

---

# 🎓 44. Interview Questions

## Beginner

1. What is a container registry?
2. Why do we need a registry?
3. What is Docker Hub?
4. What is an image repository?
5. What is an image tag?
6. What is an image digest?
7. How do you login to a registry?
8. How do you tag an image?
9. How do you push an image?
10. How do you pull an image?

## Intermediate

11. Tag vs digest?
12. Public vs private registry?
13. How does Docker Hub authentication work?
14. What is Amazon ECR?
15. How do you authenticate Docker to ECR?
16. What IAM permissions are needed to push to ECR?
17. Why should production not rely blindly on `latest`?
18. What is image promotion?
19. Why are image layers important?
20. How do you secure registry credentials?

## Advanced

21. Design GitHub Actions → ECR using OIDC.
22. Design a production image promotion pipeline.
23. How would you deploy an image by digest?
24. How would you secure a private registry?
25. How would you integrate image scanning?
26. How would you troubleshoot an ECR pull failure?
27. How would Kubernetes authenticate to a private registry?
28. Explain tags, manifests, layers, and digests.
29. How would you implement immutable image releases?
30. Design a complete enterprise container image supply chain.

---

# 🏆 45. Registry Mastery Checklist

Before moving forward:

- [ ] Registry concept
- [ ] Docker Hub
- [ ] Public/private repositories
- [ ] Image naming
- [ ] Tags
- [ ] Digests
- [ ] Login
- [ ] Push
- [ ] Pull
- [ ] Docker Hub token
- [ ] Amazon ECR
- [ ] ECR authentication
- [ ] IAM
- [ ] GHCR
- [ ] Private registries
- [ ] Image layers
- [ ] Image promotion
- [ ] GitHub Actions
- [ ] OIDC
- [ ] Image scanning
- [ ] Security
- [ ] Kubernetes pull access
- [ ] ECS pull access
- [ ] Troubleshooting
- [ ] Production workflow

---

# ⚡ 46. Registry Cheat Sheet

```bash
# Login
docker login

# Build
docker build -t myapp:1.0.0 .

# Tag
docker tag \
  myapp:1.0.0 \
  USER/myapp:1.0.0

# Push
docker push USER/myapp:1.0.0

# Pull
docker pull USER/myapp:1.0.0

# Inspect
docker image inspect USER/myapp:1.0.0

# List
docker image ls

# ECR identity
aws sts get-caller-identity

# ECR login
aws ecr get-login-password \
  --region REGION \
| docker login \
  --username AWS \
  --password-stdin \
  ACCOUNT.dkr.ecr.REGION.amazonaws.com

# ECR tag
docker tag \
  myapp:1.0.0 \
  ACCOUNT.dkr.ecr.REGION.amazonaws.com/myapp:1.0.0

# ECR push
docker push \
  ACCOUNT.dkr.ecr.REGION.amazonaws.com/myapp:1.0.0

# ECR pull
docker pull \
  ACCOUNT.dkr.ecr.REGION.amazonaws.com/myapp:1.0.0
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
05 Containers
       ↓
06 Dockerfile
       ↓
07 Dockerfile Instructions
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
13 Docker Registry          ← 🟢 YOU ARE HERE
       ↓
14 Docker Security
       ↓
15 Docker + GitHub Actions
```

## 👉 [14 — Docker Security](../14-Docker-Security/)

Next we move into production security:

```text
Docker Security
      │
      ├── Container Isolation
      ├── Non-Root
      ├── Capabilities
      ├── Seccomp
      ├── AppArmor / SELinux
      ├── Secrets
      ├── Image Scanning
      ├── Supply Chain Security
      ├── SBOM
      ├── Image Signing
      ├── Runtime Security
      ├── Docker Daemon Security
      ├── Network Security
      └── Production Hardening
```

---

<div align="center">

# 📦 Build Once. Store Securely. Promote with Confidence.

### VishwaTech Labs

**Docker • GitHub Actions • Terraform • Ansible • Kubernetes • DevOps • Cloud Security**

### By Vishwanath Gowda H

⭐ Build → Tag → Scan → Push → Promote → Deploy

</div>
