<div align="center">

# 🐳 Docker Multi-Stage Builds — Production Image Engineering

### 🚀 Zero to Hero | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Multi--Stage-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/build/building/multi-stage/)
[![Optimization](https://img.shields.io/badge/Image-Optimization-orange)](#-why-multi-stage-builds)
[![Security](https://img.shields.io/badge/Security-Minimal%20Runtime-success)](#-security-benefits)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-Production-blueviolet)](#-multi-stage-in-cicd)
[![Labs](https://img.shields.io/badge/Labs-20+-purple)](#-hands-on-labs)

**Build with everything you need. Ship only what you need.**

[📘 Multi-stage Builds](https://docs.docker.com/build/building/multi-stage/) •
[🏗️ Docker Build](https://docs.docker.com/build/) •
[🔐 Docker Scout](https://docs.docker.com/scout/)

</div>

---

# 🎯 What You Will Learn

A production application often needs many tools to **build** an application:

```text
Compiler
Package Manager
SDK
Development Dependencies
Source Code
Testing Tools
Linters
Build Tools
```

But the final application may need only:

```text
Runtime
Application Artifact
Required Runtime Dependencies
Configuration
```

Multi-stage builds separate these concerns.

```text
┌──────────────────────────────┐
│        BUILD STAGE           │
│                              │
│ Source Code                  │
│ Compiler / SDK               │
│ Dev Dependencies             │
│ Tests / Build Tools          │
│                              │
│       Build Artifact         │
└──────────────┬───────────────┘
               │
               │ COPY --from
               ▼
┌──────────────────────────────┐
│       RUNTIME STAGE          │
│                              │
│ Minimal Runtime              │
│ Application Artifact         │
│ Runtime Dependencies         │
│                              │
└──────────────────────────────┘
```

---

# 🧠 1. What Is a Multi-Stage Build?

A multi-stage Dockerfile contains multiple `FROM` instructions.

Example:

```dockerfile
FROM golang:1.24 AS builder

WORKDIR /src
COPY . .
RUN go build -o /out/app .

FROM alpine:3.20

COPY --from=builder /out/app /app

CMD ["/app"]
```

There are two stages:

```text
Stage 1 → builder
Stage 2 → runtime
```

The final image is created from the final stage unless another target is selected.

---

# 🔥 2. Why Multi-Stage Builds?

Without multi-stage builds:

```text
Runtime Image
│
├── Compiler
├── SDK
├── Build tools
├── Source code
├── Test tools
├── Dev dependencies
└── Application
```

With multi-stage builds:

```text
Runtime Image
│
├── Runtime
├── Required runtime dependencies
└── Application artifact
```

Benefits can include:

- Smaller final images
- Fewer unnecessary packages
- Reduced attack surface
- Cleaner separation of build/runtime
- Better production image design
- Easier CI/CD pipelines

> A smaller image is not automatically a secure image, but removing unnecessary software can reduce exposure.

---

# 🧱 3. Basic Multi-Stage Syntax

```dockerfile
FROM base-image AS builder

# Build application

FROM runtime-image

COPY --from=builder /path/to/artifact /destination/

CMD ["..."]
```

Key instruction:

```dockerfile
COPY --from=builder
```

It copies files from the named build stage into the current stage.

---

# 🏗️ 4. Stage Names

You can name a stage:

```dockerfile
FROM node:22 AS builder
```

Then:

```dockerfile
COPY --from=builder /app/dist /usr/share/nginx/html
```

Prefer meaningful names:

```text
builder
test
runtime
production
development
```

Avoid unclear:

```text
stage1
stage2
stage3
```

Meaningful names make Dockerfiles easier to maintain.

---

# 🧭 5. Stage Flow

Typical:

```text
SOURCE
  │
  ▼
BUILDER
  │
  ├── Install dependencies
  ├── Compile
  ├── Test
  └── Package
  │
  ▼
ARTIFACT
  │
  ▼
RUNTIME
  │
  ▼
PRODUCTION IMAGE
```

---

# 🐹 6. Go — Classic Multi-Stage Example

Go is one of the best demonstrations because the compiled binary can be copied into a minimal runtime image.

```dockerfile
FROM golang:1.24 AS builder

WORKDIR /src

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -o /out/app .

FROM alpine:3.20

COPY --from=builder /out/app /app

CMD ["/app"]
```

Build:

```bash
docker build -t go-app:1.0 .
```

Run:

```bash
docker run --rm go-app:1.0
```

---

# 🔬 7. Go Multi-Stage Architecture

```text
golang image
     │
     ├── Go compiler
     ├── SDK
     ├── Dependencies
     └── Source
          │
          ▼
       Binary
          │
          ▼
     alpine image
          │
          └── Binary
```

The compiler does not need to be part of the final runtime image.

---

# 🟨 8. Node.js — Build Stage + Nginx

For a frontend application:

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

CMD ["nginx", "-g", "daemon off;"]
```

Architecture:

```text
Node.js
  │
  ├── npm
  ├── source
  ├── dependencies
  └── build tools
       │
       ▼
     dist/
       │
       ▼
Nginx Runtime
       │
       └── static files
```

---

# 🐍 9. Python — Important Reality

Python applications are different from Go.

You generally cannot simply copy one compiled binary.

You need to consider:

```text
Python runtime
Application code
Python dependencies
Native libraries
System libraries
```

A multi-stage build can still help separate:

```text
Build dependencies
```

from:

```text
Runtime dependencies
```

---

# 🐍 10. Python Multi-Stage Example

Example pattern:

```dockerfile
FROM python:3.12-slim AS builder

WORKDIR /build

RUN python -m venv /opt/venv

COPY requirements.txt .

RUN /opt/venv/bin/pip install --no-cache-dir -r requirements.txt


FROM python:3.12-slim AS runtime

ENV PATH="/opt/venv/bin:$PATH" \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

COPY --from=builder /opt/venv /opt/venv
COPY . .

RUN useradd --create-home appuser \
    && chown -R appuser:appuser /app

USER appuser

EXPOSE 8000

CMD ["python", "app.py"]
```

> This pattern should be tested carefully when dependencies contain native extensions because build-time and runtime system libraries must be compatible.

---

# ☕ 11. Java Maven Multi-Stage

Example:

```dockerfile
FROM maven:3.9-eclipse-temurin-21 AS builder

WORKDIR /build

COPY pom.xml .
RUN mvn dependency:go-offline

COPY src ./src

RUN mvn clean package -DskipTests


FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=builder /build/target/app.jar /app/app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

Architecture:

```text
Maven + JDK
     │
     ▼
app.jar
     │
     ▼
JRE/runtime
     │
     ▼
Production
```

---

# 🟦 12. Java Gradle Example

```dockerfile
FROM gradle:8-jdk21 AS builder

WORKDIR /build

COPY build.gradle settings.gradle ./
COPY gradle ./gradle

RUN gradle dependencies --no-daemon

COPY src ./src

RUN gradle build --no-daemon


FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=builder /build/build/libs/app.jar /app/app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

Adapt the paths and Gradle output filename to the actual project.

---

# 🔵 13. .NET Multi-Stage Example

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS builder

WORKDIR /src

COPY . .

RUN dotnet restore

RUN dotnet publish \
    -c Release \
    -o /out \
    --no-restore


FROM mcr.microsoft.com/dotnet/aspnet:8.0

WORKDIR /app

COPY --from=builder /out .

EXPOSE 8080

ENTRYPOINT ["dotnet", "MyApp.dll"]
```

Concept:

```text
SDK
 ↓
restore
 ↓
build
 ↓
publish
 ↓
ASP.NET runtime
```

---

# 🧱 14. Named Runtime Stage

You can explicitly name the final stage:

```dockerfile
FROM node:22 AS builder

# Build...

FROM nginx:stable-alpine AS production

COPY --from=builder /app/dist /usr/share/nginx/html
```

This makes the Dockerfile easier to reason about and allows target selection.

---

# 🎯 15. Build a Specific Stage

Suppose:

```dockerfile
FROM node:22 AS development
...
FROM node:22 AS builder
...
FROM nginx:stable-alpine AS production
...
```

You can build a target:

```bash
docker build \
  --target development \
  -t myapp:dev .
```

Or:

```bash
docker build \
  --target production \
  -t myapp:prod .
```

This is useful for development/debugging workflows.

---

# 🧪 16. Development + Test + Production Stages

Advanced pattern:

```text
           Dockerfile
               │
       ┌───────┼────────┐
       ▼       ▼        ▼
 development  test   production
       │       │        │
       ▼       ▼        ▼
     Local   CI test  Runtime
```

Example structure:

```dockerfile
FROM python:3.12-slim AS base

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt


FROM base AS test

COPY . .
RUN pytest


FROM base AS production

COPY . .

RUN useradd --create-home appuser \
    && chown -R appuser:appuser /app

USER appuser

CMD ["python", "app.py"]
```

Build test target:

```bash
docker build --target test -t app:test .
```

Build production:

```bash
docker build --target production -t app:prod .
```

---

# 🧪 17. Testing Inside a Build

A test stage can make CI/CD easier:

```dockerfile
FROM node:22 AS test

WORKDIR /app

COPY package*.json .
RUN npm ci

COPY . .

RUN npm test
```

Then:

```bash
docker build --target test -t app:test .
```

If tests fail:

```text
docker build
     ↓
test stage
     ↓
RUN npm test
     ↓
FAIL
     ↓
CI pipeline fails
```

---

# 🧹 18. What Should NOT Enter Runtime?

Avoid copying:

```text
Compilers
SDKs
Source maps where not needed
Build caches
Test frameworks
Linters
Package managers
Development dependencies
Temporary files
Credentials
```

Instead:

```text
Runtime
+
Required application
+
Required runtime dependencies
```

---

# 🔐 19. Security Benefits

Multi-stage builds can reduce the final image's attack surface.

Example:

```text
Builder
├── gcc
├── git
├── make
├── compiler
├── SDK
├── source
└── tests

             ↓ COPY artifact

Runtime
└── application
```

But remember:

> Multi-stage builds reduce unnecessary content; they do not replace image scanning, dependency management, runtime hardening, or secure configuration.

---

# 📏 20. Image Size Comparison

Suppose:

```text
Single-stage:
1.2 GB

Multi-stage:
180 MB
```

Why?

```text
Removed:
Compiler
SDK
Build cache
Dev dependencies
Source/build tooling
```

The exact size depends on the application and base image.

Always measure:

```bash
docker image ls
```

and:

```bash
docker history myapp:1.0
```

---

# 🔍 21. `docker history`

Check image layers:

```bash
docker history myapp:1.0
```

Look for:

```text
Large layers
Unnecessary files
Package installation
Application copy
```

For deeper image analysis, use appropriate image inspection/scanning tools.

---

# 🧬 22. Artifact-Only Transfer

The key multi-stage principle:

```text
BUILD
   │
   ├── source
   ├── dependencies
   ├── tools
   └── compiler
        │
        ▼
     artifact
        │
        ▼
RUNTIME
   │
   └── artifact
```

Example:

```dockerfile
COPY --from=builder /out/app /app
```

This is much cleaner than copying the entire builder filesystem.

---

# 🆚 23. Single-Stage vs Multi-Stage

| Single Stage | Multi Stage |
|---|---|
| Build + runtime together | Build/runtime separated |
| Often larger | Often smaller |
| More build tools in final image | Build tools can stay out |
| Simpler initially | More structured |
| Can increase attack surface | Can reduce unnecessary content |
| Good for simple cases | Excellent for production builds |

Multi-stage is a design tool, not a requirement for every Dockerfile.

---

# ⚡ 24. Multi-Stage + Build Cache

Combine both concepts:

```dockerfile
FROM node:22 AS builder

WORKDIR /app

COPY package*.json .
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:stable-alpine

COPY --from=builder /app/dist /usr/share/nginx/html
```

Now:

```text
Dependency cache
      +
Build cache
      +
Separate runtime
      ↓
Fast + small production image
```

---

# 🏗️ 25. Multi-Stage + BuildKit

Modern Docker builds can use BuildKit features such as:

```text
Cache mounts
Secrets
SSH mounts
Parallel build work
Advanced builders
```

Example:

```dockerfile
# syntax=docker/dockerfile:1

FROM node:22 AS builder

WORKDIR /app

COPY package*.json .

RUN --mount=type=cache,target=/root/.npm \
    npm ci

COPY . .

RUN npm run build

FROM nginx:stable-alpine

COPY --from=builder /app/dist /usr/share/nginx/html
```

---

# 🔑 26. Secrets in Multi-Stage Builds

Bad:

```dockerfile
ARG PRIVATE_TOKEN
RUN git clone https://token@example.com/private-repo.git
```

Even if you do not copy the source into the final stage, sensitive information may still be exposed through build metadata, logs, or intermediate build data.

Use BuildKit secret/SSH mechanisms where appropriate.

See:

👉 [Docker Build secrets](https://docs.docker.com/build/building/secrets/)

---

# 🛡️ 27. Runtime User

The final stage should define an appropriate runtime user.

Example:

```dockerfile
FROM alpine:3.20

RUN adduser -D appuser

COPY --from=builder /out/app /app

USER appuser

CMD ["/app"]
```

Check:

```bash
docker run --rm myapp id
```

Expected concept:

```text
uid != 0
```

---

# 🌐 28. Runtime Dependencies Matter

A common mistake:

```text
Build succeeds
       ↓
Runtime fails
```

Why?

```text
Builder has required libraries
Runtime does not
```

Example:

```text
Native Python package
     ↓
Needs shared library
     ↓
Runtime image missing library
     ↓
Import/startup failure
```

Always test the **final runtime stage**, not only the builder.

---

# 🚨 29. Common Multi-Stage Failure

Example:

```dockerfile
FROM golang:1.24 AS builder

RUN go build -o /out/app .

FROM alpine:3.20

COPY --from=builder /out/app /app

CMD ["/app"]
```

If the binary depends on unavailable shared libraries:

```text
Container starts
      ↓
Executable fails
      ↓
Runtime dependency missing
```

For Go, build configuration such as:

```bash
CGO_ENABLED=0
```

may be appropriate for certain applications, but only when the application and dependencies support it.

---

# 🧪 30. Final Image Testing

Never assume:

```text
Builder passed
=
Production image works
```

Test:

```bash
docker build -t myapp:test .
docker run --rm myapp:test
```

Then test:

```text
Startup
Health
Ports
Application behavior
Dependencies
Permissions
Signals
```

---

# 🏭 31. Production Pipeline

A mature pipeline:

```text
Developer Push
      │
      ▼
GitHub Actions
      │
      ▼
Buildx / BuildKit
      │
      ├── Restore Cache
      │
      ▼
Builder Stage
      │
      ├── Dependencies
      ├── Build
      └── Tests
      │
      ▼
Runtime Stage
      │
      ▼
Security Scan
      │
      ▼
Image Tag
      │
      ▼
Registry
      │
      ▼
Kubernetes / ECS / Runtime
```

---

# 🐙 32. GitHub Actions Example

A simplified workflow:

```yaml
name: Docker Build

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build
        uses: docker/build-push-action@v6
        with:
          context: .
          push: false
          tags: myapp:${{ github.sha }}
```

For a production workflow, add:

```text
Registry login
Cache
Tests
Scanning
SBOM/attestation as appropriate
Push
Deployment
```

---

# 🏷️ 33. Tagging Multi-Stage Images

Good:

```bash
docker build -t myapp:1.0.0 .
docker build -t myapp:${GIT_SHA} .
```

Useful production references:

```text
Version tag
Commit SHA
Release tag
```

Avoid depending only on:

```text
latest
```

for production deployment identity.

---

# 🧪 34. Hands-On Labs

## Lab 01 — Two Stages

Create:

```text
builder
runtime
```

Copy a simple artifact between them.

---

## Lab 02 — Go Multi-Stage

Build a Go binary.

Runtime:

```text
alpine
```

Measure image size.

---

## Lab 03 — Node + Nginx

Build a frontend using Node.

Serve the final `dist` directory with Nginx.

---

## Lab 04 — Python Multi-Stage

Build Python dependencies in one stage.

Copy the virtual environment into runtime.

Test native dependencies.

---

## Lab 05 — Java Maven

Build a JAR using Maven.

Run using a JRE/runtime image.

---

## Lab 06 — .NET

Build with SDK.

Run with ASP.NET runtime image.

---

## Lab 07 — Development Target

Create:

```text
development
test
production
```

targets.

---

## Lab 08 — Test Stage

Run:

```text
unit tests
```

during Docker build.

---

## Lab 09 — Cache + Multi-Stage

Optimize dependency layers.

Measure:

```text
First build
Cached build
Source change
Dependency change
```

---

## Lab 10 — Runtime Security

Make the final stage:

```text
Non-root
Minimal
No compiler
No dev tools
```

---

## Lab 11 — Runtime Dependency Failure

Intentionally remove a required library.

Diagnose the failure.

---

## Lab 12 — Image History

Compare:

```bash
docker history single-stage:1.0
docker history multi-stage:1.0
```

---

## Lab 13 — Image Size

Compare:

```text
Single-stage
Multi-stage
```

Record:

```text
Image Size
Build Time
Startup Time
```

---

## Lab 14 — Build Target

Build:

```bash
docker build --target test -t app:test .
```

Then:

```bash
docker build --target production -t app:prod .
```

---

## Lab 15 — BuildKit Cache Mount

Add a package-manager cache mount.

Measure repeated builds.

---

## Lab 16 — Build Secret

Use a BuildKit secret during a controlled private dependency build.

Verify it is not intentionally embedded in the final image.

---

## Lab 17 — Multi-Platform

Build:

```text
amd64
arm64
```

using buildx.

---

## Lab 18 — GitHub Actions

Build the multi-stage image in CI.

---

## Lab 19 — Scan

Build final image.

Run your chosen image vulnerability scanner.

Fix meaningful findings.

---

## Lab 20 — Production Challenge

Create a production image with:

```text
☑ Multi-stage
☑ Cache optimization
☑ Non-root
☑ Minimal runtime
☑ No secrets
☑ Health strategy
☑ Versioned tag
☑ CI build
☑ Security scan
```

---

# 🚨 35. Troubleshooting Guide

## `COPY --from` fails

Check:

```text
Stage name
Artifact path
Build output
File permissions
```

---

## Runtime executable not found

Check:

```text
Binary path
Architecture
Dynamic libraries
Interpreter
Permissions
```

---

## Python import failure

Check:

```text
Python version
Virtual environment
Native libraries
Installed dependencies
PATH
```

---

## Java JAR missing

Check:

```text
Maven output path
Artifact name
COPY --from path
```

---

## Node frontend shows blank page

Check:

```text
Build output directory
Nginx root
Asset paths
SPA routing
```

---

# 🧠 36. Interview Questions

## Beginner

1. What is a multi-stage Docker build?
2. Why use multiple `FROM` instructions?
3. What does `COPY --from` do?
4. What is a builder stage?
5. What is a runtime stage?
6. Why are multi-stage builds useful?
7. Can a Dockerfile have multiple stages?
8. Can you name a stage?
9. Can you build a specific stage?
10. Does the final image include every stage?

## Intermediate

11. How do multi-stage builds reduce image size?
12. Why are Go applications a good example?
13. How do you build Node frontend + Nginx?
14. How do you handle Python dependencies?
15. What is a test stage?
16. What is `--target`?
17. How does cache work across stages?
18. What runtime dependency problems can occur?
19. Why should the final stage be tested separately?
20. How do you run the final stage as non-root?

## Advanced

21. Design a production multi-stage build for Java.
22. Design one for Python with native dependencies.
23. How would you combine multi-stage builds with BuildKit cache mounts?
24. How would you securely access private dependencies?
25. How would you integrate multi-stage builds into GitHub Actions?
26. How would you debug an artifact that works in the builder but fails in runtime?
27. How would you optimize a 1 GB image into a smaller runtime image?
28. How do multi-platform builds affect multi-stage design?
29. How do you decide whether Alpine, Debian slim, distroless, or another runtime base is appropriate?
30. Explain a complete production pipeline using multi-stage Docker builds.

---

# 🏆 37. Production Checklist

```text
☑ Builder stage separated
☑ Runtime stage minimal
☑ Only required artifacts copied
☑ Dependency strategy understood
☑ Runtime libraries verified
☑ Non-root user
☑ No secrets
☑ No compiler/SDK in runtime
☑ Cache optimized
☑ Health strategy
☑ Image scanned
☑ Versioned image tag
☑ Final stage tested
☑ CI/CD integrated
☑ Rollback strategy
```

---

# ⚡ 38. Multi-Stage Cheat Sheet

```dockerfile
# Builder
FROM build-image AS builder

WORKDIR /src

COPY dependency-files ./
RUN install-dependencies

COPY . .
RUN build-command


# Runtime
FROM runtime-image

COPY --from=builder /artifact /app/artifact

USER appuser

EXPOSE 8080

ENTRYPOINT ["application"]
```

Build:

```bash
docker build -t myapp:1.0 .
```

Build target:

```bash
docker build \
  --target builder \
  -t myapp:builder .
```

Inspect:

```bash
docker history myapp:1.0
```

Run:

```bash
docker run --rm myapp:1.0
```

---

# 🗺️ 39. What's Next?

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
09 Multi-Stage Builds      ← 🟢 YOU ARE HERE
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

## 👉 [10 — Docker Networking](../10-Docker-Networking/)

Next we enter one of the most important real-world Docker topics:

```text
Docker Networking
      │
      ├── Bridge
      ├── Host
      ├── None
      ├── User-defined Networks
      ├── DNS
      ├── Container-to-Container
      ├── Port Publishing
      ├── Network Isolation
      ├── Frontend → Backend
      ├── Backend → Database
      └── Production Network Design
```

---

<div align="center">

# 🐳 Build Big. Ship Small.

### VishwaTech Labs

**Docker • GitHub Actions • Terraform • Ansible • Kubernetes • DevOps • Cloud Security**

### By Vishwanath Gowda H

⭐ Build → Test → Extract → Secure → Ship

</div>
