<div align="center">

# 🔐 Docker Security — Complete Zero-to-Hero Masterclass

### 🛡️ Secure the Image → Container → Host → Network → Supply Chain | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Security-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/engine/security/)
[![OWASP](https://img.shields.io/badge/OWASP-Container%20Security-000000?logo=owasp&logoColor=white)](https://owasp.org/)
[![Trivy](https://img.shields.io/badge/Scanner-Trivy-1904DA)](https://trivy.dev/)
[![SBOM](https://img.shields.io/badge/Supply%20Chain-SBOM-success)](#-sbom)
[![Labs](https://img.shields.io/badge/Labs-30+-purple)](#-hands-on-labs)

**Learn how to harden Docker images, containers, networks, secrets, the Docker daemon, and the container software supply chain.**

[📘 Docker Security](https://docs.docker.com/engine/security/) •
[🔍 Docker Scout](https://docs.docker.com/scout/) •
[🛡️ Trivy](https://trivy.dev/) •
[📦 OCI](https://opencontainers.org/)

</div>

---

# 🎯 What You Will Learn

Docker security is not one command.

It is a layered security model:

```text
                🔐 Docker Security
                       │
       ┌───────────────┼────────────────┐
       ▼               ▼                ▼
    Image          Container          Host
       │               │                │
       ├── Base       ├── User         ├── Docker daemon
       ├── Packages   ├── Capabilities ├── Kernel
       ├── Secrets    ├── Seccomp      ├── Filesystem
       └── Scan       ├── AppArmor     └── Access
                      └── Resources
                       │
       ┌───────────────┼────────────────┐
       ▼               ▼                ▼
    Network         Secrets          Supply Chain
       │               │                │
       ├── Ports       ├── Runtime     ├── SBOM
       ├── DNS         ├── CI/CD       ├── Signing
       └── Isolation   └── Vaults      └── Provenance
```

You will learn:

- Docker security fundamentals
- Threat modeling
- Image hardening
- Minimal base images
- Non-root containers
- Linux capabilities
- Privileged containers
- Seccomp
- AppArmor / SELinux concepts
- Read-only filesystems
- Resource limits
- Secrets
- Network security
- Docker socket security
- Docker daemon security
- Image scanning
- Trivy
- SBOM
- Image signing concepts
- Software supply chain security
- CI/CD security
- Runtime security
- Kubernetes security connection
- 30+ hands-on labs
- Production hardening

---

# 🧠 1. Docker Security Mental Model

Think in layers:

```text
Application
    ↓
Container
    ↓
Image
    ↓
Docker Engine
    ↓
Host OS
    ↓
Kernel
    ↓
Hardware / Cloud
```

A vulnerability at any layer can affect the overall security posture.

Therefore:

```text
Secure Image
+
Secure Runtime
+
Secure Host
+
Secure Network
+
Secure Supply Chain
=
Strong Container Security
```

---

# 🏗️ 2. Docker Security Threat Model

Potential attack paths:

```text
Attacker
   │
   ├── Vulnerable application
   │
   ├── Vulnerable dependency
   │
   ├── Malicious image
   │
   ├── Exposed secret
   │
   ├── Excessive container privileges
   │
   ├── Docker socket access
   │
   ├── Host vulnerability
   │
   └── Network exposure
```

Security objective:

```text
Reduce attack surface
       ↓
Limit privileges
       ↓
Limit blast radius
       ↓
Detect attacks
       ↓
Recover safely
```

---

# 🔐 3. Principle of Least Privilege

Give only the permissions required.

Bad:

```text
Container
   ↓
root
+
privileged
+
all capabilities
+
host filesystem
```

Better:

```text
Container
   ↓
non-root user
+
minimal capabilities
+
restricted filesystem
+
private network
```

Least privilege is one of the most important container security principles.

---

# 👤 4. Root vs Non-Root Containers

A container process can run as root inside the container.

Example:

```bash
docker run --rm alpine:3.20 id
```

You may see:

```text
uid=0(root)
```

Root inside a container is not identical to unrestricted root on the host, because container isolation mechanisms exist.

However:

> Running workloads as root increases risk and should be avoided when the application does not require it.

---

# 🛡️ 5. Run as Non-Root

Dockerfile example:

```dockerfile
FROM python:3.12-slim

RUN useradd --create-home appuser

WORKDIR /app

COPY . .

RUN chown -R appuser:appuser /app

USER appuser

CMD ["python", "app.py"]
```

Verify:

```bash
docker run --rm myapp id
```

Expected concept:

```text
uid != 0
```

---

# 🧑‍💻 6. Why Non-Root Matters

Imagine:

```text
Application vulnerability
        ↓
Attacker gains code execution
        ↓
Process privileges
```

If:

```text
root
```

the potential impact can be greater.

If:

```text
non-root
```

the attacker starts with fewer privileges.

This does not eliminate container escape risk, but it reduces the impact of many application-level compromises.

---

# 🚨 7. The `--privileged` Flag

Dangerous example:

```bash
docker run --privileged ...
```

Privileged mode substantially changes the container's access to host resources.

Avoid it unless there is a documented, justified requirement.

Bad:

```text
Need something to work
       ↓
Add --privileged
       ↓
Works
       ↓
Security risk
```

Better:

```text
Identify required capability
       ↓
Grant only required permission
```

---

# 🧩 8. Linux Capabilities

Linux capabilities split powerful root privileges into individual units.

Concept:

```text
Root powers
    │
    ├── NET_ADMIN
    ├── NET_RAW
    ├── SYS_ADMIN
    ├── CHOWN
    ├── DAC_OVERRIDE
    └── ...
```

Docker drops some capabilities by default, but the exact runtime profile should be verified for your Docker/runtime version.

---

# 🔍 9. Inspect Capabilities

Run:

```bash
docker inspect container
```

For deeper Linux-level analysis, inspect the running process/container security configuration using appropriate host tools.

The important principle:

```text
Do not grant capabilities you don't need.
```

---

# ➖ 10. Drop Capabilities

Example:

```bash
docker run --rm \
  --cap-drop=ALL \
  alpine:3.20 \
  sh
```

This creates a highly restricted starting point.

If the application needs a specific capability, add only that one:

```bash
--cap-add=NET_BIND_SERVICE
```

Only add capabilities after understanding why they are required.

---

# ⚠️ 11. `SYS_ADMIN`

One capability deserves special attention:

```text
CAP_SYS_ADMIN
```

It is broad and powerful.

Avoid granting it casually.

If an application asks for:

```bash
--cap-add=SYS_ADMIN
```

stop and investigate whether there is a narrower alternative.

---

# 🔒 12. Read-Only Root Filesystem

Docker can make the container root filesystem read-only:

```bash
docker run --rm \
  --read-only \
  nginx:stable-alpine
```

This can reduce the ability of an attacker or compromised process to modify the container filesystem.

However, applications may still need writable temporary or data locations.

---

# 📂 13. Read-Only + tmpfs

Example:

```bash
docker run --rm \
  --read-only \
  --tmpfs /tmp \
  nginx:stable-alpine
```

Concept:

```text
Root filesystem
      ↓
READ ONLY

/tmp
      ↓
Temporary writable memory-backed area
```

This is a useful hardening pattern when the application supports it.

---

# 🧱 14. Filesystem Hardening

Security options:

```text
Read-only root
Minimal writable paths
Non-root user
Correct ownership
No unnecessary host mounts
No sensitive host paths
```

Avoid:

```bash
-v /:/host
```

unless there is an exceptional, explicitly controlled reason.

---

# 🚨 15. Docker Socket Security

A common dangerous pattern:

```bash
-v /var/run/docker.sock:/var/run/docker.sock
```

Why dangerous?

The Docker socket provides powerful control over the Docker daemon.

Concept:

```text
Container
   │
   ▼
Docker Socket
   │
   ▼
Docker Daemon
   │
   ▼
Host
```

Therefore:

> Access to the Docker daemon can effectively provide host-level control depending on daemon configuration and permissions.

Do not mount the Docker socket into containers unless absolutely necessary.

---

# 🔐 16. Docker Daemon Security

The Docker daemon is a highly privileged component.

Protect:

```text
Docker socket
Daemon configuration
Host access
Remote API
TLS configuration
User permissions
```

Never expose a Docker daemon API publicly without a carefully designed secure configuration.

---

# 🌐 17. Docker Network Security

Security model:

```text
Internet
   │
   ▼
Only required public service
   │
   ▼
Private application network
   │
   ▼
Database
```

Avoid:

```text
Internet
 ├── Frontend
 ├── Backend
 └── Database ❌
```

Use:

```text
Network segmentation
Minimal published ports
Private database
Host firewall
```

---

# 🔌 18. Port Exposure

Example:

```yaml
ports:
  - "8080:80"
```

This publishes a host port.

Ask:

```text
Does this service really need to be reachable from outside the host?
```

If not, don't publish it unnecessarily.

Internal services can communicate through private Docker networks.

---

# 🛡️ 19. Bind to Localhost

For development/admin services:

```yaml
ports:
  - "127.0.0.1:8080:8080"
```

This can prevent direct exposure on other host interfaces.

Still consider:

```text
Host firewall
Authentication
Application security
```

---

# 🔑 20. Secrets

Never bake secrets into an image.

Bad:

```dockerfile
ENV DB_PASSWORD=SuperSecret
```

Bad:

```dockerfile
COPY .env /app/.env
```

Bad:

```text
ARG AWS_SECRET_ACCESS_KEY=...
```

Secrets may become exposed through:

```text
Image history
Layers
Logs
Configuration
Source control
CI output
```

---

# 🧠 21. Secret Management Architecture

Better:

```text
Secret Manager
      │
      ▼
CI/CD or Runtime
      │
      ▼
Application
```

Examples of secret-management systems include:

```text
AWS Secrets Manager
Azure Key Vault
Google Secret Manager
HashiCorp Vault
Docker/Compose secret mechanisms
Kubernetes Secrets + external secret systems
```

Choose based on the deployment environment and security requirements.

---

# 🐳 22. BuildKit Secrets

BuildKit provides mechanisms for using secrets during builds without intentionally baking them into final image layers.

Concept:

```text
Build secret
     │
     ▼
Build step
     │
     ▼
Image
     │
     └── Secret should NOT become permanent image content
```

Do not use:

```dockerfile
ARG SECRET=...
```

for sensitive build credentials.

---

# 🔍 23. Image Security

Image security starts with:

```text
Trusted base image
       ↓
Minimal packages
       ↓
Pinned dependencies
       ↓
Non-root
       ↓
Scan
       ↓
SBOM
       ↓
Sign/verify where required
```

---

# 🧱 24. Minimal Base Images

Instead of:

```text
Huge development image
```

prefer an appropriately minimal runtime image.

Examples:

```text
python:3.12-slim
node:22-bookworm-slim
nginx:stable-alpine
distroless-style runtime images
```

Do not choose a minimal image blindly. Ensure it contains what the application actually needs and that your operational tooling remains adequate.

---

# ⚖️ 25. Alpine vs Slim vs Distroless

### Alpine

Advantages:

```text
Small
Fast to pull
Minimal
```

Considerations:

```text
Different libc ecosystem
Package compatibility
Debugging differences
```

### Slim

Advantages:

```text
Reduced size
Often familiar Debian/Ubuntu ecosystem
Good compatibility
```

### Distroless

Advantages:

```text
Very small runtime surface
Fewer unnecessary tools
```

Considerations:

```text
Debugging can be harder
Shell may not exist
Operational tooling must be planned
```

Choose based on security, compatibility, supportability, and team expertise.

---

# 🧹 26. Remove Unnecessary Packages

Bad:

```dockerfile
RUN apt-get update
RUN apt-get install -y curl vim git gcc ...
```

when the runtime doesn't need them.

Better:

```text
Build stage
   ↓
Compilers/tools

Runtime stage
   ↓
Only runtime dependencies
```

This is where multi-stage builds improve security as well as image size.

---

# 🔒 27. Pin Base Images

Instead of relying only on:

```dockerfile
FROM python:3.12
```

consider controlled release references and, for high-assurance builds, digest pinning:

```dockerfile
FROM python:3.12-slim@sha256:<verified-digest>
```

The digest must come from a trusted registry/source and be verified before use.

---

# 🧬 28. Supply Chain Security

Your application depends on:

```text
Base image
   ↓
OS packages
   ↓
Language runtime
   ↓
Libraries
   ↓
Your source code
   ↓
Build system
   ↓
Registry
   ↓
Deployment
```

Any compromised component can affect the final artifact.

---

# 📦 29. SBOM

SBOM = Software Bill of Materials.

It describes components contained in software.

Concept:

```text
Container Image
      │
      ▼
     SBOM
      │
      ├── OS packages
      ├── Libraries
      ├── Dependencies
      └── Versions
```

SBOM helps with:

```text
Vulnerability management
Inventory
Incident response
Compliance
Supply-chain visibility
```

---

# 🔍 30. Generate SBOM

Tools can generate SBOMs in formats such as:

```text
CycloneDX
SPDX
```

For example, Trivy can generate SBOM-related output:

```bash
trivy image --format spdx-json -o sbom.json myapp:1.0.0
```

Always verify the command against the installed Trivy version and desired output format.

---

# 🛡️ 31. Image Vulnerability Scanning

Trivy example:

```bash
trivy image myapp:1.0.0
```

Concept:

```text
Image
  ↓
Scanner
  ↓
OS + dependencies
  ↓
CVE findings
```

Official:

[Trivy](https://trivy.dev/)

---

# 🚦 32. Severity

Typical vulnerability severity levels:

```text
UNKNOWN
LOW
MEDIUM
HIGH
CRITICAL
```

Do not automatically assume:

```text
CRITICAL = exploitable in your exact deployment
```

Investigate:

```text
Affected component
Attack vector
Application exposure
Exploit availability
Fix version
Runtime relevance
```

---

# 🧪 33. Scan in CI

Example:

```yaml
- name: Scan image
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: myapp:${{ github.sha }}
    severity: HIGH,CRITICAL
    exit-code: '1'
```

Pin third-party GitHub Actions to a trusted version/commit strategy according to your organization's supply-chain policy rather than blindly following mutable references.

---

# ✍️ 34. Image Signing

Image signing provides a way to establish trust in artifact origin/integrity.

Concept:

```text
Build
  ↓
Image
  ↓
Sign
  ↓
Registry
  ↓
Verify
  ↓
Deploy
```

Tools/ecosystems can include:

```text
Sigstore Cosign
Notation
Registry/cloud-native signing solutions
```

---

# 🔐 35. Cosign Concept

A common supply-chain pattern:

```text
CI
 ↓
Build
 ↓
Sign artifact
 ↓
Push
 ↓
Deploy system
 ↓
Verify signature
```

Official:

[Sigstore Cosign](https://docs.sigstore.dev/cosign/)

Do not treat signing alone as proof that the software is safe; it establishes an additional trust signal around artifact identity/origin.

---

# 🧾 36. Provenance

Provenance answers:

```text
Where did this artifact come from?
How was it built?
Which source revision produced it?
Which build system created it?
```

Concept:

```text
Git Commit
    ↓
CI Build
    ↓
Image
    ↓
Provenance
```

Modern build systems can generate attestations/provenance information.

---

# 🏭 37. Secure CI/CD Pipeline

```text
Developer
   ↓
Git
   ↓
Code Review
   ↓
CI
   ├── Unit Tests
   ├── Dependency Scan
   ├── Docker Build
   ├── Image Scan
   ├── SBOM
   ├── Provenance
   └── Sign
        ↓
      Registry
        ↓
   Policy Verification
        ↓
     Deployment
```

---

# 🔐 38. GitHub Actions Security

Protect:

```text
Repository secrets
OIDC permissions
Workflow permissions
Third-party actions
Build credentials
Registry credentials
Deployment credentials
```

Use:

```yaml
permissions:
  contents: read
```

and grant only additional permissions actually required.

For AWS deployments, prefer OIDC/federated authentication where possible.

---

# 🧱 39. Dockerfile Security

Secure Dockerfile principles:

```text
☑ Trusted base
☑ Minimal packages
☑ Multi-stage build
☑ Non-root USER
☑ No hard-coded secrets
☑ Pin/verify dependencies
☑ .dockerignore
☑ Read-only runtime where possible
☑ Healthcheck where appropriate
☑ Minimal final image
```

---

# 📄 40. `.dockerignore`

Example:

```text
.git
.github
.env
.env.*
node_modules
__pycache__
*.log
coverage
tests
README.md
```

Why?

```text
Smaller build context
Less accidental secret exposure
Faster builds
Cleaner images/builds
```

Do not blindly exclude files your build actually needs.

---

# 🚫 41. Dangerous Dockerfile Pattern

Bad:

```dockerfile
COPY . .
```

when the project contains:

```text
.env
private keys
credentials
.git
large artifacts
```

Better:

```text
.dockerignore
+
explicit COPY
+
secret-management strategy
```

---

# 🛡️ 42. Runtime Hardening

A hardened container may use:

```bash
docker run \
  --read-only \
  --cap-drop=ALL \
  --security-opt=no-new-privileges:true \
  --tmpfs /tmp \
  myapp:1.0.0
```

Not every application can run with every restriction.

Treat this as a hardening baseline to test and adapt.

---

# 🚫 43. `no-new-privileges`

Example:

```bash
--security-opt=no-new-privileges:true
```

This helps prevent a process from gaining additional privileges through mechanisms such as setuid/setgid execution.

Use together with:

```text
Non-root
Capability reduction
Read-only filesystem
Seccomp
```

---

# 🧩 44. Seccomp

Seccomp can restrict the Linux system calls available to a container process.

Concept:

```text
Application
    ↓
System calls
    ↓
Seccomp policy
    ↓
Allowed / blocked
    ↓
Kernel
```

Docker provides a default seccomp profile.

Do not disable seccomp casually.

---

# 🚨 45. `seccomp=unconfined`

Avoid:

```bash
--security-opt seccomp=unconfined
```

unless there is a documented reason and risk review.

Disabling protections to make an application work is not a good security strategy.

---

# 🛡️ 46. AppArmor

AppArmor is a Linux security mechanism that can restrict application behavior through profiles.

Concept:

```text
Container process
       ↓
AppArmor profile
       ↓
Allowed operations
```

Docker can integrate with AppArmor on supported Linux systems.

---

# 🛡️ 47. SELinux

SELinux provides mandatory access control on supported Linux systems.

It can control:

```text
Process
Files
Labels
Access rules
```

Docker deployments on SELinux-enabled systems should account for the host's security policy and labeling requirements.

---

# 🧱 48. Resource Limits

Security is also about resource exhaustion.

Potential attack:

```text
Malicious workload
      ↓
CPU exhaustion
Memory exhaustion
PID exhaustion
Disk exhaustion
      ↓
Denial of Service
```

Consider:

```text
CPU limits
Memory limits
PIDs limits
Log limits
Disk quotas/monitoring
```

The exact resource controls depend on Docker/runtime/platform configuration.

---

# 🧠 49. Memory Limits

Example:

```bash
docker run \
  --memory=512m \
  myapp:1.0.0
```

This can help prevent a container from consuming unlimited memory.

Test carefully because an application may fail if its memory requirement exceeds the configured limit.

---

# 🧵 50. PID Limits

Example:

```bash
docker run \
  --pids-limit=200 \
  myapp:1.0.0
```

This can reduce process-exhaustion risk.

---

# 🪵 51. Logging Security

Logs may accidentally contain:

```text
Passwords
Tokens
API keys
Personal data
Session information
```

Implement:

```text
Log filtering
Redaction
Centralized logging
Retention
Access control
```

Never assume logs are safe just because they are not part of the application database.

---

# ☁️ 52. Cloud Container Security

When Docker runs on cloud infrastructure:

```text
Cloud IAM
     ↓
Host
     ↓
Docker Engine
     ↓
Container
     ↓
Application
```

Security must cover:

```text
IAM
Network
Security groups/firewalls
Host OS
Docker
Registry
Secrets
Monitoring
```

---

# 🔥 53. Container Security Monitoring

Monitor:

```text
Image vulnerabilities
Container processes
Network connections
File modifications
Privilege changes
Unexpected binaries
Secrets exposure
Resource anomalies
```

Runtime tools may include:

```text
Falco
Cloud-native runtime security
EDR
SIEM
Audit systems
```

---

# 🧩 54. Docker Security vs Kubernetes Security

Docker:

```text
Image
Container
Docker Engine
Host
Network
```

Kubernetes adds:

```text
Pod Security
RBAC
NetworkPolicy
Admission Control
Secrets
Service Accounts
CNI
Runtime
```

Docker security is foundational knowledge for Kubernetes security.

---

# 🧪 55. Hands-On Labs

## Lab 01 — Identify Root

```bash
docker run --rm alpine:3.20 id
```

---

## Lab 02 — Create Non-Root Image

Build an image that runs as:

```text
appuser
```

Verify UID.

---

## Lab 03 — Privileged Comparison

In a disposable lab, compare normal and privileged containers.

Document the security implications.

---

## Lab 04 — Drop Capabilities

```bash
docker run --rm \
  --cap-drop=ALL \
  alpine:3.20 \
  sh
```

Test what changes.

---

## Lab 05 — Add Specific Capability

Test a legitimate use case for:

```text
NET_BIND_SERVICE
```

Document why it is needed.

---

## Lab 06 — Read-Only Root

```bash
docker run --rm \
  --read-only \
  alpine:3.20 \
  sh
```

Try writing a file.

---

## Lab 07 — Read-Only + tmpfs

```bash
docker run --rm \
  --read-only \
  --tmpfs /tmp \
  alpine:3.20 \
  sh
```

Test writable `/tmp`.

---

## Lab 08 — No New Privileges

Use:

```bash
--security-opt=no-new-privileges:true
```

Document the purpose.

---

## Lab 09 — Docker Socket Risk

Study why:

```text
/var/run/docker.sock
```

is sensitive.

Do not expose it unnecessarily.

---

## Lab 10 — Network Exposure

Identify unnecessary published ports.

Remove them.

---

## Lab 11 — Private Network

Build:

```text
frontend
backend
database
```

Ensure the database is not publicly exposed.

---

## Lab 12 — Dockerfile Secrets

Create a deliberately insecure Dockerfile.

Identify the secret exposure.

Then redesign it.

---

## Lab 13 — `.dockerignore`

Create:

```text
.env
.git
logs
node_modules
```

Verify they are excluded from build context.

---

## Lab 14 — Image Scan

Install/use Trivy.

Scan:

```bash
trivy image myapp:1.0.0
```

Document findings.

---

## Lab 15 — Scan Failure Gate

Create a CI policy that fails on selected severity levels.

---

## Lab 16 — SBOM

Generate:

```text
SPDX
```

or another supported SBOM format.

Inspect the components.

---

## Lab 17 — Multi-Stage Security

Build:

```text
builder image
runtime image
```

Compare packages and attack surface.

---

## Lab 18 — Digest Pinning

Identify the digest of a trusted image and use it in a controlled build.

---

## Lab 19 — Image Signing

Create a test signing workflow using a trusted signing tool.

Verify the signature.

---

## Lab 20 — Provenance

Generate or inspect build provenance/attestation information.

---

## Lab 21 — GitHub Actions Security

Create a workflow with minimal:

```yaml
permissions:
```

---

## Lab 22 — GitHub OIDC

Use GitHub Actions OIDC to access AWS without long-lived AWS keys.

---

## Lab 23 — Runtime Hardening

Combine:

```text
non-root
cap-drop
read-only
no-new-privileges
tmpfs
```

Test the application.

---

## Lab 24 — Resource Limits

Configure:

```text
Memory
CPU
PIDs
```

Test application behavior.

---

## Lab 25 — Runtime Monitoring

Deploy a test workload and monitor:

```text
Processes
Network
Files
Resource usage
```

---

## Lab 26 — Supply Chain Review

Map:

```text
Source
 ↓
Dependencies
 ↓
Dockerfile
 ↓
Base Image
 ↓
Build
 ↓
Registry
 ↓
Deployment
```

Identify threats.

---

## Lab 27 — Secure Registry

Configure:

```text
Private repository
Least privilege
Scanning
Retention
```

---

## Lab 28 — Incident Simulation

Simulate:

```text
Compromised image
```

Investigate:

```text
Image
Container
Network
Logs
Registry
```

---

## Lab 29 — Container Escape Theory

Study:

```text
Kernel
Namespaces
Capabilities
Seccomp
LSM
Privileged mode
```

Understand the defensive controls.

Do not perform unauthorized escape testing against systems you do not own or have permission to assess.

---

## Lab 30 — Production Hardening Challenge

Take a normal Docker application and apply:

```text
Non-root
Minimal image
No secrets in image
Capability reduction
Read-only filesystem
No-new-privileges
Private network
Minimal ports
Healthcheck
Resource limits
Image scanning
SBOM
Signed artifact
```

Document the before/after security posture.

---

# 🚨 56. Troubleshooting Flow

When hardening breaks an application:

```text
Application fails
      │
      ▼
Check logs
      │
      ▼
Check user/permissions
      │
      ▼
Check filesystem write paths
      │
      ▼
Check capabilities
      │
      ▼
Check seccomp/AppArmor/SELinux
      │
      ▼
Check network
      │
      ▼
Check resource limits
      │
      ▼
Adjust only the required control
```

Do not immediately disable every security control.

---

# 🚨 57. Common Security Mistakes

### ❌ Running everything as root

Use:

```text
USER appuser
```

where practical.

### ❌ `--privileged`

Avoid unnecessary privileged mode.

### ❌ Docker socket mounted everywhere

Treat:

```text
docker.sock
```

as highly sensitive.

### ❌ Hard-coded secrets

Use secret-management mechanisms.

### ❌ Public database ports

Keep databases private.

### ❌ Huge base images

Use appropriate minimal runtime images.

### ❌ Ignoring vulnerabilities

Scan and remediate according to risk.

### ❌ Blindly using `latest`

Use controlled release references.

### ❌ Disabling security controls

Do not use:

```text
seccomp=unconfined
```

or similar bypasses just to solve application issues without understanding the risk.

---

# 🏢 58. Enterprise Container Security Architecture

```text
                         SOURCE
                           │
                           ▼
                     Git Repository
                           │
                           ▼
                      CI Pipeline
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
       SAST            Dependency        Secrets
                        Scan             Check
          │                │                │
          └────────────────┼────────────────┘
                           ▼
                      Docker Build
                           │
                 ┌─────────┼─────────┐
                 ▼         ▼         ▼
               Scan      SBOM    Provenance
                 │         │         │
                 └─────────┼─────────┘
                           ▼
                       Sign Image
                           │
                           ▼
                      Private Registry
                           │
                           ▼
                    Policy Verification
                           │
                           ▼
                       Deployment
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
          Runtime       Network       Logging
          Security      Security      / SIEM
```

---

# 🏆 59. Production Docker Security Checklist

```text
IMAGE
☑ Trusted base image
☑ Minimal packages
☑ Dependency versions controlled
☑ Image scanning
☑ SBOM
☑ Provenance
☑ Signing/verification strategy
☑ No embedded secrets

CONTAINER
☑ Non-root
☑ Drop unnecessary capabilities
☑ No privileged mode unless justified
☑ no-new-privileges
☑ Read-only filesystem where possible
☑ Minimal writable paths
☑ Resource limits

NETWORK
☑ Minimal exposed ports
☑ Private internal networks
☑ Database not public
☑ Host firewall
☑ Egress reviewed

HOST
☑ Docker Engine patched
☑ Host OS patched
☑ Docker socket protected
☑ Least privilege access
☑ Monitoring

CI/CD
☑ Protected branches
☑ Minimal workflow permissions
☑ Secure secrets
☑ OIDC/federated credentials where possible
☑ Third-party actions controlled
☑ Security gates

OPERATIONS
☑ Logs monitored
☑ Runtime monitoring
☑ Vulnerability remediation
☑ Incident response
☑ Backup/recovery
☑ Regular access reviews
```

---

# 🎓 60. Interview Questions

## Beginner

1. Why is Docker security important?
2. What is container isolation?
3. Why should containers run as non-root?
4. What is `--privileged`?
5. What are Linux capabilities?
6. What is a Docker image vulnerability?
7. What is `.dockerignore`?
8. Why should secrets not be baked into images?
9. What is image scanning?
10. What is network isolation?

## Intermediate

11. What is `CAP_SYS_ADMIN`?
12. What does `--cap-drop=ALL` do?
13. What is a read-only root filesystem?
14. What is `no-new-privileges`?
15. What is seccomp?
16. What is AppArmor?
17. What is SELinux?
18. Why is Docker socket access dangerous?
19. How do you secure Docker networks?
20. How do you scan a Docker image?

## Advanced

21. Explain container escape risk.
22. How do Linux namespaces help isolation?
23. How do capabilities reduce privilege?
24. How does seccomp protect containers?
25. Design a hardened production container.
26. How would you implement SBOM in CI/CD?
27. How would you sign and verify container images?
28. How would you secure GitHub Actions?
29. How would you design a container supply-chain security pipeline?
30. Explain defense in depth for Docker security.

---

# ⚡ 61. Docker Security Cheat Sheet

```bash
# Run as current/non-root user where supported
docker run --user 1000:1000 IMAGE

# Drop capabilities
docker run \
  --cap-drop=ALL \
  IMAGE

# Add only required capability
docker run \
  --cap-drop=ALL \
  --cap-add=NET_BIND_SERVICE \
  IMAGE

# Read-only root filesystem
docker run \
  --read-only \
  IMAGE

# Read-only + tmpfs
docker run \
  --read-only \
  --tmpfs /tmp \
  IMAGE

# No new privileges
docker run \
  --security-opt=no-new-privileges:true \
  IMAGE

# Memory limit
docker run \
  --memory=512m \
  IMAGE

# PID limit
docker run \
  --pids-limit=200 \
  IMAGE

# Inspect container
docker inspect CONTAINER

# Scan image with Trivy
trivy image IMAGE:TAG

# Generate SBOM
trivy image \
  --format spdx-json \
  -o sbom.json \
  IMAGE:TAG
```

---

# 🗺️ 62. What's Next?

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
13 Docker Registry
       ↓
14 Docker Security         ← 🟢 YOU ARE HERE
       ↓
15 Docker + GitHub Actions
```

## 👉 [15 — Docker + GitHub Actions](../15-Docker-GitHub-Actions/)

Now we connect Docker with your **CI/CD journey**:

```text
Developer
   ↓
Git Push
   ↓
GitHub
   ↓
GitHub Actions
   ├── Test
   ├── Build
   ├── Scan
   ├── SBOM
   ├── Sign
   └── Push
          ↓
      Registry
          ↓
   Docker / Kubernetes / ECS
```

This will become the bridge from your **Docker Zero-to-Hero** course into your **GitHub Actions + Terraform + Kubernetes + Ansible 45-day DevOps program.**

---

<div align="center">

# 🔐 Secure Every Layer. Trust Every Artifact.

### VishwaTech Labs

**Docker • GitHub Actions • Terraform • Ansible • Kubernetes • DevSecOps • Cloud Security**

### By Vishwanath Gowda H

⭐ Harden → Scan → Verify → Monitor → Defend

</div>
