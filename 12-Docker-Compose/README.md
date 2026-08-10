<div align="center">

# 🐳 Docker Compose — Complete Zero-to-Hero Masterclass

### 🚀 Multi-Container Applications | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/compose/)
[![YAML](https://img.shields.io/badge/Config-YAML-orange?logo=yaml&logoColor=white)](https://yaml.org/)
[![Networking](https://img.shields.io/badge/Networking-Services-blue)](#-compose-networking)
[![Storage](https://img.shields.io/badge/Storage-Volumes-success)](#-compose-volumes)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-Ready-blueviolet)](#-compose-in-cicd)
[![Labs](https://img.shields.io/badge/Labs-25+-purple)](#-hands-on-labs)

**Define, run, network, persist, test, and manage complete multi-container applications with one declarative Compose file.**

[📘 Docker Compose](https://docs.docker.com/compose/) •
[📄 Compose File Reference](https://docs.docker.com/reference/compose-file/) •
[🧪 Compose CLI](https://docs.docker.com/reference/cli/docker/compose/)

</div>

---

# 🎯 What You Will Learn

Docker Compose lets you describe an application made of multiple services in a YAML file.

Instead of running:

```bash
docker run ...
docker network create ...
docker volume create ...
docker run ...
docker run ...
```

you can describe the desired application:

```text
                 docker-compose.yml
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
          frontend     backend     database
             │           │           │
             └───────────┼───────────┘
                         │
                    Networks
                         │
                      Volumes
```

You will learn:

- Compose fundamentals
- YAML structure
- Services
- Images
- Build
- Ports
- Networks
- Volumes
- Environment variables
- `.env`
- Healthchecks
- Service dependencies
- Restart policies
- Profiles
- Resource configuration concepts
- Development workflows
- Production considerations
- PostgreSQL
- Redis
- Frontend + backend + database
- Troubleshooting
- CI/CD
- 25+ hands-on labs

---

# 🧠 1. What Is Docker Compose?

Docker Compose is a tool for defining and running multi-container applications using a Compose file.

Concept:

```text
Compose File
     │
     ▼
Desired Application State
     │
     ├── Services
     ├── Networks
     ├── Volumes
     ├── Environment
     └── Configuration
     │
     ▼
Docker Engine
```

A Compose project can contain:

```text
frontend
backend
database
cache
worker
reverse proxy
```

---

# 🏗️ 2. Why Compose?

Without Compose:

```text
Many docker run commands
Many environment variables
Manual networks
Manual volumes
Hard-to-repeat setup
```

With Compose:

```text
compose.yaml
     ↓
docker compose up
     ↓
Complete application
```

This makes local development and many test/integration environments much easier to reproduce.

---

# 📁 3. Compose File Names

Modern Docker Compose commonly uses:

```text
compose.yaml
```

You may also see:

```text
compose.yml
docker-compose.yaml
docker-compose.yml
```

Follow the naming convention used by your project.

For this course, we will generally use:

```text
compose.yaml
```

---

# 🧩 4. Minimal Compose File

```yaml
services:
  web:
    image: nginx:stable-alpine
    ports:
      - "8080:80"
```

Start:

```bash
docker compose up -d
```

Check:

```bash
docker compose ps
```

Stop:

```bash
docker compose down
```

---

# 🧠 5. Compose File Structure

Think:

```yaml
services:
  frontend:
    ...

  backend:
    ...

  database:
    ...

networks:
  ...

volumes:
  ...
```

Architecture:

```text
compose.yaml
│
├── services
│   ├── frontend
│   ├── backend
│   └── database
│
├── networks
│
└── volumes
```

---

# 🐳 6. Service

A service describes a containerized application component.

Example:

```yaml
services:
  web:
    image: nginx:stable-alpine
```

The service name is:

```text
web
```

Compose uses service names for:

```text
Configuration
Networking
DNS/service discovery
Lifecycle management
```

---

# 🖼️ 7. `image`

Example:

```yaml
services:
  web:
    image: nginx:stable-alpine
```

Compose pulls/uses the specified image as needed.

Run:

```bash
docker compose up -d
```

---

# 🔨 8. `build`

Instead of using an existing image:

```yaml
services:
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
```

Concept:

```text
backend/
├── Dockerfile
├── app.py
└── requirements.txt
```

Compose:

```text
build context
      ↓
Dockerfile
      ↓
Image
      ↓
Container
```

---

# 🆚 9. `image` vs `build`

### `image`

```yaml
image: nginx:stable-alpine
```

Use an existing image.

### `build`

```yaml
build:
  context: ./backend
```

Build an image from source.

### Both

```yaml
services:
  backend:
    build:
      context: ./backend
    image: my-backend:1.0
```

This can give the built image a specific name/tag.

---

# 🔌 10. `ports`

Example:

```yaml
ports:
  - "8080:80"
```

Means:

```text
Host:8080
     ↓
Container:80
```

Same concept as:

```bash
docker run -p 8080:80 nginx
```

---

# 🔐 11. Bind Port to Localhost

Instead of:

```yaml
ports:
  - "8080:80"
```

you can restrict host exposure:

```yaml
ports:
  - "127.0.0.1:8080:80"
```

This can be useful for local-only services.

Do not assume this alone replaces host firewall/network security.

---

# 🌐 12. Compose Networking

Compose creates a project network by default for services.

Example:

```yaml
services:
  backend:
    image: my-backend:1.0

  db:
    image: postgres:17
```

Concept:

```text
backend ─────┐
             │
         Compose Network
             │
db ──────────┘
```

The backend can generally reach the database using its service name:

```text
db
```

Example:

```text
postgresql://db:5432/app
```

---

# 🧠 13. Service Name = DNS Name

Suppose:

```yaml
services:
  backend:
    ...
  database:
    ...
```

Inside the Compose network:

```text
database
```

can be used as the hostname for the database service.

Do not use:

```text
localhost
```

for another service.

Remember:

```text
localhost
   ↓
Current container

database
   ↓
Database service
```

---

# 🏗️ 14. Three-Tier Compose Architecture

```text
                 🌐 Browser
                     │
                     ▼
                Frontend
                     │
                     ▼
                 Backend
                     │
                     ▼
                PostgreSQL
```

Compose:

```text
services:
├── frontend
├── backend
└── db
```

---

# 🧪 15. Complete Three-Tier Example

```yaml
services:

  frontend:
    image: nginx:stable-alpine
    ports:
      - "8080:80"
    depends_on:
      - backend

  backend:
    build:
      context: ./backend
    environment:
      DATABASE_URL: postgresql://postgres:example@db:5432/app
    depends_on:
      - db

  db:
    image: postgres:17
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: example
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

This is a **training example**. Real production credentials should not be hard-coded in the Compose file.

---

# 💾 16. Compose Volumes

Define:

```yaml
volumes:
  postgres_data:
```

Use:

```yaml
services:
  db:
    image: postgres:17
    volumes:
      - postgres_data:/var/lib/postgresql/data
```

Concept:

```text
Compose
   │
   ▼
Named Volume
   │
   ▼
Database
```

---

# 📂 17. Bind Mounts in Compose

Development example:

```yaml
services:
  backend:
    build: ./backend
    volumes:
      - ./backend:/app
```

Useful for local development.

Be careful in production because bind mounts tightly couple the container to the host filesystem.

---

# 🧪 18. Read-Only Mount

Example:

```yaml
services:
  web:
    image: nginx:stable-alpine
    volumes:
      - ./config:/etc/nginx/conf.d:ro
```

This gives the container read-only access to that mount.

---

# 🌍 19. Environment Variables

Example:

```yaml
services:
  backend:
    environment:
      APP_ENV: development
      PORT: "8000"
```

The application receives:

```text
APP_ENV=development
PORT=8000
```

---

# 📄 20. `.env`

Create:

```text
.env
```

Example:

```dotenv
APP_ENV=development
POSTGRES_DB=app
POSTGRES_USER=postgres
POSTGRES_PASSWORD=change-me
```

Compose can use variables from the project environment and `.env` for interpolation.

Example:

```yaml
services:
  db:
    image: postgres:17
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
```

Do not commit real secrets.

Add:

```text
.env
```

to:

```text
.gitignore
```

when appropriate.

---

# 🔐 21. Environment Variables Are Not a Secret Vault

Do not assume:

```text
.env
```

automatically means:

```text
secure
```

Secrets can appear in:

```text
Process environment
CI logs
Configuration
Shell history
Application logs
Container inspection
```

Use a proper secrets-management strategy for sensitive production credentials.

---

# 🏥 22. `healthcheck`

Example:

```yaml
services:
  backend:
    image: my-backend:1.0
    healthcheck:
      test:
        - CMD
        - curl
        - -f
        - http://localhost:8000/health
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 10s
```

A health check tells Docker how to evaluate container health.

It does not automatically guarantee that all dependencies are ready.

---

# ⛓️ 23. `depends_on`

Example:

```yaml
services:
  backend:
    depends_on:
      - db

  db:
    image: postgres:17
```

This expresses a startup dependency.

Important:

> Container startup order is not the same thing as application readiness.

A database container can be running while PostgreSQL is still initializing.

---

# 🏥 24. `depends_on` + Health

A more deliberate pattern can use health conditions where supported by the Compose specification/tooling:

```yaml
services:

  backend:
    build: ./backend
    depends_on:
      db:
        condition: service_healthy

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

Now Compose can coordinate startup based on the database health condition.

The application itself should still implement robust retry/readiness behavior.

---

# 🔄 25. Restart Policies

Example:

```yaml
services:
  backend:
    restart: unless-stopped
```

Common policy concepts:

```text
no
always
on-failure
unless-stopped
```

Choose according to the desired lifecycle.

A restart policy is not a replacement for:

```text
Monitoring
Health checks
Alerting
Application-level recovery
Orchestration
```

---

# 🧑‍💻 26. Development Workflow

Typical:

```bash
docker compose up -d
```

Then:

```bash
docker compose ps
docker compose logs
docker compose exec backend sh
```

Make code changes.

Rebuild when needed:

```bash
docker compose build backend
```

Restart:

```bash
docker compose restart backend
```

---

# 📜 27. Essential Compose Commands

Start:

```bash
docker compose up
```

Background:

```bash
docker compose up -d
```

Build:

```bash
docker compose build
```

Build + start:

```bash
docker compose up --build
```

Stop/remove containers and networks:

```bash
docker compose down
```

Logs:

```bash
docker compose logs
```

Follow logs:

```bash
docker compose logs -f
```

Service logs:

```bash
docker compose logs -f backend
```

List:

```bash
docker compose ps
```

Execute:

```bash
docker compose exec backend sh
```

Pull:

```bash
docker compose pull
```

Restart:

```bash
docker compose restart
```

---

# 🧹 28. `docker compose down`

Default:

```bash
docker compose down
```

Removes Compose-managed containers and networks created for the project.

Named volumes are normally preserved unless you explicitly request volume removal.

Dangerous:

```bash
docker compose down -v
```

This also removes the project's named volumes.

For a database, this can destroy persistent data.

Use with extreme care.

---

# 💥 29. The Dangerous Command

```bash
docker compose down -v
```

Think:

```text
Containers ❌
Networks   ❌
Named Volumes ❌
```

Before using it:

```text
Is database data important?
Is there a backup?
Is this only a lab?
```

---

# 🧪 30. `docker compose config`

Validate/render the Compose configuration:

```bash
docker compose config
```

Useful for checking:

```text
YAML structure
Variable interpolation
Merged configuration
Service definitions
```

Run this before troubleshooting a complicated Compose project.

---

# 🧩 31. Multiple Compose Files

You may have:

```text
compose.yaml
compose.dev.yaml
compose.prod.yaml
```

Then use multiple files where supported:

```bash
docker compose \
  -f compose.yaml \
  -f compose.dev.yaml \
  up -d
```

This allows environment-specific configuration patterns.

Keep overrides understandable; don't create an overly complex configuration hierarchy.

---

# 🎯 32. Profiles

Profiles allow optional services.

Example:

```yaml
services:

  app:
    image: myapp:1.0

  adminer:
    image: adminer
    profiles:
      - tools
```

Start normal:

```bash
docker compose up -d
```

Start tools profile:

```bash
docker compose \
  --profile tools \
  up -d
```

Useful for:

```text
Debug tools
Admin UI
Monitoring tools
Local-only services
```

---

# 🧪 33. Example Development Profile

```yaml
services:

  backend:
    build: ./backend

  db:
    image: postgres:17

  adminer:
    image: adminer
    profiles:
      - debug
```

Normal:

```bash
docker compose up -d
```

Debug:

```bash
docker compose --profile debug up -d
```

---

# 🏗️ 34. Compose Project Structure

Recommended training project:

```text
my-application/
│
├── compose.yaml
├── .env.example
├── .gitignore
│
├── frontend/
│   ├── Dockerfile
│   └── ...
│
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── ...
│
├── database/
│   └── init/
│
└── README.md
```

---

# 🐍 35. Python Backend + PostgreSQL

```yaml
services:

  backend:
    build:
      context: ./backend
    environment:
      DATABASE_URL: postgresql://postgres:example@db:5432/app
    ports:
      - "8000:8000"
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:17
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: example
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test:
        - CMD-SHELL
        - pg_isready -U postgres -d app
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

---

# 🟨 36. Node.js Backend + PostgreSQL

```yaml
services:

  backend:
    build:
      context: ./backend
    environment:
      DATABASE_URL: postgresql://postgres:example@db:5432/app
    ports:
      - "3000:3000"
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:17
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: example
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test:
        - CMD-SHELL
        - pg_isready -U postgres -d app
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

---

# 🔴 37. Redis + Backend + PostgreSQL

```yaml
services:

  backend:
    build: ./backend
    environment:
      DATABASE_URL: postgresql://postgres:example@db:5432/app
      REDIS_URL: redis://redis:6379
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy

  db:
    image: postgres:17
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: example
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test:
        - CMD-SHELL
        - pg_isready -U postgres -d app
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7
    healthcheck:
      test:
        - CMD
        - redis-cli
        - ping
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

---

# 🌐 38. Custom Networks

Example:

```yaml
services:

  frontend:
    networks:
      - frontend_net

  backend:
    networks:
      - frontend_net
      - backend_net

  db:
    networks:
      - backend_net

networks:
  frontend_net:
  backend_net:
```

Architecture:

```text
frontend
    │
frontend_net
    │
backend
    │
backend_net
    │
database
```

This limits direct connectivity.

---

# 🔐 39. Production Network Principle

Avoid:

```text
frontend ─┐
backend  ─┼── public network
database ─┘
```

Prefer:

```text
Internet
   ↓
Frontend / Proxy
   ↓
Backend
   ↓
Database
```

with private networks between internal services.

---

# 🏥 40. Healthcheck Strategy

Good healthcheck:

```text
Fast
Lightweight
Deterministic
Local
Meaningful
```

Bad healthcheck:

```text
Heavy database query
External API dependency
Long-running script
Random network dependency
```

Health checks should answer:

> "Is this service healthy enough for its intended role?"

---

# 📦 41. Build + Compose

Compose can build application images:

```yaml
services:
  backend:
    build:
      context: ./backend
```

Then:

```bash
docker compose build
```

or:

```bash
docker compose up --build
```

For CI:

```text
Source
 ↓
Compose/Build configuration
 ↓
Docker Build
 ↓
Tests
 ↓
Scan
 ↓
Image Registry
```

---

# 🔐 42. Production Secrets

Avoid:

```yaml
environment:
  DB_PASSWORD: super-secret-password
```

for real production secrets.

Better architecture:

```text
Secret Manager
      ↓
CI/CD / Runtime
      ↓
Application
```

Depending on the environment, use appropriate Docker/Compose secret mechanisms, CI secret stores, cloud secret managers, or orchestration-native secret systems.

---

# 🧪 43. Compose Secrets Concept

Compose supports secret-related configuration.

Concept:

```text
Secret
   ↓
Service
   ↓
Mounted/available through configured secret mechanism
```

Always verify the exact Compose implementation/version and platform behavior used by your project.

---

# 📊 44. Resource Configuration

Compose can express resource-related settings, but the exact behavior can vary by Docker environment and deployment mode.

Production planning should consider:

```text
CPU
Memory
PIDs
Disk
Network
Health
Restart behavior
```

Do not assume a Compose file automatically provides the same scheduling/resource behavior as Kubernetes.

---

# 🧠 45. Compose vs Kubernetes

### Docker Compose

Best for:

```text
Local development
Learning
Integration testing
Small environments
Single-host multi-container apps
```

### Kubernetes

Best suited for:

```text
Cluster orchestration
High availability
Scheduling
Service discovery
Scaling
Rolling deployments
Self-healing
Network policies
Large distributed systems
```

Mental model:

```text
Compose
  ↓
Application definition on Docker

Kubernetes
  ↓
Cluster orchestration platform
```

Compose is not a replacement for Kubernetes.

---

# 🔄 46. Compose Lifecycle

```text
compose.yaml
     │
     ▼
docker compose up
     │
     ├── Create networks
     ├── Create volumes
     ├── Pull/build images
     ├── Create containers
     └── Start services
     │
     ▼
Running application
     │
     ▼
docker compose down
     │
     ├── Remove containers
     └── Remove project networks
```

Named volumes remain unless explicitly removed.

---

# 🧪 47. Hands-On Labs

## Lab 01 — First Compose

Create:

```yaml
services:
  web:
    image: nginx:stable-alpine
    ports:
      - "8080:80"
```

Run:

```bash
docker compose up -d
```

---

## Lab 02 — Compose Commands

Practice:

```bash
docker compose up
docker compose ps
docker compose logs
docker compose down
```

---

## Lab 03 — Two Services

Create:

```text
frontend
backend
```

Verify DNS using service names.

---

## Lab 04 — Three Services

Create:

```text
frontend
backend
database
```

---

## Lab 05 — PostgreSQL Volume

Persist:

```text
/var/lib/postgresql/data
```

---

## Lab 06 — Database Persistence

Create a database.

Run:

```bash
docker compose down
docker compose up -d
```

Verify data.

---

## Lab 07 — Dangerous `down -v`

In a disposable lab:

```bash
docker compose down -v
```

Observe what happens to the named volume.

---

## Lab 08 — Environment Variables

Create:

```text
.env
```

Use variables in Compose.

---

## Lab 09 — `.env.example`

Create:

```text
.env.example
```

Commit the example.

Keep real `.env` out of source control.

---

## Lab 10 — Healthcheck

Add:

```text
database healthcheck
```

---

## Lab 11 — `depends_on`

Make backend depend on database.

Then add health-based startup coordination.

---

## Lab 12 — Redis

Add:

```text
Redis
```

to backend.

---

## Lab 13 — Custom Networks

Create:

```text
frontend_net
backend_net
```

Verify:

```text
frontend → backend
backend → database
frontend ✕ database
```

---

## Lab 14 — Bind Mount

Mount application source for development.

---

## Lab 15 — Read-Only Config

Mount Nginx configuration as:

```text
read-only
```

---

## Lab 16 — Profiles

Create:

```text
debug
```

profile containing Adminer or another development tool.

---

## Lab 17 — Build Services

Build:

```text
frontend
backend
```

using separate Dockerfiles.

---

## Lab 18 — Multiple Compose Files

Create:

```text
compose.yaml
compose.dev.yaml
```

Use overrides.

---

## Lab 19 — Config Validation

Run:

```bash
docker compose config
```

Fix any issues.

---

## Lab 20 — Logs

Practice:

```bash
docker compose logs -f backend
```

and identify an application failure.

---

## Lab 21 — Exec

Enter:

```bash
docker compose exec backend sh
```

Inspect:

```text
Environment
Network
Filesystem
Processes
```

---

## Lab 22 — Full Stack

Build:

```text
Nginx
   ↓
Node/Python API
   ↓
PostgreSQL
   ↓
Redis
```

---

## Lab 23 — Production Review

Review:

```text
Secrets
Ports
Networks
Volumes
Health
Restart
Images
```

---

## Lab 24 — CI Build

Run Compose validation/build in GitHub Actions.

---

## Lab 25 — Incident Simulation

Break:

```text
Database
```

Then troubleshoot:

```text
Logs
Health
DNS
Network
Credentials
Volume
```

---

# 🚨 48. Troubleshooting Flow

When a Compose application fails:

```text
docker compose ps
       ↓
Is service running?
       ↓
docker compose logs SERVICE
       ↓
What failed?
       ↓
Check environment
       ↓
Check DNS/service name
       ↓
Check port
       ↓
Check health
       ↓
Check volume
       ↓
Check dependencies
       ↓
Fix
```

Useful:

```bash
docker compose ps
docker compose logs -f
docker compose config
docker compose exec SERVICE sh
docker compose images
docker network ls
docker volume ls
```

---

# 🚨 49. Problem — Backend Cannot Reach Database

Check:

```text
Database service name
Database port
Compose network
Database health
Credentials
Application connection string
```

Correct concept:

```text
db:5432
```

Not:

```text
localhost:5432
```

---

# 🚨 50. Problem — Database Starts but Backend Fails

Possible reason:

```text
Database container running
≠
Database ready
```

Use:

```text
healthcheck
+
application retry logic
```

Do not rely only on container startup order.

---

# 🚨 51. Problem — Data Lost

Check:

```text
Was a named volume used?
Was the volume removed?
Was `down -v` executed?
Was the application writing to another path?
Was the correct volume mounted?
```

---

# 🚨 52. Problem — Port Already in Use

Example:

```text
Bind for 0.0.0.0:8080 failed
```

Find the process/container using the port.

Then:

```text
Change host port
or
Stop conflicting service
```

Remember:

```text
8080:80
```

means host 8080 → container 80.

---

# 🚨 53. Problem — Environment Variable Missing

Run:

```bash
docker compose config
```

Check:

```text
Variable interpolation
.env
Shell environment
Variable names
```

Do not commit secrets while debugging.

---

# 🧪 54. Production-Style Compose Example

```yaml
services:

  reverse-proxy:
    image: nginx:stable-alpine
    ports:
      - "80:80"
    depends_on:
      backend:
        condition: service_healthy
    networks:
      - public_net

  backend:
    build:
      context: ./backend
    environment:
      APP_ENV: production
      DATABASE_URL: postgresql://postgres:example@db:5432/app
    healthcheck:
      test:
        - CMD
        - curl
        - -f
        - http://localhost:8000/health
      interval: 30s
      timeout: 5s
      retries: 3
    networks:
      - public_net
      - private_net

  db:
    image: postgres:17
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: example
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test:
        - CMD-SHELL
        - pg_isready -U postgres -d app
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - private_net

networks:
  public_net:
  private_net:

volumes:
  postgres_data:
```

This is a **training architecture**, not a drop-in production security configuration. Replace example credentials with a proper secret strategy.

---

# 🔐 55. Production Compose Checklist

```text
☑ Images pinned/controlled
☑ Secrets managed securely
☑ Minimal public ports
☑ Database private
☑ Healthchecks
☑ Application retries
☑ Persistent volumes
☑ Backups
☑ Non-root containers where appropriate
☑ Read-only mounts where practical
☑ Network segmentation
☑ Logs
☑ Monitoring
☑ Resource planning
☑ CI validation
☑ Image scanning
☑ Disaster recovery plan
```

---

# 🎓 56. Interview Questions

## Beginner

1. What is Docker Compose?
2. What is a Compose file?
3. What is a service?
4. What does `docker compose up` do?
5. What does `docker compose down` do?
6. What is `image`?
7. What is `build`?
8. What is `ports`?
9. What is `volumes`?
10. What is `environment`?

## Intermediate

11. How does Compose networking work?
12. How do services communicate?
13. Why should you use service names instead of container IPs?
14. What is `depends_on`?
15. Why is `depends_on` alone not readiness?
16. What is a healthcheck?
17. What does `docker compose down -v` do?
18. What is `.env`?
19. What are profiles?
20. How do multiple Compose files work?

## Advanced

21. Design a three-tier Compose application.
22. How would you secure database access?
23. How would you design private/public networks?
24. How would you manage production secrets?
25. How would you make a database persistent?
26. How would you troubleshoot service DNS?
27. How would you troubleshoot a healthcheck?
28. Compose vs Kubernetes?
29. How would you integrate Compose into GitHub Actions?
30. Design a production-like Compose stack with proxy, backend, database, cache, volumes, healthchecks, and private networking.

---

# 🏆 57. Knowledge Checklist

Before moving to the next module:

- [ ] Compose file syntax
- [ ] Services
- [ ] Images
- [ ] Build
- [ ] Ports
- [ ] Networks
- [ ] Service DNS
- [ ] Volumes
- [ ] Bind mounts
- [ ] Environment variables
- [ ] `.env`
- [ ] Secrets concepts
- [ ] Healthchecks
- [ ] `depends_on`
- [ ] Restart policies
- [ ] Profiles
- [ ] Multiple Compose files
- [ ] `docker compose config`
- [ ] Logs
- [ ] Exec
- [ ] Database persistence
- [ ] Three-tier architecture
- [ ] Network segmentation
- [ ] CI/CD integration
- [ ] Troubleshooting

---

# ⚡ 58. Docker Compose Cheat Sheet

```bash
# Start
docker compose up

# Start detached
docker compose up -d

# Build + start
docker compose up --build

# Build
docker compose build

# List services
docker compose ps

# Logs
docker compose logs

# Follow logs
docker compose logs -f

# Service logs
docker compose logs -f backend

# Execute command
docker compose exec backend sh

# Pull images
docker compose pull

# Restart
docker compose restart

# Validate/render config
docker compose config

# Stop/remove containers and networks
docker compose down

# Remove volumes too - DANGEROUS
docker compose down -v

# Start a profile
docker compose --profile debug up -d
```

---

# 🗺️ 59. What's Next?

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
12 Docker Compose          ← 🟢 YOU ARE HERE
       ↓
13 Docker Registry
       ↓
14 Docker Security
       ↓
15 Docker + GitHub Actions
```

## 👉 [13 — Docker Registry](../13-Docker-Registry/)

Next we move from:

```text
Build locally
     ↓
Run locally
```

to:

```text
Build Image
     ↓
Tag Image
     ↓
Authenticate
     ↓
Push
     ↓
Container Registry
     ↓
Pull
     ↓
Deploy
```

We will cover:

```text
Docker Hub
Private Registries
Amazon ECR
Authentication
Image Tags
Digest
Push/Pull
Registry Security
GitHub Actions
Image Promotion
CI/CD
```

---

<div align="center">

# 🐳 One File. Many Services. One Repeatable Application.

### VishwaTech Labs

**Docker • GitHub Actions • Terraform • Ansible • Kubernetes • DevOps • Cloud Security**

### By Vishwanath Gowda H

⭐ Define → Build → Network → Persist → Test → Automate

</div>
