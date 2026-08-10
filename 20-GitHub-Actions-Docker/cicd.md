<div align="center">

# 🚀 Docker + GitHub Actions — Complete CI/CD Masterclass

### 🐳 Build → Test → Scan → SBOM → Push → Deploy | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/)
[![GitHub Actions](https://img.shields.io/badge/GitHub-Actions-2088FF?logo=githubactions&logoColor=white)](https://docs.github.com/en/actions)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-Production-success)](#-complete-cicd-pipeline)
[![Security](https://img.shields.io/badge/Security-Trivy-red)](https://trivy.dev/)
[![AWS](https://img.shields.io/badge/AWS-ECR-FF9900?logo=amazonaws&logoColor=white)](https://docs.aws.amazon.com/ecr/)
[![Labs](https://img.shields.io/badge/Labs-30+-purple)](#-hands-on-labs)

**Turn Docker knowledge into a real CI/CD pipeline using GitHub Actions.**

[📘 GitHub Actions](https://docs.github.com/en/actions) •
[🐳 Docker Build/Push](https://docs.docker.com/build/ci/github-actions/) •
[🔐 GitHub OIDC](https://docs.github.com/en/actions/concepts/security/openid-connect) •
[☁️ AWS ECR](https://docs.aws.amazon.com/ecr/)

</div>

---

# 🎯 What You Will Learn

This is the bridge between:

```text
Docker
   +
GitHub
   +
CI/CD
   +
Security
   +
Container Registry
```

Final architecture:

```text
Developer
    │
    ▼
Git Push
    │
    ▼
GitHub Repository
    │
    ▼
GitHub Actions
    │
    ├── Checkout
    ├── Lint
    ├── Unit Tests
    ├── Docker Build
    ├── Image Scan
    ├── SBOM
    ├── Sign / Attest
    └── Push
          │
          ▼
      Container Registry
          │
          ├── Docker Hub
          ├── GHCR
          └── AWS ECR
          │
          ▼
      Deployment
          │
          ├── Docker
          ├── ECS
          └── Kubernetes
```

---

# 🧠 1. What Is CI/CD?

## Continuous Integration — CI

Developers frequently push code.

The CI system automatically:

```text
Checkout
   ↓
Build
   ↓
Test
   ↓
Scan
   ↓
Report
```

## Continuous Delivery / Deployment — CD

After CI:

```text
Artifact
   ↓
Registry
   ↓
Deployment
   ↓
Environment
```

---

# 🏗️ 2. Why GitHub Actions + Docker?

Before automation:

```text
Developer
   ↓
docker build
   ↓
docker tag
   ↓
docker login
   ↓
docker push
```

After automation:

```text
git push
   ↓
GitHub Actions
   ↓
Everything automated
```

This provides:

```text
Repeatability
Consistency
Auditability
Automation
Security gates
Faster delivery
```

---

# 📁 3. Recommended Repository Structure

```text
docker-github-actions/
│
├── .github/
│   └── workflows/
│       ├── ci.yaml
│       ├── docker-build.yaml
│       ├── docker-publish.yaml
│       └── security.yaml
│
├── app/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── ...
│
├── tests/
│   └── ...
│
├── .dockerignore
├── compose.yaml
└── README.md
```

---

# ⚙️ 4. What Is GitHub Actions?

GitHub Actions is GitHub's automation platform.

Basic structure:

```text
Workflow
   │
   ├── Event
   │
   ├── Job
   │
   └── Steps
```

Example:

```yaml
name: CI

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - run: echo "Run tests here"
```

---

# 🔔 5. Workflow Trigger

Example:

```yaml
on:
  push:
    branches:
      - main
```

Meaning:

```text
Push to main
     ↓
Workflow starts
```

Other common triggers:

```text
pull_request
workflow_dispatch
schedule
release
workflow_call
```

Use only the triggers your project actually needs.

---

# 🧩 6. Jobs

Example:

```yaml
jobs:

  test:
    runs-on: ubuntu-latest

  build:
    runs-on: ubuntu-latest
```

Concept:

```text
Workflow
   │
   ├── test
   │
   └── build
```

Jobs are isolated execution units.

---

# 🔢 7. Steps

Example:

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v4

  - name: Run tests
    run: pytest
```

Each job contains steps.

---

# 🖥️ 8. Runner

Example:

```yaml
runs-on: ubuntu-latest
```

GitHub provides a hosted runner for the job.

Concept:

```text
GitHub Actions
      │
      ▼
Runner
      │
      ├── Git
      ├── Docker
      ├── Shell
      └── Build tools
```

---

# 🐳 9. Docker Build in GitHub Actions

Simple:

```yaml
- name: Build Docker image
  run: docker build -t myapp:${{ github.sha }} .
```

Tag:

```text
myapp:<commit-sha>
```

This gives every build a traceable identifier.

---

# 🔥 10. Buildx

For advanced Docker builds:

```yaml
- name: Set up Docker Buildx
  uses: docker/setup-buildx-action@v3
```

Buildx enables modern BuildKit-based workflows and can support:

```text
Caching
Multi-platform builds
Advanced outputs
Attestations
```

---

# 📦 11. Docker Login

Docker Hub example:

```yaml
- name: Login to Docker Hub
  uses: docker/login-action@v3
  with:
    username: ${{ secrets.DOCKERHUB_USERNAME }}
    password: ${{ secrets.DOCKERHUB_TOKEN }}
```

Never hard-code credentials.

---

# 🚀 12. Build and Push Action

Example:

```yaml
- name: Build and push
  uses: docker/build-push-action@v6
  with:
    context: .
    push: true
    tags: |
      myuser/myapp:${{ github.sha }}
      myuser/myapp:latest
```

For production, prefer controlled release tagging and avoid blindly relying on `latest`.

---

# 🏷️ 13. Image Tagging Strategy

Recommended tags can include:

```text
myapp:<commit-sha>
myapp:<release-version>
```

Example:

```text
myapp:8f31c9a
myapp:1.4.2
```

Use the exact SHA generated by GitHub.

Concept:

```text
Git Commit
    │
    ▼
Image Tag
    │
    ▼
Registry
```

---

# 🧬 14. Tag + Digest

A mature deployment strategy may track:

```text
Human-friendly release:
myapp:1.4.2

Exact artifact:
myapp@sha256:...
```

The digest should be captured from the registry/build output rather than manually invented.

---

# 🧪 15. CI Pipeline

Recommended:

```text
Push / PR
   ↓
Checkout
   ↓
Install dependencies
   ↓
Lint
   ↓
Unit Tests
   ↓
Build
   ↓
Image Scan
   ↓
SBOM
   ↓
Push
```

Do not push production artifacts before required quality/security gates pass.

---

# 🔍 16. Trivy in GitHub Actions

Example:

```yaml
- name: Scan image
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: myapp:${{ github.sha }}
    severity: HIGH,CRITICAL
    exit-code: '1'
```

For enterprise CI, pin third-party actions according to your organization's supply-chain policy rather than relying on mutable action references.

---

# 📦 17. SBOM in CI

Concept:

```text
Docker Image
     ↓
SBOM Generator
     ↓
SPDX / CycloneDX
     ↓
Artifact
```

Example with Trivy:

```bash
trivy image \
  --format spdx-json \
  -o sbom.json \
  myapp:${GITHUB_SHA}
```

Store the SBOM as a workflow artifact or attach it to your release process according to your requirements.

---

# ✍️ 18. Build Attestations

Modern Docker Buildx workflows can produce attestations/provenance.

Concept:

```text
Source
  ↓
Build
  ↓
Image
  ├── SBOM
  └── Provenance
```

This improves software supply-chain traceability.

---

# 🔐 19. GitHub Actions Permissions

Use minimal permissions.

Example:

```yaml
permissions:
  contents: read
```

If the workflow needs more permissions:

```yaml
permissions:
  contents: read
  packages: write
```

Only grant what is required.

---

# 🛡️ 20. GitHub Secrets

Examples:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
AWS_ROLE_ARN
```

Access:

```yaml
${{ secrets.DOCKERHUB_TOKEN }}
```

Never:

```yaml
run: echo "${{ secrets.DOCKERHUB_TOKEN }}"
```

Never print secrets into logs.

---

# ☁️ 21. AWS OIDC

Avoid long-lived AWS access keys when possible.

Architecture:

```text
GitHub Actions
      │
      │ OIDC token
      ▼
AWS IAM
      │
      ▼
Assumed Role
      │
      ▼
Temporary Credentials
      │
      ▼
ECR
```

Official documentation:

[GitHub OIDC with cloud providers](https://docs.github.com/en/actions/concepts/security/openid-connect)

---

# 🔑 22. AWS OIDC Trust Model

The IAM role should trust the appropriate GitHub OIDC provider and restrict:

```text
Organization
Repository
Branch/environment
Workflow claims
```

Avoid broad trust such as:

```text
Any GitHub repository
```

Use conditions that match your repository and deployment design.

---

# ☁️ 23. GitHub Actions → ECR

High-level workflow:

```text
GitHub
  ↓
Actions
  ↓
OIDC
  ↓
AWS IAM Role
  ↓
ECR Login
  ↓
Docker Build
  ↓
Docker Push
```

---

# 🧪 24. ECR Workflow Example

```yaml
name: Docker ECR

on:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: us-east-1

      - name: Login to ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Set up Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: |
            ${{ steps.login-ecr.outputs.registry }}/myapp:${{ github.sha }}
```

Replace:

```text
AWS_ROLE_ARN
AWS_REGION
repository name
```

with your environment.

---

# 🔐 25. Docker Hub Workflow

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

      - name: Login
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build and Push
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: |
            ${{ secrets.DOCKERHUB_USERNAME }}/myapp:${{ github.sha }}
```

---

# 🐙 26. GitHub Container Registry

Reference:

```text
ghcr.io/OWNER/IMAGE:TAG
```

A workflow can authenticate to GHCR using GitHub-provided authentication mechanisms and appropriate package permissions.

Example permissions may include:

```yaml
permissions:
  contents: read
  packages: write
```

---

# 🏗️ 27. Complete CI/CD Pipeline

```text
                  Developer
                      │
                      ▼
                  Git Push
                      │
                      ▼
                 GitHub Repo
                      │
                      ▼
               GitHub Actions
                      │
       ┌──────────────┼──────────────┐
       ▼              ▼              ▼
      Test          Build          Lint
       │              │              │
       └──────────────┼──────────────┘
                      ▼
                  Docker Image
                      │
             ┌────────┼────────┐
             ▼        ▼        ▼
           Scan      SBOM   Provenance
             │        │        │
             └────────┼────────┘
                      ▼
                  Push Registry
                      │
             ┌────────┼────────┐
             ▼        ▼        ▼
           ECR       GHCR    Docker Hub
                      │
                      ▼
                  Deployment
                      │
             ┌────────┼────────┐
             ▼        ▼        ▼
          Docker    ECS      Kubernetes
```

---

# 🔄 28. Build Once, Promote Many

Recommended model:

```text
Source
  ↓
Build
  ↓
Test
  ↓
Scan
  ↓
ONE IMAGE
  ↓
Development
  ↓
Staging
  ↓
Production
```

Avoid:

```text
Build dev
Build staging
Build production
```

when the goal is to promote the exact same tested artifact.

---

# 🏷️ 29. Release Workflow

Example:

```text
Developer
   ↓
Pull Request
   ↓
CI
   ↓
Merge
   ↓
Release Tag
   ↓
Build
   ↓
Scan
   ↓
Push
   ↓
Deploy
```

Release tag:

```text
v1.4.2
```

Image:

```text
myapp:1.4.2
```

---

# 🌍 30. Environment Strategy

Example:

```text
Development
    ↓
Staging
    ↓
Production
```

Use:

```text
Environment variables
Secrets
Deployment configuration
```

Do not build separate application binaries/images merely to change environment configuration unless there is a clear reason.

---

# 🔐 31. GitHub Environments

GitHub Environments can be used for environment-specific:

```text
Secrets
Variables
Protection rules
Approvals
Deployment controls
```

Concept:

```text
GitHub Actions
     │
     ├── dev
     ├── staging
     └── production
```

Production can require approval before deployment.

---

# 🚦 32. Pull Request Security

For pull requests:

```text
Checkout
 ↓
Lint
 ↓
Unit tests
 ↓
Build
 ↓
Scan
```

Avoid automatically granting powerful production credentials to untrusted pull-request workflows.

Design PR workflows carefully, especially for forked repositories.

---

# 🧱 33. Branch Protection

Recommended:

```text
main
 │
 ├── Pull Request
 ├── Required checks
 ├── Code review
 └── Merge
```

Security gates:

```text
Tests
Scan
Build
Review
```

---

# 🧪 34. Docker Compose in CI

You can use Compose for integration testing.

Example:

```bash
docker compose up -d
docker compose ps
docker compose exec backend pytest
docker compose down -v
```

Architecture:

```text
GitHub Runner
     │
     ▼
Compose
 ├── Backend
 ├── PostgreSQL
 └── Redis
```

This is especially useful for integration tests.

---

# 🏥 35. Healthcheck in CI

Example:

```yaml
services:
  db:
    image: postgres:17
    healthcheck:
      test:
        - CMD-SHELL
        - pg_isready -U postgres
      interval: 10s
      timeout: 5s
      retries: 5
```

Then integration tests can wait for the service to become ready.

Applications should still have retry logic where appropriate.

---

# ⚡ 36. Docker Build Cache

Buildx supports cache mechanisms.

Concept:

```text
First build
   ↓
Slow

Cached build
   ↓
Reuse previous layers
   ↓
Faster
```

Example:

```yaml
- name: Build and push
  uses: docker/build-push-action@v6
  with:
    context: .
    push: true
    tags: myapp:${{ github.sha }}
    cache-from: type=gha
    cache-to: type=gha,mode=max
```

Verify compatibility and cache behavior for your workflow.

---

# 🌍 37. Multi-Platform Images

You may need:

```text
linux/amd64
linux/arm64
```

Buildx can support multi-platform builds.

Example:

```yaml
- name: Build multi-platform image
  uses: docker/build-push-action@v6
  with:
    context: .
    platforms: linux/amd64,linux/arm64
    push: true
    tags: myapp:${{ github.sha }}
```

This is useful when deployments run on different CPU architectures.

---

# 🧪 38. Testing Strategy

Recommended layers:

```text
Unit Tests
    ↓
Integration Tests
    ↓
Docker Build
    ↓
Image Scan
    ↓
Container Smoke Test
    ↓
Push
    ↓
Deployment Test
```

Do not rely on a Docker build alone as proof that the application works.

---

# 🧪 39. Container Smoke Test

After build:

```bash
docker run -d \
  --name smoke \
  -p 8080:8080 \
  myapp:${GITHUB_SHA}
```

Test:

```bash
curl http://localhost:8080/health
```

Cleanup:

```bash
docker rm -f smoke
```

---

# 🔍 40. Security Gate

Example pipeline:

```text
Build
 ↓
Scan
 ↓
HIGH/CRITICAL findings?
 ├── YES → STOP
 └── NO
       ↓
     Push
```

Security gates should be designed around your vulnerability policy and exception process.

---

# 🧾 41. Artifact Management

Store:

```text
Docker Image
SBOM
Test reports
Scan reports
Provenance
Release metadata
```

Concept:

```text
Git Commit
    │
    ├── Image
    ├── SBOM
    ├── Scan
    └── Provenance
```

This creates traceability.

---

# 🔐 42. Secret Management Rules

Never:

```text
Commit secrets
Echo secrets
Put secrets in Dockerfile
Put credentials in image
Use long-lived cloud keys unnecessarily
```

Prefer:

```text
GitHub Secrets
GitHub OIDC
Cloud IAM roles
Secret managers
Short-lived credentials
```

---

# 🛡️ 43. Third-Party Action Security

Every action is code running in your CI environment.

Examples:

```yaml
uses: actions/checkout@v4
uses: docker/login-action@v3
```

Security practices:

```text
Review action source
Use trusted maintainers
Pin versions
Prefer immutable commit references for high-assurance environments
Review updates
Limit permissions
```

---

# 🧠 44. Runner Security

Hosted runners are ephemeral in common GitHub-hosted workflows.

Still protect:

```text
Secrets
Tokens
Build artifacts
Logs
Permissions
Dependencies
```

For self-hosted runners:

```text
Host security
Network isolation
Patch management
Runner lifecycle
Credential isolation
```

become especially important.

---

# 🚨 45. Common Failure — Docker Login

Check:

```text
Username
Token
Secret name
Registry
Permissions
```

Never print the secret to diagnose it.

---

# 🚨 46. Common Failure — ECR Access Denied

Check:

```bash
aws sts get-caller-identity
```

Then verify:

```text
OIDC trust
IAM role
Repository
Region
ECR permissions
```

---

# 🚨 47. Common Failure — Image Not Found

Check:

```text
Registry
Repository
Tag
Architecture
Authentication
```

Example:

```text
myapp:${{ github.sha }}
```

The same SHA tag must exist in the registry.

---

# 🚨 48. Common Failure — Permission Denied

Check:

```text
GitHub workflow permissions
Package permissions
AWS IAM
Registry permissions
```

Use least privilege, but make sure the workflow has exactly what it needs.

---

# 🚨 49. Common Failure — Build Works Locally but Fails in CI

Possible differences:

```text
OS
Architecture
Environment variables
Secrets
Docker version
Build context
Case sensitivity
Network
Cache
```

Reproduce with:

```text
same Dockerfile
same build context
same arguments
same platform
```

---

# 🚨 50. Common Failure — Tests Pass but Container Fails

Possible cause:

```text
Different runtime environment
Missing dependency
Wrong working directory
Wrong port
Missing environment variable
Permission issue
Healthcheck failure
```

Use:

```bash
docker run
docker logs
docker inspect
docker exec
```

to reproduce locally.

---

# 🧪 51. Hands-On Labs

## Lab 01 — First GitHub Actions Workflow

Create:

```text
.github/workflows/ci.yaml
```

Run:

```text
Checkout
Test
```

---

## Lab 02 — Docker Build

Build an image in GitHub Actions.

---

## Lab 03 — Docker Login

Authenticate to Docker Hub using GitHub Secrets.

---

## Lab 04 — Push Image

Push:

```text
commit SHA
```

to Docker Hub.

---

## Lab 05 — Pull Image

Pull the CI-created image locally.

---

## Lab 06 — Trivy Scan

Scan the image.

Fail on selected severities.

---

## Lab 07 — SBOM

Generate an SBOM.

Upload it as an artifact.

---

## Lab 08 — Build Cache

Add:

```text
cache-from
cache-to
```

Measure build performance.

---

## Lab 09 — Multi-Platform Build

Build:

```text
amd64
arm64
```

---

## Lab 10 — GitHub Environment

Create:

```text
dev
staging
prod
```

---

## Lab 11 — Protected Production

Require manual approval for production.

---

## Lab 12 — ECR Repository

Create an ECR repository.

---

## Lab 13 — GitHub OIDC

Configure:

```text
GitHub
 ↓
OIDC
 ↓
AWS IAM
```

---

## Lab 14 — Push to ECR

Build and push through Actions.

---

## Lab 15 — ECR Pull

Pull from ECR on an authorized machine.

---

## Lab 16 — Compose Integration Tests

Run:

```text
Backend
PostgreSQL
Redis
```

and execute integration tests.

---

## Lab 17 — Container Smoke Test

Start the image.

Call:

```text
/health
```

---

## Lab 18 — Security Gate

Fail CI when defined vulnerability thresholds are exceeded.

---

## Lab 19 — Image Promotion

Promote one exact image from:

```text
dev
 ↓
staging
 ↓
production
```

---

## Lab 20 — Release Tag

Push:

```text
v1.0.0
```

Build:

```text
myapp:1.0.0
```

---

## Lab 21 — Digest Deployment

Capture the image digest.

Deploy by digest.

---

## Lab 22 — Branch Protection

Require:

```text
CI
Code review
Security scan
```

before merge.

---

## Lab 23 — Pull Request Workflow

Run tests and security checks for PRs.

---

## Lab 24 — Fork Security

Study why powerful secrets should not be automatically exposed to untrusted PR workflows.

---

## Lab 25 — Third-Party Action Review

Review every action used in the workflow.

---

## Lab 26 — Self-Hosted Runner Security

Design a secure self-hosted runner architecture.

---

## Lab 27 — Failure Simulation

Break:

```text
Registry authentication
```

Troubleshoot.

---

## Lab 28 — Failure Simulation

Break:

```text
AWS IAM permissions
```

Troubleshoot.

---

## Lab 29 — Failure Simulation

Break:

```text
Docker build
```

Troubleshoot.

---

## Lab 30 — Enterprise CI/CD Project

Build:

```text
GitHub
 ↓
Actions
 ↓
Tests
 ↓
Docker Build
 ↓
Trivy
 ↓
SBOM
 ↓
Provenance
 ↓
ECR
 ↓
Deployment
```

Document the complete architecture.

---

# 🏢 52. Enterprise CI/CD Architecture

```text
                        DEVELOPER
                            │
                            ▼
                       GIT COMMIT
                            │
                            ▼
                     GitHub Repository
                            │
              ┌─────────────┴─────────────┐
              ▼                           ▼
         Pull Request                  Main
              │                           │
              ▼                           ▼
             CI                         CI/CD
              │                           │
      ┌───────┼────────┐          ┌───────┼────────┐
      ▼       ▼        ▼          ▼       ▼        ▼
    Test    Lint     Scan       Build    Scan     SBOM
                                      │
                                      ▼
                                  Provenance
                                      │
                                      ▼
                                    Sign
                                      │
                                      ▼
                                  Registry
                                      │
                              ┌───────┼───────┐
                              ▼       ▼       ▼
                             Dev   Staging   Prod
                                      │
                                      ▼
                              Approval / Policy
                                      │
                                      ▼
                                  Deployment
```

---

# 🔐 53. Production CI/CD Checklist

```text
SOURCE
☑ Protected branches
☑ Pull requests
☑ Code review
☑ Dependency management

GITHUB ACTIONS
☑ Minimal permissions
☑ Trusted actions
☑ Version pinning strategy
☑ Secure secrets
☑ OIDC where possible
☑ Protected environments

DOCKER
☑ Multi-stage builds
☑ Non-root
☑ Minimal images
☑ No secrets
☑ .dockerignore

SECURITY
☑ Image scanning
☑ SBOM
☑ Provenance
☑ Signing/verification
☑ Vulnerability gates

REGISTRY
☑ Private production repository
☑ Least privilege
☑ Controlled tags
☑ Digest tracking
☑ Retention policy

DEPLOYMENT
☑ Build once/promote many
☑ Environment approvals
☑ Health checks
☑ Rollback strategy
☑ Monitoring
```

---

# 🎓 54. Interview Questions

## Beginner

1. What is GitHub Actions?
2. What is CI/CD?
3. What is a workflow?
4. What is a job?
5. What is a step?
6. What is a runner?
7. How do you build Docker images in GitHub Actions?
8. How do you push an image?
9. What are GitHub Secrets?
10. What is Docker Buildx?

## Intermediate

11. How do you authenticate to Docker Hub?
12. How do you authenticate to ECR?
13. What is GitHub OIDC?
14. Why is OIDC better than long-lived AWS keys?
15. How do you implement image scanning?
16. What is an SBOM?
17. What is image provenance?
18. How do you implement Docker build caching?
19. How do you build multi-platform images?
20. How do GitHub Environments work?

## Advanced

21. Design GitHub Actions → ECR using OIDC.
22. Design a secure Docker CI/CD pipeline.
23. How would you protect production credentials?
24. How would you secure pull-request workflows?
25. How would you implement image promotion?
26. How would you deploy by digest?
27. How would you integrate SBOM and signing?
28. How would you secure third-party GitHub Actions?
29. How would you design a self-hosted runner securely?
30. Design an enterprise Docker supply-chain pipeline.

---

# ⚡ 55. GitHub Actions + Docker Cheat Sheet

```bash
# Build
docker build -t myapp:${GITHUB_SHA} .

# Run
docker run --rm myapp:${GITHUB_SHA}

# Scan
trivy image myapp:${GITHUB_SHA}

# Generate SBOM
trivy image \
  --format spdx-json \
  -o sbom.json \
  myapp:${GITHUB_SHA}

# Compose integration tests
docker compose up -d
docker compose ps
docker compose logs
docker compose down -v
```

GitHub Actions building blocks:

```yaml
name:
on:
permissions:
jobs:
runs-on:
steps:
uses:
run:
with:
env:
needs:
if:
```

---

# 🏆 56. Final Docker + GitHub Actions Mastery Checklist

Before moving into Terraform/Kubernetes/Ansible CI/CD:

- [ ] GitHub Actions fundamentals
- [ ] Workflows
- [ ] Events
- [ ] Jobs
- [ ] Steps
- [ ] Runners
- [ ] Docker Build
- [ ] Buildx
- [ ] Docker Login
- [ ] Docker Push
- [ ] Docker Pull
- [ ] Tags
- [ ] Digests
- [ ] Docker Hub
- [ ] GHCR
- [ ] ECR
- [ ] GitHub Secrets
- [ ] GitHub Environments
- [ ] OIDC
- [ ] IAM
- [ ] Trivy
- [ ] SBOM
- [ ] Provenance
- [ ] Image signing concepts
- [ ] Build cache
- [ ] Multi-platform builds
- [ ] Compose integration testing
- [ ] Security gates
- [ ] Artifact promotion
- [ ] Production approvals
- [ ] Rollback strategy
- [ ] CI/CD troubleshooting

---

# 🗺️ 57. Docker Zero-to-Hero — COMPLETE

```text
01 Fundamentals              🟢
       ↓
02 Installation              🟢
       ↓
03 Docker CLI                🟢
       ↓
04 Images                    🟢
       ↓
05 Containers                🟢
       ↓
06 Dockerfile                🟢
       ↓
07 Dockerfile Instructions   🟢
       ↓
08 Build Cache & Layers      🟢
       ↓
09 Multi-Stage Builds        🟢
       ↓
10 Docker Networking        🟢
       ↓
11 Docker Volumes            🟢
       ↓
12 Docker Compose            🟢
       ↓
13 Docker Registry           🟢
       ↓
14 Docker Security           🟢
       ↓
15 Docker + GitHub Actions   🟢
```

# 🎉 YOU COMPLETED THE DOCKER CORE!

You now have the foundation to move into:

```text
🐳 Docker
     │
     ▼
🚀 GitHub Actions
     │
     ▼
🏗️ Terraform
     │
     ▼
☸️ Kubernetes
     │
     ▼
⚙️ Ansible
```

---

# 🌟 Your 45-Day Course Architecture

A powerful teaching sequence:

```text
PHASE 1
Docker Fundamentals
        ↓
PHASE 2
Docker Advanced
        ↓
PHASE 3
Docker Security
        ↓
PHASE 4
GitHub Actions
        ↓
PHASE 5
Terraform
        ↓
PHASE 6
Kubernetes
        ↓
PHASE 7
Ansible
        ↓
PHASE 8
Enterprise CI/CD Project
```

Final project:

```text
Developer
    ↓
GitHub
    ↓
GitHub Actions
    ↓
Test
    ↓
Docker Build
    ↓
Trivy
    ↓
SBOM
    ↓
ECR
    ↓
Terraform
    ↓
AWS Infrastructure
    ↓
Kubernetes
    ↓
Ansible
    ↓
Production Application
```

---

<div align="center">

# 🚀 BUILD • AUTOMATE • SECURE • DEPLOY

### VishwaTech Labs

**Docker • GitHub Actions • Terraform • Ansible • Kubernetes • DevSecOps • Cloud Security**

### By Vishwanath Gowda H

## ⭐ From `git push` to Production!

</div>
