<div align="center">

# 🐳 Docker Installation & Setup

### 🚀 Zero to Hero | VishwaTech Labs

[![Docker](https://img.shields.io/badge/Docker-Installation-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Windows](https://img.shields.io/badge/Windows-Docker%20Desktop-0078D4?logo=windows&logoColor=white)](https://docs.docker.com/desktop/setup/install/windows-install/)
[![Linux](https://img.shields.io/badge/Linux-Docker%20Engine-FCC624?logo=linux&logoColor=black)](https://docs.docker.com/engine/install/)
[![WSL 2](https://img.shields.io/badge/Windows-WSL%202-4D4D4D?logo=linux&logoColor=white)](https://docs.docker.com/desktop/features/wsl/)
[![Hands-On](https://img.shields.io/badge/Learning-Hands--On-orange)](#-hands-on-lab)
[![VishwaTech Labs](https://img.shields.io/badge/Training-VishwaTech%20Labs-black)](#)

**Install Docker correctly, understand the runtime, verify the environment, and run your first real container.**

[📘 Docker Desktop](https://docs.docker.com/desktop/) •
[🪟 Windows Install](https://docs.docker.com/desktop/setup/install/windows-install/) •
[🐧 Docker Engine](https://docs.docker.com/engine/install/) •
[🔧 Ubuntu Install](https://docs.docker.com/engine/install/ubuntu/) •
[🐧 WSL 2](https://docs.docker.com/desktop/features/wsl/)

</div>

---

# 🎯 What You Will Learn

By the end of this module, you should be able to:

```text
Understand Docker installation options
        ↓
Choose Docker Desktop or Docker Engine
        ↓
Install Docker on Windows
        ↓
Understand WSL 2
        ↓
Install Docker Engine on Ubuntu
        ↓
Verify Docker CLI + Engine
        ↓
Run hello-world
        ↓
Run Nginx
        ↓
Check Docker service
        ↓
Troubleshoot common installation problems
```

---

# 🧭 1. Choose Your Installation Method

For students, the simplest path depends on the operating system:

| Environment | Recommended learning path |
|---|---|
| 🪟 Windows 10/11 | **Docker Desktop + WSL 2** |
| 🍎 macOS | **Docker Desktop** |
| 🐧 Ubuntu Server | **Docker Engine** |
| ☁️ Linux Cloud VM | **Docker Engine** |
| 🧪 Windows Server | Follow the Windows Server/container-specific documentation |

Docker's current documentation provides separate installation paths for Docker Desktop and Docker Engine. citeturn0search2turn0search8

---

# 🪟 2. Windows — Recommended Student Setup

For most Windows students in this course:

```text
Windows 10 / Windows 11
          │
          ▼
        WSL 2
          │
          ▼
   Docker Desktop
          │
          ▼
    Docker Engine
          │
          ▼
      Docker CLI
          │
          ▼
      Containers
```

Docker's current Windows installation documentation recommends the per-user installation mode for most users, with WSL 2 covering the needs of most Docker Desktop users. citeturn0search1

---

# ⚙️ 3. Windows Prerequisites

Before installing Docker Desktop, check the system requirements.

Docker currently documents Windows requirements including supported Windows 10/11 editions/builds, WSL 2, hardware virtualization, and a 64-bit processor with SLAT. The exact supported Windows versions can change, so always verify against the official Docker page before teaching students to install a specific release. citeturn0search1

### Check Windows version

Press:

```text
Win + R
```

Then:

```text
winver
```

Or PowerShell:

```powershell
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion, OsBuildNumber
```

### Check virtualization

Open:

```text
Task Manager
   ↓
Performance
   ↓
CPU
   ↓
Virtualization
```

You want:

```text
Virtualization: Enabled
```

If it is disabled, hardware virtualization may need to be enabled in BIOS/UEFI.

---

# 🐧 4. What Is WSL 2?

**WSL = Windows Subsystem for Linux**

WSL 2 provides a real Linux kernel environment on Windows and is a common backend for Docker Desktop on Windows.

Think:

```text
                 WINDOWS
                    │
                    ▼
                 WSL 2
                    │
              Linux Kernel
                    │
                    ▼
              Docker Desktop
                    │
                    ▼
              Docker Engine
                    │
                    ▼
               Containers
```

Docker's WSL documentation recommends keeping WSL up to date and currently lists WSL 2.1.5 or later as the minimum for Docker Desktop's WSL 2 backend. citeturn0search5turn0search6

---

# 🔍 5. Check WSL

Open **PowerShell**:

```powershell
wsl --version
```

Check installed distributions:

```powershell
wsl --list --verbose
```

If WSL is not installed, Microsoft/Docker documentation provides the supported installation process.

A common command is:

```powershell
wsl --install
```

Then update:

```powershell
wsl --update
```

Docker's current Windows documentation explicitly documents these commands for installing/updating WSL. citeturn0search1

> ⚠️ Restart may be required after enabling WSL features.

---

# 🐳 6. Install Docker Desktop on Windows

Official installation guide:

👉 [Install Docker Desktop on Windows](https://docs.docker.com/desktop/setup/install/windows-install/)

Basic workflow:

```text
1️⃣ Verify Windows
       ↓
2️⃣ Enable hardware virtualization
       ↓
3️⃣ Install / update WSL 2
       ↓
4️⃣ Download Docker Desktop
       ↓
5️⃣ Install Docker Desktop
       ↓
6️⃣ Start Docker Desktop
       ↓
7️⃣ Verify Docker CLI
       ↓
8️⃣ Run hello-world
```

Docker currently supports a per-user installation mode that is recommended for most users. citeturn0search1

---

# 🖥️ 7. Start Docker Desktop

After installation:

```text
Start Menu
    ↓
Docker Desktop
    ↓
Wait for Docker Engine to start
```

When Docker Desktop is ready, verify from PowerShell:

```powershell
docker version
```

Then:

```powershell
docker info
```

---

# ✅ 8. Verify Docker Installation

Run:

```bash
docker version
```

You should see client/server information when the Docker Engine is reachable.

Then:

```bash
docker info
```

This displays information about:

- Containers
- Images
- Storage
- Server configuration
- Plugins
- Runtime
- Architecture

---

# 🧪 9. Your First Verification Container

Run:

```bash
docker run hello-world
```

What happens?

```text
Docker CLI
    │
    ▼
Docker Engine
    │
    ▼
Check local image
    │
    ├── Exists → Use it
    │
    └── Missing
           ↓
       Pull image
           ↓
       Create container
           ↓
       Run container
           ↓
       Display message
           ↓
       Container exits
```

Docker's official Ubuntu installation guide also uses `docker run hello-world` as the installation verification step. citeturn0search0

---

# 🌐 10. Run Your First Web Server

Now run:

```bash
docker run -d --name my-nginx -p 8080:80 nginx
```

Understand every part:

```text
docker run
   │
   ├── -d
   │    Detached mode
   │
   ├── --name my-nginx
   │    Container name
   │
   ├── -p 8080:80
   │    Host 8080 → Container 80
   │
   └── nginx
        Image
```

Check:

```bash
docker ps
```

Open:

```text
http://localhost:8080
```

---

# 🛑 11. Stop and Remove the Container

Stop:

```bash
docker stop my-nginx
```

Start again:

```bash
docker start my-nginx
```

Stop:

```bash
docker stop my-nginx
```

Remove:

```bash
docker rm my-nginx
```

Verify:

```bash
docker ps -a
```

---

# 🐧 12. Linux — Docker Engine

For Linux servers, Docker Engine is the normal server-side installation path.

Official documentation:

👉 [Install Docker Engine](https://docs.docker.com/engine/install/)

Supported Linux installation documentation currently includes distributions such as:

```text
Ubuntu
Debian
Fedora
RHEL
CentOS
```

and other supported platforms/architectures. Always check the official installation page for the current support matrix. citeturn0search2

---

# 🟠 13. Ubuntu — Recommended Docker Engine Installation

Official guide:

👉 [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

Docker's current Ubuntu instructions recommend installing Docker Engine from Docker's official `apt` repository. citeturn0search0

### Step 1 — Update packages

```bash
sudo apt update
```

### Step 2 — Install prerequisites

```bash
sudo apt install ca-certificates curl
```

### Step 3 — Create keyring directory

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

### Step 4 — Download Docker's official signing key

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
```

### Step 5 — Set permissions

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### Step 6 — Add Docker repository

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

### Step 7 — Update package index

```bash
sudo apt update
```

### Step 8 — Install Docker Engine

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

These package names match Docker's current Ubuntu installation documentation. citeturn0search0

---

# 🔎 14. Verify Docker Engine on Ubuntu

Check service:

```bash
sudo systemctl status docker
```

If it is not running:

```bash
sudo systemctl start docker
```

Enable at boot:

```bash
sudo systemctl enable docker
```

Verify:

```bash
sudo docker version
```

Run:

```bash
sudo docker run hello-world
```

---

# 👤 15. Run Docker Without `sudo` on Linux

By default, Docker's Unix socket is owned by root, so ordinary users may need `sudo`.

Docker documents an optional post-installation method using the `docker` group. citeturn0search3

Create the group if needed:

```bash
sudo groupadd docker
```

Add your user:

```bash
sudo usermod -aG docker $USER
```

Apply the new group membership by logging out/in, or use:

```bash
newgrp docker
```

Test:

```bash
docker run hello-world
```

> ⚠️ Important security note: membership in the `docker` group effectively grants high privileges on the host. Treat access to the Docker daemon as privileged access.

---

# 🧹 16. Check for Conflicting Linux Packages

Before installing Docker Engine from Docker's official repository, check whether unofficial/conflicting packages are installed.

Docker's current Ubuntu documentation lists packages such as:

```text
docker.io
docker-compose
docker-compose-v2
docker-doc
docker-buildx
podman-docker
containerd
runc
```

as packages that may need to be removed to avoid conflicts, depending on the host. citeturn0search0

Example:

```bash
sudo apt remove docker.io docker-compose docker-compose-v2 \
  docker-doc docker-buildx podman-docker containerd runc
```

> ⚠️ Review what will be removed before executing package-removal commands on an existing server.

---

# 🔥 17. Docker Desktop vs Docker Engine

| Feature | Docker Desktop | Docker Engine |
|---|---|---|
| Target | Developer desktops | Linux servers/hosts |
| Windows | ✅ | Not the normal Windows 10/11 path |
| macOS | ✅ | Docker Desktop is typical |
| Linux | ✅ | ✅ |
| GUI | ✅ | ❌ CLI-focused |
| WSL 2 integration | ✅ Windows | N/A |
| Docker CLI | ✅ | ✅ |
| Compose | ✅ | Plugin available |
| Great for students | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Great for cloud Linux VM | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

### Simple rule

```text
Laptop
  ↓
Docker Desktop

Linux Cloud Server
  ↓
Docker Engine
```

---

# 🏗️ 18. What Gets Installed?

Conceptually:

```text
                 Docker Setup
                     │
       ┌─────────────┼─────────────┐
       ▼             ▼             ▼
   Docker CLI      Engine       Compose
       │             │             │
       ▼             ▼             ▼
   Commands      Containers    Multi-container
                 Images         applications
                 Networks
                 Volumes
```

On Docker Desktop, several Docker capabilities are packaged together. Docker's current Desktop documentation lists Docker Engine, Docker CLI, Docker Build, Docker Compose and other tooling among the components/features available through Desktop. citeturn0search8

---

# 🧪 19. Verification Checklist

Run:

```bash
docker version
```

Then:

```bash
docker info
```

Then:

```bash
docker images
```

Then:

```bash
docker ps
```

Then:

```bash
docker ps -a
```

Finally:

```bash
docker run hello-world
```

Expected learning path:

```text
Installation
    ↓
CLI works
    ↓
Engine works
    ↓
Image works
    ↓
Container works
    ↓
🎉 Docker is ready!
```

---

# 🔧 20. Troubleshooting

## ❌ `docker: command not found`

Possible causes:

- Docker is not installed
- Docker CLI is not in PATH
- Terminal needs to be reopened

Check:

```bash
docker --version
```

On Linux:

```bash
which docker
```

---

## ❌ Cannot connect to Docker daemon

Example concept:

```text
Cannot connect to the Docker daemon
```

### Windows/macOS

Make sure Docker Desktop is running.

### Linux

Check:

```bash
sudo systemctl status docker
```

Start:

```bash
sudo systemctl start docker
```

---

# ❌ 21. Permission Denied on Linux

Example:

```text
permission denied while trying to connect to the Docker daemon socket
```

Check:

```bash
sudo docker ps
```

If that works, review Docker group configuration:

```bash
groups
```

Then:

```bash
sudo usermod -aG docker $USER
```

Log out/in or run:

```bash
newgrp docker
```

See Docker's post-installation documentation for the supported procedure. citeturn0search3

---

# ❌ 22. WSL Problems on Windows

Check:

```powershell
wsl --version
```

Check distributions:

```powershell
wsl --list --verbose
```

Update:

```powershell
wsl --update
```

Restart WSL:

```powershell
wsl --shutdown
```

Then restart Docker Desktop.

Docker recommends keeping WSL current because older WSL versions can cause Docker Desktop issues. citeturn0search5

---

# ⚠️ 23. Firewall & Port Publishing — Important

When using Docker on Linux, understand your firewall design.

Docker's Ubuntu installation documentation warns that published container ports can bypass some firewall configurations, and recommends understanding Docker's packet-filtering behavior before exposing services. citeturn0search0

Never blindly expose:

```bash
-p 0.0.0.0:5432:5432
```

for a database on an internet-facing server.

Prefer controlled network access and appropriate security-group/firewall rules.

---

# 🔐 24. Installation Security Checklist

Before using Docker on production:

```text
☑ Use official Docker installation documentation
☑ Keep Docker updated
☑ Keep WSL updated on Windows
☑ Enable hardware virtualization where required
☑ Do not expose unnecessary ports
☑ Understand Docker daemon privileges
☑ Protect Docker socket access
☑ Do not publish secrets
☑ Review firewall configuration
☑ Use trusted images
☑ Scan images
```

---

# 🧪 25. Hands-On Lab — Complete Installation

## Lab Objective

Install Docker and prove that your environment is working.

### Windows students

```text
Windows
   ↓
WSL 2
   ↓
Docker Desktop
   ↓
Docker Engine
   ↓
hello-world
   ↓
Nginx
```

### Linux students

```text
Ubuntu
   ↓
Docker Repository
   ↓
Docker Engine
   ↓
systemctl
   ↓
hello-world
   ↓
Nginx
```

---

## 🏆 Lab Tasks

### Task 1

Verify Docker:

```bash
docker version
```

### Task 2

Verify server information:

```bash
docker info
```

### Task 3

Run:

```bash
docker run hello-world
```

### Task 4

Run Nginx:

```bash
docker run -d --name training-nginx -p 8080:80 nginx
```

### Task 5

Check:

```bash
docker ps
```

### Task 6

Open:

```text
http://localhost:8080
```

### Task 7

Check logs:

```bash
docker logs training-nginx
```

### Task 8

Stop:

```bash
docker stop training-nginx
```

### Task 9

Start:

```bash
docker start training-nginx
```

### Task 10

Remove:

```bash
docker rm -f training-nginx
```

---

# 🎯 26. Student Challenge

Without looking at the previous section:

### Challenge

Install Docker and produce evidence for:

```text
1. Docker version
2. Docker info
3. hello-world
4. Running Nginx
5. Port mapping
6. Container logs
7. Stop container
8. Start container
9. Remove container
```

Create:

```text
screenshots/
README.md
```

and document your installation.

---

# 🎓 27. Interview Questions

### Beginner

1. How do you install Docker on Windows?
2. What is Docker Desktop?
3. What is Docker Engine?
4. What is WSL 2?
5. Why is WSL 2 commonly used with Docker Desktop on Windows?
6. How do you verify Docker installation?
7. What does `docker info` show?
8. What does `docker run hello-world` prove?
9. How do you check whether Docker is running on Ubuntu?
10. How do you start Docker using systemd?

### Intermediate

11. Docker Desktop vs Docker Engine?
12. Why is hardware virtualization required for Docker Desktop on Windows?
13. How do you troubleshoot a Docker daemon connection error?
14. How do you run Docker without `sudo` on Linux?
15. What security implications does membership in the `docker` group have?
16. What is the difference between Docker CLI and Docker daemon?
17. What happens when `docker run` needs an image that isn't local?
18. How do you troubleshoot WSL/Docker Desktop issues?
19. Why should you understand firewall behavior before publishing container ports?
20. How would you install Docker on an AWS Ubuntu EC2 instance?

---

# 🧠 28. Knowledge Check

Before moving to **03-Docker-CLI**, you should be able to:

- [ ] Explain Docker Desktop
- [ ] Explain Docker Engine
- [ ] Explain WSL 2
- [ ] Install Docker Desktop on Windows
- [ ] Install Docker Engine on Ubuntu
- [ ] Verify Docker with `docker version`
- [ ] Inspect Docker with `docker info`
- [ ] Run `hello-world`
- [ ] Run Nginx
- [ ] Publish a port
- [ ] Check container status
- [ ] Check container logs
- [ ] Start/stop/remove a container
- [ ] Troubleshoot daemon connection problems
- [ ] Explain Linux Docker group permissions

---

# 🔗 29. Official Documentation

| Resource | Link |
|---|---|
| 🐳 Docker Documentation | [docs.docker.com](https://docs.docker.com/) |
| 🖥️ Docker Desktop | [Docker Desktop](https://docs.docker.com/desktop/) |
| 🪟 Windows Installation | [Install Docker Desktop on Windows](https://docs.docker.com/desktop/setup/install/windows-install/) |
| 🐧 Docker Engine | [Install Docker Engine](https://docs.docker.com/engine/install/) |
| 🟠 Ubuntu Engine | [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/) |
| 🐧 WSL 2 | [Docker Desktop + WSL 2](https://docs.docker.com/desktop/features/wsl/) |
| 👤 Linux Post-install | [Docker post-installation](https://docs.docker.com/engine/install/linux-postinstall/) |

---

# 🗺️ 30. What's Next?

Installation is complete.

Now we move from:

```text
🛠️ INSTALL
   ↓
💻 COMMANDS
```

## 👉 [03 — Docker CLI](../03-Docker-CLI/)

You will learn:

```text
docker version
docker info
docker pull
docker images
docker run
docker ps
docker stop
docker start
docker restart
docker rm
docker exec
docker logs
docker inspect
docker stats
docker system
```

---

<div align="center">

# 🚀 Docker Is Ready!

### VishwaTech Labs

**Docker • DevOps • Cloud • GitHub Actions • Terraform • Ansible • Kubernetes • Security**

### By Vishwanath Gowda H

⭐ Learn → Practice → Build → Automate → Deploy

</div>
