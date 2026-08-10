<div align="center">

# 🌐 Docker Networking — Complete Zero-to-Hero Masterclass

### 🚀 Zero to Hero | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Networking-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/engine/network/)
[![Bridge](https://img.shields.io/badge/Network-Bridge-orange)](#-bridge-network)
[![DNS](https://img.shields.io/badge/DNS-Container%20Discovery-blue)](#-container-dns-and-service-discovery)
[![Security](https://img.shields.io/badge/Network-Security-success)](#-docker-network-security)
[![Labs](https://img.shields.io/badge/Labs-25+-purple)](#-hands-on-labs)

**Understand how containers communicate with each other, with the host, and with external systems — from your first bridge network to production three-tier architectures.**

[📘 Docker Networking](https://docs.docker.com/engine/network/) •
[🌐 Bridge Networks](https://docs.docker.com/engine/network/drivers/bridge/) •
[🔌 Port Publishing](https://docs.docker.com/engine/network/port-publishing/) •
[🔍 Network Troubleshooting](https://docs.docker.com/engine/network/)

</div>

---

# 🎯 What You Will Learn

Docker networking answers a fundamental question:

> **How does a container communicate with another container, the Docker host, the Internet, or an external database?**

Core model:

```text
                    🌐 Internet
                         │
                         ▼
                  Host / Firewall
                         │
                    Published Port
                         │
                         ▼
                 ┌───────────────┐
                 │   Container   │
                 └───────────────┘
                         │
                  Docker Network
                         │
             ┌───────────┴───────────┐
             ▼                       ▼
        Backend API              Database
```

You will learn:

- Docker networking fundamentals
- Network namespaces
- Bridge networking
- User-defined bridge networks
- Host networking
- None networking
- Container DNS
- Service discovery
- Port publishing
- Container-to-container communication
- Host-to-container communication
- Internet access
- Network inspection
- Network isolation
- Frontend/backend/database architecture
- Troubleshooting
- Security
- Production design

---

# 🧠 1. What Is Docker Networking?

Docker networking provides connectivity between containers, the host, and external networks.

A container can have:

```text
Network interface
IP address
Routes
DNS configuration
Network namespace
```

Concept:

```text
Container
   │
   ▼
Network Namespace
   │
   ▼
Docker Network
   │
   ├── Other Containers
   ├── Host
   └── External Network
```

---

# 🏗️ 2. The Most Important Mental Model

There are two separate concepts beginners often confuse:

```text
CONTAINER PORT
       vs
HOST PUBLISHED PORT
```

Example:

```bash
docker run -d -p 8080:80 nginx
```

Means:

```text
Host Port       Container Port
    8080   ────────►   80
```

The application listens on port `80` inside the container.

The Docker host exposes port `8080`.

---

# 🔌 3. `-p` Port Publishing

Syntax:

```bash
-p HOST_PORT:CONTAINER_PORT
```

Example:

```bash
docker run -d \
  --name web \
  -p 8080:80 \
  nginx
```

Access:

```text
http://localhost:8080
```

Flow:

```text
Browser
   │
   ▼
Host:8080
   │
   ▼
Container:80
   │
   ▼
Nginx
```

---

# 🚨 4. `EXPOSE` vs `-p`

Dockerfile:

```dockerfile
EXPOSE 80
```

does not publish the port.

Runtime:

```bash
docker run -p 8080:80 nginx
```

publishes the port.

Remember:

```text
EXPOSE
   ↓
Documentation / metadata

-p
   ↓
Runtime port publishing
```

---

# 🧱 5. Docker Network Drivers

Common Docker network modes/drivers include:

```text
bridge
host
none
overlay
macvlan
ipvlan
```

The exact availability and behavior depends on Docker environment and deployment mode.

For learning Docker on a single host, focus first on:

```text
bridge
host
none
```

Then learn overlay networking for multi-host/container orchestration scenarios.

---

# 🌉 6. Bridge Network

The bridge driver is commonly used for container networking on a single Docker host.

List:

```bash
docker network ls
```

You may see:

```text
bridge
host
none
```

Run:

```bash
docker run -d --name web nginx
```

By default, containers can be attached to a bridge network.

---

# 🏗️ 7. Default Bridge vs User-Defined Bridge

This distinction is very important.

### Default bridge

Docker creates a default bridge network named:

```text
bridge
```

### User-defined bridge

Create:

```bash
docker network create app-network
```

Then:

```bash
docker run -d \
  --name backend \
  --network app-network \
  nginx
```

User-defined networks provide better isolation and built-in DNS-based container name resolution compared with relying on the legacy default bridge behavior.

---

# 🌐 8. User-Defined Bridge Network

Create:

```bash
docker network create app-network
```

Run backend:

```bash
docker run -d \
  --name backend \
  --network app-network \
  nginx
```

Run another container:

```bash
docker run -d \
  --name client \
  --network app-network \
  alpine:3.20 \
  sleep infinity
```

Now the containers share:

```text
app-network
```

---

# 🔍 9. `docker network ls`

List:

```bash
docker network ls
```

Typical output concept:

```text
NETWORK ID     NAME        DRIVER
xxxx           bridge      bridge
xxxx           host        host
xxxx           none        null
xxxx           app-network bridge
```

Use this command whenever you are unsure which networks exist.

---

# 🔎 10. `docker network inspect`

Inspect:

```bash
docker network inspect app-network
```

Useful information can include:

```text
Network ID
Driver
Subnet
Gateway
Containers
IP addresses
Options
```

This is one of your most important networking troubleshooting commands.

---

# 🏷️ 11. Container DNS

On a user-defined Docker network, containers can generally reach one another using container names.

Example:

```bash
docker network create app-network
```

Backend:

```bash
docker run -d \
  --name backend \
  --network app-network \
  nginx
```

Client:

```bash
docker run --rm \
  --network app-network \
  alpine:3.20 \
  wget -qO- http://backend
```

Concept:

```text
client
  │
  │ backend
  ▼
Docker DNS
  │
  ▼
backend container
```

This is much better than hard-coding container IP addresses.

---

# 🚫 12. Don't Hard-Code Container IPs

Bad:

```text
backend = 172.18.0.5
```

Why?

Containers can be recreated.

Then:

```text
Old IP
  ↓
Container removed
  ↓
New container
  ↓
Different IP
```

Better:

```text
http://backend:8080
```

Use stable service/container names through Docker's network DNS.

---

# 🧭 13. Service Discovery

Imagine:

```text
frontend
backend
database
```

Create:

```bash
docker network create app-network
```

Attach all:

```text
frontend ─┐
backend  ─┼── app-network
database ─┘
```

Then:

```text
frontend → backend
backend  → database
```

using names rather than manually managed container IPs.

---

# 🧪 14. Test Container DNS

Run:

```bash
docker run --rm \
  --network app-network \
  alpine:3.20 \
  getent hosts backend
```

If the image does not contain the required utility, install/use an appropriate diagnostic image or tool.

You can also test HTTP:

```bash
docker run --rm \
  --network app-network \
  curlimages/curl:latest \
  http://backend
```

Use a controlled tag/reference in production examples rather than relying blindly on mutable tags.

---

# 🖥️ 15. Host Networking

Host mode:

```bash
docker run --network host nginx
```

The container uses the host's network namespace rather than a separate typical container network namespace.

Concept:

```text
Host Network
     │
     └── Container process
```

This changes the networking model significantly.

Port publishing with:

```bash
-p
```

is not used in the same way with host networking.

Host networking has platform-specific behavior, so verify support and semantics on your operating system.

---

# 🚫 16. None Network

Run:

```bash
docker run --network none alpine:3.20
```

The container receives no normal external network connectivity through Docker.

Concept:

```text
Container
   │
   ▼
No normal network connectivity
```

Useful for:

```text
Isolation
Security-sensitive workloads
Offline processing
Testing
```

---

# 🔗 17. Connect Existing Container to Network

Create:

```bash
docker network create app-network
```

Connect:

```bash
docker network connect app-network web
```

Inspect:

```bash
docker network inspect app-network
```

Disconnect:

```bash
docker network disconnect app-network web
```

A container can be connected to multiple networks.

---

# 🕸️ 18. Multiple Networks

Example architecture:

```text
                  Internet
                     │
                     ▼
                 frontend
                     │
              frontend-net
                     │
                     ▼
                  backend
                     │
              backend-net
                     │
                     ▼
                 database
```

This allows you to control which containers share a network.

For example:

```text
frontend ↔ backend
backend  ↔ database
frontend ✕ database
```

This is a powerful isolation pattern.

---

# 🔐 19. Network Isolation

Suppose:

```text
frontend
backend
database
```

Instead of putting everything on one network:

```text
                    ┌──────────────┐
frontend ───────────│ frontend-net │
                    └──────┬───────┘
                           │
                         backend
                           │
                    ┌──────┴───────┐
                    │ backend-net  │
                    └──────┬───────┘
                           │
                        database
```

Now:

```text
Frontend → Backend
Backend → Database
Frontend → Database ❌
```

This supports least-privilege network design.

---

# 🏢 20. Real-World Three-Tier Architecture

```text
                    🌐 Internet
                         │
                         ▼
                 Reverse Proxy /
                    Load Balancer
                         │
                         ▼
                 ┌──────────────┐
                 │   Frontend   │
                 └──────┬───────┘
                        │
                        ▼
                 ┌──────────────┐
                 │   Backend    │
                 └──────┬───────┘
                        │
                        ▼
                 ┌──────────────┐
                 │   Database   │
                 └──────────────┘
```

Recommended concept:

```text
Public-facing layer
       ↓
Application layer
       ↓
Data layer
```

Do not expose the database directly to the Internet.

---

# 🔥 21. Example Three-Tier Docker Setup

Create networks:

```bash
docker network create frontend-net
docker network create backend-net
```

Database:

```bash
docker run -d \
  --name database \
  --network backend-net \
  postgres:17
```

Backend:

```bash
docker run -d \
  --name backend \
  --network backend-net \
  my-backend:1.0
```

Connect backend to frontend network:

```bash
docker network connect frontend-net backend
```

Frontend:

```bash
docker run -d \
  --name frontend \
  --network frontend-net \
  -p 8080:80 \
  my-frontend:1.0
```

Concept:

```text
frontend-net
     │
frontend ─── backend
               │
backend-net     │
     │          │
     └── database
```

---

# 🧩 22. Container-to-Container Communication

Suppose backend listens on:

```text
8080
```

Frontend should connect to:

```text
http://backend:8080
```

Not:

```text
http://localhost:8080
```

Why?

Inside the frontend container:

```text
localhost
   ↓
frontend container itself
```

It does not automatically mean:

```text
backend container
```

This is one of the most common Docker networking mistakes.

---

# 🚨 23. `localhost` Inside a Container

This:

```bash
curl http://localhost:8080
```

means:

```text
THIS container
```

not:

```text
Host
```

and not:

```text
Another container
```

Mental model:

```text
Container A
localhost
   ↓
Container A

Container B
localhost
   ↓
Container B
```

For another container:

```text
http://container-name:port
```

---

# 🖥️ 24. Container Accessing Host

This is platform-dependent.

On Docker Desktop, a commonly available hostname is:

```text
host.docker.internal
```

Example:

```text
http://host.docker.internal:8080
```

On Linux Docker Engine, availability/configuration can differ. Do not assume this hostname exists everywhere.

For production architectures, prefer explicit service/network design rather than relying on host access.

---

# 🌍 25. Container Internet Access

A typical container attached to a normal Docker network can access external networks when the host/network configuration allows it.

Example:

```bash
docker run --rm \
  alpine:3.20 \
  wget -qO- https://example.com
```

Concept:

```text
Container
   ↓
Docker Network
   ↓
Host networking
   ↓
Internet
```

External access depends on host firewall, routing, DNS, proxy, and network policies.

---

# 🧭 26. Container Routes

Inside a container:

```bash
ip route
```

may show routes such as:

```text
default via ...
```

Minimal images may not include `ip`.

Use a diagnostic image when necessary.

Networking troubleshooting often requires checking:

```text
IP
Route
DNS
Port
Application
Firewall
```

---

# 🔍 27. Useful Network Commands

List:

```bash
docker network ls
```

Inspect:

```bash
docker network inspect app-network
```

Create:

```bash
docker network create app-network
```

Connect:

```bash
docker network connect app-network container
```

Disconnect:

```bash
docker network disconnect app-network container
```

Remove:

```bash
docker network rm app-network
```

Prune unused:

```bash
docker network prune
```

---

# 📡 28. Network Subnets

Create a custom network:

```bash
docker network create \
  --driver bridge \
  --subnet 172.20.0.0/16 \
  app-network
```

Inspect:

```bash
docker network inspect app-network
```

You may see:

```text
Subnet:
172.20.0.0/16

Gateway:
172.20.0.1
```

Avoid overlapping this subnet with networks your host, VPN, office, or cloud environment already uses.

---

# 🚦 29. Gateway

Concept:

```text
Container
   │
   ▼
Docker Network Gateway
   │
   ▼
Host / External Network
```

For a custom network:

```text
Subnet:
172.20.0.0/16

Gateway:
172.20.0.1
```

The exact gateway is assigned/configured by Docker and should be verified with:

```bash
docker network inspect app-network
```

---

# 🧮 30. Custom IPAM

Docker allows network address management configuration.

Example:

```bash
docker network create \
  --driver bridge \
  --subnet 172.21.0.0/16 \
  --gateway 172.21.0.1 \
  app-network
```

You can also configure IP ranges and allocation behavior.

Use custom addressing only when there is a real requirement.

---

# ⚠️ 31. Avoid Network Overlap

Bad:

```text
Office Network:
10.0.0.0/16

Docker Network:
10.0.0.0/16
```

This can cause routing ambiguity.

Better:

```text
Office:
10.0.0.0/16

Docker:
172.20.0.0/16
```

Always consider:

```text
Home LAN
Office LAN
VPN
Cloud VPC
Docker networks
Kubernetes networks
```

---

# 🛡️ 32. Docker Network Security

Security principles:

```text
Least exposure
      ↓
Minimal published ports
      ↓
Private application networks
      ↓
Database not public
      ↓
Network segmentation
      ↓
Controlled egress
      ↓
Host firewall
      ↓
Monitoring
```

Example:

```text
Internet
   │
   ▼
Reverse Proxy
   │
   ▼
Backend
   │
   ▼
Database
```

Not:

```text
Internet
  ├── Frontend
  ├── Backend
  └── Database ❌
```

---

# 🔐 33. Published Ports Are Exposure

This:

```bash
-p 0.0.0.0:5432:5432
```

can expose PostgreSQL on host interfaces depending on host/firewall configuration.

If database access should be local-only:

```bash
-p 127.0.0.1:5432:5432
```

Or better, if only containers need access, don't publish the database port to the host at all.

Use:

```text
Backend → database:5432
```

on a private Docker network.

---

# 🧱 34. Private Backend Pattern

Instead of:

```bash
docker run -p 8080:8080 backend
docker run -p 5432:5432 database
```

consider:

```text
Backend
   │
   │ private Docker network
   ▼
Database
```

Only expose the public application entry point.

---

# 🌐 35. Reverse Proxy Pattern

```text
Internet
   │
   ▼
Nginx / Reverse Proxy
   │
   ├── frontend
   │
   └── backend
          │
          ▼
       database
```

Public:

```text
80 / 443
```

Private:

```text
backend
database
```

This is common in production architectures.

---

# 🧪 36. Hands-On Labs

## Lab 01 — List Networks

```bash
docker network ls
```

Identify:

```text
bridge
host
none
```

---

## Lab 02 — Inspect Default Bridge

```bash
docker network inspect bridge
```

Document:

```text
Subnet
Gateway
Containers
```

---

## Lab 03 — Create User-Defined Network

```bash
docker network create training-net
```

Inspect:

```bash
docker network inspect training-net
```

---

## Lab 04 — Container Communication

Create:

```bash
docker network create app-net
```

Run:

```bash
docker run -d --name web --network app-net nginx
```

Test from another container.

---

## Lab 05 — DNS

Test:

```bash
getent hosts web
```

from a suitable diagnostic container.

---

## Lab 06 — Name vs IP

Find the container IP.

Then compare:

```text
IP-based connection
vs
Name-based connection
```

Explain why name-based communication is preferred.

---

## Lab 07 — Port Publishing

```bash
docker run -d \
  --name web \
  --network app-net \
  -p 8080:80 \
  nginx
```

Access:

```text
http://localhost:8080
```

---

## Lab 08 — No Port Publishing

Run:

```bash
docker run -d \
  --name private-web \
  --network app-net \
  nginx
```

Explain:

```text
Container-to-container access
vs
Host/browser access
```

---

## Lab 09 — Multiple Networks

Create:

```bash
docker network create frontend-net
docker network create backend-net
```

Attach backend to both.

---

## Lab 10 — Three-Tier Architecture

Build:

```text
Frontend
Backend
Database
```

Use:

```text
frontend-net
backend-net
```

Verify:

```text
Frontend → Backend
Backend → Database
Frontend ✕ Database
```

---

## Lab 11 — Custom Subnet

```bash
docker network create \
  --subnet 172.20.0.0/16 \
  training-net
```

Inspect it.

---

## Lab 12 — Custom Gateway

```bash
docker network create \
  --subnet 172.21.0.0/16 \
  --gateway 172.21.0.1 \
  training-net
```

Verify.

---

## Lab 13 — Static IP

On a custom network:

```bash
docker run -d \
  --name web \
  --network training-net \
  --ip 172.21.0.10 \
  nginx
```

Discuss why stable DNS/service names are usually preferable to depending on static container IPs.

---

## Lab 14 — Host Network

Test:

```bash
docker run --network host nginx
```

Compare networking behavior with bridge mode.

---

## Lab 15 — None Network

```bash
docker run --rm \
  --network none \
  alpine:3.20 \
  ip addr
```

Explain the result.

---

## Lab 16 — Network Connect

Create a running container.

Connect it:

```bash
docker network connect training-net container
```

Verify with:

```bash
docker inspect container
```

---

## Lab 17 — Network Disconnect

```bash
docker network disconnect training-net container
```

Verify connectivity changes.

---

## Lab 18 — Port Conflict

Run two containers trying to publish:

```text
8080
```

Diagnose and fix.

---

## Lab 19 — DNS Troubleshooting

Break the network relationship.

Then troubleshoot:

```text
Network
DNS
Name
Port
Application
```

---

## Lab 20 — Database Isolation

Run PostgreSQL without publishing its port.

Allow only backend access through a private network.

---

## Lab 21 — Reverse Proxy

Create:

```text
Nginx
Backend
Database
```

Publish only Nginx.

---

## Lab 22 — Network Overlap

Create a custom network with an intentional overlapping subnet.

Document why overlapping routes are problematic.

---

## Lab 23 — Network Cleanup

List:

```bash
docker network ls
```

Remove unused networks:

```bash
docker network prune
```

---

## Lab 24 — Network Security Review

Review a Docker deployment:

```text
☑ Minimal published ports
☑ Database private
☑ Network segmentation
☑ No unnecessary host mode
☑ Controlled egress
☑ No hard-coded container IPs
```

---

## Lab 25 — Production Network Challenge

Design:

```text
Internet
   ↓
Reverse Proxy
   ↓
Frontend
   ↓
Backend
   ↓
Database
```

Implement appropriate Docker networks and explain every connection.

---

# 🚨 37. Troubleshooting Flow

When container A cannot reach container B:

```text
START
  │
  ▼
Are both containers running?
  │
  ▼
Are they on the same network?
  │
  ▼
Does DNS resolve the name?
  │
  ▼
Is the application listening?
  │
  ▼
Is the port correct?
  │
  ▼
Is the application bound to the correct interface?
  │
  ▼
Are firewall/network rules blocking traffic?
  │
  ▼
SUCCESS
```

Commands:

```bash
docker ps
docker network ls
docker network inspect app-net
docker inspect container
docker logs container
docker exec -it container sh
```

---

# 🚨 38. Common Problem — "Connection Refused"

Possible causes:

```text
Application not running
Wrong port
Application listening on wrong interface
Container not on network
Service not ready
Firewall
```

Do not immediately blame Docker networking.

Test the application itself.

---

# 🚨 39. Common Problem — "Could Not Resolve Host"

Check:

```text
Container network
Container name
DNS
Network attachment
```

Example:

```bash
docker network inspect app-net
```

Confirm both containers appear.

---

# 🚨 40. Common Problem — "localhost Doesn't Work"

If backend is in another container:

Bad:

```text
http://localhost:8080
```

Better:

```text
http://backend:8080
```

because:

```text
localhost = current container
```

---

# 🚨 41. Common Problem — Port Published but App Still Fails

Example:

```bash
docker run -p 8080:8080 app
```

But application listens only on:

```text
127.0.0.1:8080
```

inside the container.

A server intended for container access usually needs to listen on an appropriate interface such as:

```text
0.0.0.0
```

The exact configuration depends on the application framework.

---

# 🏢 42. Production Network Architecture

```text
                     INTERNET
                         │
                      HTTPS
                         │
                         ▼
                ┌────────────────┐
                │ Reverse Proxy  │
                └───────┬────────┘
                        │
                public/app network
                        │
                ┌───────▼────────┐
                │    Backend     │
                └───────┬────────┘
                        │
                   private network
                        │
                ┌───────▼────────┐
                │    Database    │
                └────────────────┘
```

Security goals:

```text
Database not public
Backend not unnecessarily public
Only required ports published
Network segmentation
Strong application authentication
Host/network firewall controls
```

---

# 🧠 43. Docker Networking vs Kubernetes Networking

Docker:

```text
Container
    ↓
Docker Network
    ↓
Container DNS
```

Kubernetes:

```text
Pod
    ↓
Cluster Network
    ↓
Service
    ↓
DNS
```

Kubernetes adds:

```text
Services
Ingress/Gateway
NetworkPolicy
CNI
Cluster networking
Pod-to-pod networking
```

Docker networking is an important foundation for understanding container networking before Kubernetes.

---

# 🎓 44. Interview Questions

## Beginner

1. What is Docker networking?
2. What is a bridge network?
3. What is the default bridge?
4. What is a user-defined bridge?
5. What does `-p` do?
6. What does `EXPOSE` do?
7. What is container IP?
8. What is a network gateway?
9. What is `docker network ls`?
10. What is `docker network inspect`?

## Intermediate

11. Default bridge vs user-defined bridge?
12. How do containers communicate?
13. How does Docker DNS work?
14. Why shouldn't you hard-code container IPs?
15. What is host networking?
16. What is none networking?
17. How do you connect an existing container to a network?
18. Can one container join multiple networks?
19. How do you isolate frontend and database?
20. Why should databases usually not have public ports?

## Advanced

21. Design a three-tier Docker network.
22. Explain Docker container-to-container DNS.
23. How would you troubleshoot connection refused?
24. How would you troubleshoot DNS resolution?
25. Why might a published port still not work?
26. How would you prevent network overlap?
27. Explain Docker network security.
28. When would host networking be appropriate?
29. Explain Docker networking concepts that help when learning Kubernetes.
30. Design a production Docker network for frontend, API, database, and reverse proxy.

---

# 🏆 45. Production Checklist

```text
☑ User-defined networks
☑ Meaningful network names
☑ DNS/service names
☑ No hard-coded container IPs
☑ Minimal published ports
☑ Database private
☑ Network segmentation
☑ Appropriate host firewall
☑ No unnecessary host networking
☑ No unnecessary public exposure
☑ Custom subnet only when required
☑ Avoid subnet overlap
☑ Network troubleshooting documented
☑ Logs/monitoring available
☑ Production architecture reviewed
```

---

# ⚡ 46. Networking Cheat Sheet

```bash
# List
docker network ls

# Inspect
docker network inspect NETWORK

# Create
docker network create NETWORK

# Create custom bridge
docker network create \
  --driver bridge \
  NETWORK

# Custom subnet
docker network create \
  --subnet 172.20.0.0/16 \
  NETWORK

# Run on network
docker run -d \
  --network NETWORK \
  --name APP \
  IMAGE

# Connect
docker network connect NETWORK CONTAINER

# Disconnect
docker network disconnect NETWORK CONTAINER

# Remove
docker network rm NETWORK

# Cleanup
docker network prune

# Publish port
docker run -p 8080:80 IMAGE

# Local-only publication
docker run -p 127.0.0.1:8080:80 IMAGE
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
10 Docker Networking       ← 🟢 YOU ARE HERE
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

## 👉 [11 — Docker Volumes](../11-Docker-Volumes/)

Next we move into **persistent data**:

```text
Container
    │
    ├── Writable Layer
    │
    ├── Named Volumes
    ├── Bind Mounts
    ├── tmpfs
    │
    ▼
Persistent Storage
```

We will build real database persistence labs, backup/restore scenarios, permissions examples, volume troubleshooting, and production storage designs.

---

<div align="center">

# 🌐 Connect Everything. Expose Only What You Need.

### VishwaTech Labs

**Docker • GitHub Actions • Terraform • Ansible • Kubernetes • DevOps • Cloud Security**

### By Vishwanath Gowda H

⭐ Network → Isolate → Secure → Troubleshoot → Scale

</div>
