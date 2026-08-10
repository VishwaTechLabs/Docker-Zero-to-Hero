<div align="center">

# 💾 Docker Volumes — Complete Persistent Storage Masterclass

### 🚀 Zero to Hero | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Volumes-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/engine/storage/volumes/)
[![Storage](https://img.shields.io/badge/Storage-Persistence-orange)](#-storage-fundamentals)
[![Databases](https://img.shields.io/badge/Databases-Hands--On-success)](#-database-persistence-lab)
[![Backup](https://img.shields.io/badge/Backup-Restore-blueviolet)](#-backup-and-restore)
[![Labs](https://img.shields.io/badge/Labs-25+-purple)](#-hands-on-labs)

**Understand how Docker stores data, why containers are ephemeral, and how to design persistent storage for databases and production workloads.**

[📘 Docker Storage](https://docs.docker.com/engine/storage/) •
[💾 Volumes](https://docs.docker.com/engine/storage/volumes/) •
[📂 Bind Mounts](https://docs.docker.com/engine/storage/bind-mounts/) •
[🧠 Storage Drivers](https://docs.docker.com/engine/storage/drivers/)

</div>

---

# 🎯 What You Will Learn

Containers are designed to be replaceable.

But applications often need data to survive:

```text
Container deleted
       ↓
Application recreated
       ↓
Data should still exist
```

This module teaches:

- Container writable layer
- Named volumes
- Anonymous volumes
- Bind mounts
- tmpfs mounts
- Volume lifecycle
- Volume inspection
- Database persistence
- Permissions
- Backup and restore
- Volume migration concepts
- Storage drivers
- Security
- Production storage design
- Docker Compose storage
- Troubleshooting

---

# 🧠 1. Why Persistent Storage Is Needed

A container has a writable layer.

Conceptually:

```text
Container
│
├── Read-only image layers
│
└── Writable container layer
```

If the container is removed:

```text
Container
   ↓
Removed
   ↓
Writable layer disappears
```

Therefore:

```text
Important data
      ↓
Externalized storage
      ↓
Volume / bind mount / other storage
```

---

# 💥 2. The Classic Database Problem

Run PostgreSQL:

```bash
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=example \
  postgres:17
```

Data is stored inside the container's writable storage unless you configure persistent storage.

Now:

```bash
docker rm -f postgres
```

Create a new container.

The old container's writable data is not automatically restored.

This is why databases need deliberate persistence.

---

# 💾 3. Docker Storage Options

The three common mount types are:

```text
Volume
Bind mount
tmpfs
```

Concept:

```text
                Docker Storage
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       Volume     Bind Mount   tmpfs
          │          │          │
       Docker      Host path   Memory
       managed
```

---

# 🟦 4. Named Volumes

Create:

```bash
docker volume create app-data
```

List:

```bash
docker volume ls
```

Inspect:

```bash
docker volume inspect app-data
```

Use:

```bash
docker run -d \
  --name app \
  -v app-data:/data \
  alpine:3.20 \
  sh -c "while true; do sleep 3600; done"
```

Concept:

```text
Docker-managed volume
        │
        ▼
      /data
        │
        ▼
   Container
```

---

# ⭐ 5. Why Named Volumes?

Named volumes are useful when:

```text
Docker should manage storage
Data should survive container replacement
Application needs persistent data
You don't want to expose arbitrary host paths
```

Typical examples:

```text
PostgreSQL
MySQL
Redis
Application uploads
Shared application data
```

---

# 🔍 6. Volume Commands

Create:

```bash
docker volume create mydata
```

List:

```bash
docker volume ls
```

Inspect:

```bash
docker volume inspect mydata
```

Remove:

```bash
docker volume rm mydata
```

Remove unused volumes:

```bash
docker volume prune
```

Be careful:

> `volume prune` can delete unused volumes and therefore potentially destroy data you intended to keep.

---

# 📦 7. Named Volume Example

```bash
docker volume create training-data
```

Run:

```bash
docker run -d \
  --name storage-demo \
  -v training-data:/data \
  alpine:3.20 \
  sh -c "echo 'VishwaTech Labs' > /data/message.txt && sleep 3600"
```

Remove container:

```bash
docker rm -f storage-demo
```

Create another:

```bash
docker run --rm \
  -v training-data:/data \
  alpine:3.20 \
  cat /data/message.txt
```

Expected:

```text
VishwaTech Labs
```

The container disappeared.

The volume remained.

---

# 📂 8. Bind Mounts

A bind mount maps a specific host filesystem path into a container.

Example:

```bash
docker run --rm \
  --mount type=bind,src="$PWD",dst=/app \
  alpine:3.20 \
  ls -la /app
```

Concept:

```text
Host directory
      │
      │ bind mount
      ▼
Container directory
```

---

# 🆚 9. Volume vs Bind Mount

| Volume | Bind Mount |
|---|---|
| Docker-managed | Host-managed |
| Docker chooses storage location | You choose exact host path |
| Great for application data | Great for source-code development |
| Easier portability within Docker workflows | Tightly coupled to host path |
| Common for databases | Common for local development |

Rule of thumb:

```text
Application data
    ↓
Volume

Local source-code development
    ↓
Bind mount
```

---

# 🧑‍💻 10. Development Bind Mount

Suppose:

```text
project/
├── app.py
└── requirements.txt
```

Run:

```bash
docker run --rm \
  --mount type=bind,src="$PWD",dst=/app \
  python:3.12-slim \
  python /app/app.py
```

Now changes on the host can be reflected in the mounted directory.

This is useful for development workflows.

---

# 🧠 11. `-v` vs `--mount`

Short syntax:

```bash
docker run -v app-data:/data image
```

More explicit syntax:

```bash
docker run \
  --mount type=volume,src=app-data,dst=/data \
  image
```

For teaching and production scripts, `--mount` can be easier to read because the fields are explicit.

---

# 🧪 12. Read-Only Mount

A mount can be made read-only.

Example:

```bash
docker run --rm \
  --mount type=bind,src="$PWD",dst=/app,readonly \
  alpine:3.20 \
  ls -la /app
```

Concept:

```text
Host
  │
  │ read-only
  ▼
Container
```

Useful when the application only needs to read data.

---

# 🧊 13. tmpfs Mount

A tmpfs mount stores data in memory rather than persistent disk storage.

Example:

```bash
docker run --rm \
  --tmpfs /tmp \
  alpine:3.20
```

Concept:

```text
Container
   │
   ▼
RAM-backed temporary storage
   │
   ▼
Container removed
   │
   ▼
Data gone
```

Useful for temporary data that should not be persisted to disk.

---

# 🆚 14. Volume vs Bind vs tmpfs

| Type | Managed by | Persistence | Typical Use |
|---|---|---|---|
| Volume | Docker | Yes | DB/app data |
| Bind | Host | Yes | Development/config |
| tmpfs | Memory | No | Temporary sensitive/runtime data |

---

# 🏗️ 15. Volume Lifecycle

Important:

```text
Volume
  │
  ├── Created
  │
  ├── Attached to container
  │
  ├── Container removed
  │
  ├── Volume remains
  │
  └── Explicitly removed
```

Unlike a container's writable layer, a named volume is independently managed.

---

# 🗄️ 16. PostgreSQL Persistent Storage

Create:

```bash
docker volume create postgres-data
```

Run:

```bash
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=strong-example-password \
  -v postgres-data:/var/lib/postgresql/data \
  postgres:17
```

Now:

```text
PostgreSQL
     │
     ▼
/var/lib/postgresql/data
     │
     ▼
postgres-data volume
```

For production, use a secret-management approach rather than putting real passwords directly into shell history or source-controlled files.

---

# 🧪 17. PostgreSQL Persistence Test

Connect:

```bash
docker exec -it postgres \
  psql -U postgres
```

Create:

```sql
CREATE DATABASE training;
```

Exit.

Remove:

```bash
docker rm -f postgres
```

Recreate:

```bash
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=strong-example-password \
  -v postgres-data:/var/lib/postgresql/data \
  postgres:17
```

Check:

```bash
docker exec -it postgres \
  psql -U postgres \
  -l
```

The database should still exist if the same compatible data volume is reused.

---

# 🐬 18. MySQL Persistent Storage

Create:

```bash
docker volume create mysql-data
```

Run:

```bash
docker run -d \
  --name mysql \
  -e MYSQL_ROOT_PASSWORD=strong-example-password \
  -v mysql-data:/var/lib/mysql \
  mysql:8.4
```

Architecture:

```text
MySQL
  │
  ▼
/var/lib/mysql
  │
  ▼
mysql-data
```

---

# 🔴 19. Redis Persistence

Redis can use persistent storage depending on its persistence configuration.

Example:

```bash
docker volume create redis-data
```

Run:

```bash
docker run -d \
  --name redis \
  -v redis-data:/data \
  redis:7
```

Important:

> Mounting `/data` alone does not guarantee the exact persistence behavior you want. Configure Redis persistence according to the durability requirement.

---

# 🗂️ 20. Application Uploads

Suppose an application stores uploads:

```text
/app/uploads
```

Use:

```bash
docker volume create uploads
```

Then:

```bash
docker run -d \
  --name web \
  --mount type=volume,src=uploads,dst=/app/uploads \
  myapp:1.0
```

Now application replacement does not automatically delete uploaded data.

---

# 🔐 21. Storage Security

Important principles:

```text
Least privilege
     ↓
Read-only where possible
     ↓
Correct ownership
     ↓
Avoid host-path exposure
     ↓
Protect database volumes
     ↓
Back up important data
     ↓
Encrypt storage where required
```

Do not assume:

```text
Volume = Automatically secure
```

Security depends on:

```text
Host
Docker daemon
Filesystem permissions
Encryption
Backups
Access control
Application
```

---

# 👤 22. Permissions

A common problem:

```text
Container user
       ↓
tries to write
       ↓
Permission denied
```

Why?

The process UID/GID may not have permission to write to the mounted storage.

Debug:

```bash
docker exec -it container id
```

Inspect:

```bash
docker exec -it container ls -ld /data
```

For bind mounts, also inspect the host directory:

```bash
ls -ld ./data
```

---

# 🧑‍🔧 23. Non-Root + Volume

A secure application may run:

```dockerfile
USER appuser
```

But then:

```text
/app/data
```

may not be writable.

Design the image and storage permissions together.

Example concept:

```text
Create user
    ↓
Create directory
    ↓
Set ownership
    ↓
USER appuser
    ↓
Mount storage
```

At runtime, ensure the mounted volume permissions are compatible.

---

# 🧱 24. Bind Mount Permissions

Suppose host:

```text
./data
```

is mounted:

```text
/app/data
```

The container sees the host filesystem permissions.

Therefore:

```text
Host UID/GID
      ↓
Mount
      ↓
Container process UID/GID
```

If they do not align, writes may fail.

---

# 💥 25. Database Data Directory Warning

Never randomly modify or delete files inside a database data directory.

For example:

```text
/var/lib/postgresql/data
/var/lib/mysql
```

These directories contain database-managed internal structures.

Use:

```text
Database backup tools
Logical dumps
Snapshots
Documented restore procedures
```

rather than manually copying arbitrary files while the database is actively changing.

---

# 💾 26. Backup Strategy

A persistent volume is not a backup.

Think:

```text
Volume
  ↓
Primary data

Backup
  ↓
Recovery copy
```

You need both when durability matters.

Possible strategy:

```text
Application
    ↓
Volume
    ↓
Scheduled Backup
    ↓
Object Storage / Backup System
```

---

# 📦 27. Volume Backup Example

A common educational pattern is to mount the volume into a temporary container and create an archive.

Example:

```bash
docker run --rm \
  -v app-data:/data:ro \
  -v "$PWD":/backup \
  alpine:3.20 \
  tar czf /backup/app-data.tar.gz -C /data .
```

Concept:

```text
app-data volume
       │
       ▼
temporary backup container
       │
       ▼
tar.gz
```

For databases, prefer database-aware backup tools when appropriate.

---

# ♻️ 28. Volume Restore Example

Create a new volume:

```bash
docker volume create restored-data
```

Extract:

```bash
docker run --rm \
  -v restored-data:/data \
  -v "$PWD":/backup \
  alpine:3.20 \
  tar xzf /backup/app-data.tar.gz -C /data
```

Then attach:

```bash
docker run --rm \
  -v restored-data:/data \
  alpine:3.20 \
  ls -la /data
```

---

# 🧠 29. Database Backup vs Volume Backup

### Logical database backup

Examples:

```text
pg_dump
mysqldump
```

Advantages:

```text
Portable
Database-aware
Selective restore possible
```

### Volume/file-level backup

Advantages:

```text
Simple filesystem copy model
Useful for application files
Can be used with appropriate database procedures
```

For live databases, coordinate backup consistency with the database engine.

---

# 🚨 30. Common Backup Mistake

Bad assumption:

```text
"I have a Docker volume, therefore I have a backup."
```

Wrong.

Correct:

```text
Volume
+
Backup
+
Restore test
=
Recovery capability
```

A backup that has never been restored/tested should not be treated as proven recoverability.

---

# 🔄 31. Volume Migration Concept

Suppose:

```text
Host A
  ↓
app-data
```

You need:

```text
Host B
  ↓
app-data
```

Concept:

```text
Host A
  │
  ▼
Backup/export
  │
  ▼
Transfer
  │
  ▼
Host B
  │
  ▼
Restore
```

Do not assume a Docker volume can simply be copied between hosts without considering the storage backend, application consistency, filesystem metadata, and architecture.

---

# 🧩 32. Volume Drivers

Docker supports volume drivers/plugins for storage backends.

Concept:

```text
Container
   ↓
Docker Volume
   ↓
Volume Driver
   ↓
Storage Backend
```

Depending on environment, storage could integrate with external systems.

Use external volume drivers only when there is a real requirement and verify maintenance/support/security.

---

# ☁️ 33. Docker Storage in Cloud

In cloud environments, consider:

```text
Docker host
   ↓
Local volume
```

versus:

```text
Docker host
   ↓
External persistent storage
```

Examples of external storage concepts:

```text
Block storage
Network file systems
Managed databases
Object storage
Backup services
```

For production databases, a managed database service may be preferable to running a database container, depending on operational requirements.

---

# 🏢 34. Production Architecture

Instead of:

```text
Container
  ↓
Local writable layer
```

Use:

```text
Application
    │
    ▼
Persistent volume
    │
    ▼
Backup
    │
    ▼
Recovery location
```

For critical data:

```text
Primary
  +
Backup
  +
Monitoring
  +
Restore testing
```

---

# 🐳 35. Docker Compose + Volumes

Example:

```yaml
services:
  db:
    image: postgres:17
    environment:
      POSTGRES_PASSWORD: example
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

Start:

```bash
docker compose up -d
```

The named volume is managed as part of the Compose project.

---

# 🔗 36. Compose Frontend + Backend + Database

```yaml
services:

  frontend:
    image: my-frontend:1.0
    ports:
      - "8080:80"

  backend:
    image: my-backend:1.0

  db:
    image: postgres:17
    environment:
      POSTGRES_PASSWORD: example
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

Concept:

```text
Frontend
   │
   ▼
Backend
   │
   ▼
PostgreSQL
   │
   ▼
postgres_data
```

Compose automatically creates a project network unless configured otherwise.

---

# 🔐 37. Secrets and Storage

Do not store:

```text
Passwords
API keys
Private keys
Cloud credentials
```

inside application data volumes without a deliberate security design.

Separate:

```text
Application data
Secrets
Configuration
Backups
Logs
```

Use the secret/configuration mechanisms appropriate to your deployment platform.

---

# 🧹 38. Cleanup Commands

List:

```bash
docker volume ls
```

Inspect:

```bash
docker volume inspect app-data
```

Remove:

```bash
docker volume rm app-data
```

Prune:

```bash
docker volume prune
```

Before removing:

```text
Ask:
Is the volume used?
Is the data backed up?
Is it needed for another project?
```

---

# 🚨 39. Troubleshooting Flow

When application cannot write:

```text
START
  │
  ▼
Is the mount attached?
  │
  ▼
Correct destination?
  │
  ▼
Correct volume/bind source?
  │
  ▼
Does path exist?
  │
  ▼
Does container UID/GID have permission?
  │
  ▼
Is filesystem read-only?
  │
  ▼
Is the application using another path?
  │
  ▼
SUCCESS
```

Commands:

```bash
docker inspect container
docker volume inspect volume
docker exec -it container id
docker exec -it container ls -la /data
docker logs container
```

---

# 🚨 40. Common Problem — Data Disappeared

Check:

```text
Did you use a named volume?
Did you use an anonymous volume?
Did you use a bind mount?
Did you mount the correct destination?
Did you remove the volume?
Did you accidentally run `volume prune`?
Did the application write somewhere else?
```

---

# 🚨 41. Common Problem — Empty Directory

Possible cause:

```text
Host bind mount
     ↓
Mount hides image contents
```

If an image contains:

```text
/app/data/default.txt
```

and you mount:

```text
./data:/app/data
```

the mounted host directory becomes the visible content at that path.

This surprises many beginners.

---

# 🚨 42. Common Problem — Permission Denied

Check:

```bash
docker exec -it app id
docker exec -it app ls -ld /data
```

Then compare with host permissions for bind mounts.

Fix the ownership/permissions according to your security requirements rather than using broad permissions such as `chmod 777` as a default solution.

---

# 🧪 43. Hands-On Labs

## Lab 01 — Named Volume

Create and attach a volume.

---

## Lab 02 — Data Persistence

Create data.

Delete container.

Recreate container.

Verify data.

---

## Lab 03 — Bind Mount

Mount the current project directory.

Modify a host file.

Observe it from the container.

---

## Lab 04 — Read-Only Mount

Mount a directory as:

```text
readonly
```

Attempt a write.

Observe failure.

---

## Lab 05 — tmpfs

Create:

```bash
docker run --rm --tmpfs /tmp alpine:3.20
```

Write data.

Destroy container.

Explain why it disappears.

---

## Lab 06 — PostgreSQL Persistence

Create:

```text
postgres-data
```

Create a database.

Remove container.

Recreate.

Verify.

---

## Lab 07 — MySQL Persistence

Repeat with MySQL.

---

## Lab 08 — Redis

Attach persistent storage and test behavior according to configured Redis persistence.

---

## Lab 09 — Volume Inspection

Use:

```bash
docker volume inspect
```

Document the result.

---

## Lab 10 — Permissions

Run an application as a non-root user.

Mount a volume.

Diagnose permission errors.

---

## Lab 11 — Read-Only Database Backup

Mount a volume read-only into a temporary container.

Create an archive.

---

## Lab 12 — Restore

Create a new volume.

Restore the archive.

Verify files.

---

## Lab 13 — Database Logical Backup

Use PostgreSQL:

```text
pg_dump
```

Create a dump.

Restore it into a fresh database.

---

## Lab 14 — Compose Volume

Create a PostgreSQL Compose project with:

```yaml
volumes:
  postgres_data:
```

---

## Lab 15 — Compose Restart

Stop:

```bash
docker compose down
```

Start:

```bash
docker compose up -d
```

Verify data.

---

## Lab 16 — Bind Mount Development

Create a Python application.

Mount source code.

Modify code without rebuilding the image.

---

## Lab 17 — Volume vs Bind

Run the same application with:

```text
Volume
Bind mount
```

Document differences.

---

## Lab 18 — tmpfs Security Experiment

Place temporary data in:

```text
tmpfs
```

Restart/remove container.

Explain lifecycle.

---

## Lab 19 — Volume Migration

Export a volume.

Transfer archive.

Restore into another volume.

---

## Lab 20 — Backup Failure Simulation

Delete test data.

Restore from backup.

Measure:

```text
RTO
```

and discuss:

```text
RPO
```

---

## Lab 21 — Database Recovery

Simulate:

```text
Database container failure
```

Recover using a tested backup strategy.

---

## Lab 22 — Storage Security Review

Review:

```text
Permissions
Read-only mounts
Host paths
Secrets
Backups
Encryption
```

---

## Lab 23 — Volume Cleanup

Create unused test volumes.

Identify them.

Remove only safe volumes.

---

## Lab 24 — Three-Tier Storage

Build:

```text
Frontend
Backend
Database
```

Only database has persistent storage.

---

## Lab 25 — Production Storage Design

Design:

```text
Application
   ↓
Persistent storage
   ↓
Backup
   ↓
Off-host recovery
```

Explain failure scenarios.

---

# 🧮 44. RPO and RTO

Two important disaster-recovery concepts:

### RPO — Recovery Point Objective

How much data loss is acceptable?

Example:

```text
RPO = 15 minutes
```

Means the organization may accept losing up to roughly 15 minutes of data depending on the backup/replication design.

### RTO — Recovery Time Objective

How quickly must service/data be restored?

Example:

```text
RTO = 30 minutes
```

The recovery process should be designed and tested to meet the target.

---

# 🏢 45. Production Database Thinking

For production:

```text
Container
   ↓
Database
   ↓
Persistent storage
   ↓
Backup
   ↓
Off-host copy
   ↓
Restore testing
```

Also consider:

```text
Replication
Monitoring
Encryption
Access control
Patch management
Disaster recovery
```

For many production systems, managed database services can reduce operational burden compared with self-managing database containers.

---

# 🎓 46. Interview Questions

## Beginner

1. Why do containers need persistent storage?
2. What happens to data in the writable container layer?
3. What is a Docker volume?
4. What is a named volume?
5. What is a bind mount?
6. What is tmpfs?
7. What does `docker volume ls` do?
8. What does `docker volume inspect` do?
9. How do you create a volume?
10. How do you attach a volume?

## Intermediate

11. Volume vs bind mount?
12. Volume vs tmpfs?
13. Why are volumes useful for databases?
14. What is `--mount`?
15. What is `-v`?
16. How do you make a mount read-only?
17. Why can permission errors happen?
18. Why does a bind mount hide image contents?
19. How do you back up a volume?
20. Is a volume a backup?

## Advanced

21. Design PostgreSQL persistence using Docker.
22. How would you back up a production database?
23. Why should live database files not be copied blindly?
24. How would you migrate storage between hosts?
25. How do volume drivers work?
26. How do Docker volumes relate to cloud storage?
27. Design RPO/RTO for a containerized database.
28. How would you secure database storage?
29. How would you troubleshoot a permission-denied volume?
30. Design production storage for a three-tier application.

---

# 🏆 47. Production Checklist

```text
☑ Important data is externalized
☑ Named volumes used where appropriate
☑ Bind mounts limited to justified use cases
☑ tmpfs used for appropriate temporary data
☑ Database persistence configured
☑ Permissions reviewed
☑ Non-root application considered
☑ Sensitive data protected
☑ Backup strategy exists
☑ Restore tested
☑ RPO defined
☑ RTO defined
☑ Off-host backup considered
☑ Storage encryption considered
☑ Monitoring configured
☑ Volume cleanup controlled
```

---

# ⚡ 48. Storage Cheat Sheet

```bash
# Create
docker volume create app-data

# List
docker volume ls

# Inspect
docker volume inspect app-data

# Run with volume
docker run -v app-data:/data IMAGE

# Explicit mount
docker run \
  --mount type=volume,src=app-data,dst=/data \
  IMAGE

# Bind mount
docker run \
  --mount type=bind,src="$PWD",dst=/app \
  IMAGE

# Read-only
docker run \
  --mount type=volume,src=app-data,dst=/data,readonly \
  IMAGE

# tmpfs
docker run \
  --tmpfs /tmp \
  IMAGE

# Remove
docker volume rm app-data

# Prune unused
docker volume prune
```

---

# 🗺️ 49. What's Next?

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
11 Docker Volumes          ← 🟢 YOU ARE HERE
       ↓
12 Docker Compose
       ↓
13 Docker Registry
       ↓
14 Docker Security
       ↓
15 Docker + GitHub Actions
```

## 👉 [12 — Docker Compose](../12-Docker-Compose/)

Next we combine everything:

```text
Dockerfile
    +
Images
    +
Containers
    +
Networks
    +
Volumes
    ↓
Docker Compose
```

We will build complete:

```text
Frontend
   ↓
Backend
   ↓
PostgreSQL
   ↓
Persistent Volume
```

projects with environment variables, health checks, dependencies, profiles, networks, volumes, development workflows, and CI/CD integration.

---

<div align="center">

# 💾 Containers Can Be Replaced. Data Must Be Designed to Survive.

### VishwaTech Labs

**Docker • GitHub Actions • Terraform • Ansible • Kubernetes • DevOps • Cloud Security**

### By Vishwanath Gowda H

⭐ Persist → Backup → Restore → Secure → Recover

</div>
