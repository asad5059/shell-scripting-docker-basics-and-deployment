# 🐳 Docker: A Beginner's Guide to Containerization
---

## Table of Contents

1. [What is Docker?](#1-what-is-docker)
2. [What Problem Does Docker Solve?](#2-what-problem-does-docker-solve)
3. [VM vs Docker](#3-vm-vs-docker)
4. [How Docker Runs on Windows and Mac](#4-how-docker-runs-on-windows-and-mac)
5. [Docker Images vs Docker Containers](#5-docker-images-vs-docker-containers)
6. [Docker Registry and Docker Hub](#6-docker-registry-and-docker-hub)
7. [Main Docker Commands](#7-main-docker-commands)
8. [Port Binding](#8-port-binding)
9. [Starting and Stopping Containers](#9-starting-and-stopping-containers)
10. [Private Docker Registries](#10-private-docker-registries)
11. [Registry vs Repository](#11-registry-vs-repository)
12. [Running Multiple Containers from the Same Image](#12-running-multiple-containers-from-the-same-image)
13. [Dockerizing a Sample Django App](#13-dockerizing-a-sample-django-app)
14. [Debugging with Docker](#14-debugging-with-docker)
15. [Conclusion](#15-conclusion)

---

## 1. What is Docker?

Docker is an open-source platform that allows us to **package, ship, and run applications inside containers**. Think of a container as a lightweight, portable box that holds everything our application needs to run — the code, the runtime, libraries, and configuration — all bundled together.

Docker was first released in 2013 and has since become one of the most widely adopted tools in modern software development. It lets us say goodbye to the dreaded *"but it works on my machine!"* problem.

```
┌──────────────────────────────────────┐
│             Docker Container         │
│                                      │
│   App Code + Runtime + Libraries     │
│   + Config + Dependencies            │
│                                      │
└──────────────────────────────────────┘
```

In short, Docker makes our applications **portable, consistent, and easy to deploy** across any environment — whether it's a developer's laptop, a test server, or a production cloud environment.

---

## 2. What Problem Does Docker Solve?

Before Docker, deploying an application was painful. Let's walk through the classic problems we all faced:

### The "Works on My Machine" Problem

Imagine we build an application on our laptop running Python 3.10. We hand it off to a teammate running Python 3.8 — suddenly things break. Or we deploy to a production server with a different OS and different library versions — chaos.

### Dependency Hell

Applications often depend on specific versions of libraries, runtimes, and tools. Managing these across different machines and environments was time-consuming and error-prone.

### Environment Inconsistency

Development, staging, and production environments were never truly identical. This made debugging painful — a bug in production might not reproduce in development.

### What Docker Does for Us

Docker solves all of these problems by packaging the application **together with its entire environment** into a container. The container behaves identically regardless of where it runs.

| Problem | Docker's Solution |
|---|---|
| "Works on my machine" | Same container runs everywhere |
| Dependency conflicts | Each container has its own isolated dependencies |
| Environment inconsistency | One image = same environment always |
| Slow environment setup | Spin up a container in seconds |

---

## 3. VM vs Docker

We often hear the comparison between Virtual Machines (VMs) and Docker containers. Let's break it down clearly.

### Virtual Machines (VMs)

A VM runs a **full operating system** on top of a **hypervisor** (like VMware or VirtualBox). Each VM includes the entire OS, its own kernel, and the application.

```
┌──────────────────────────────────────────────┐
│                  Hardware                    │
├──────────────────────────────────────────────┤
│               Host OS (Kernel)               │
├──────────────────────────────────────────────┤
│                 Hypervisor                   │
├───────────────────┬──────────────────────────┤
│   VM 1            │   VM 2                   │
│ ┌─────────────┐   │ ┌─────────────┐          │
│ │  Guest OS   │   │ │  Guest OS   │          │
│ │    App      │   │ │    App      │          │
│ └─────────────┘   │ └─────────────┘          │
└───────────────────┴──────────────────────────┘
```

### Docker Containers

Docker containers share the **host OS kernel**. They don't need a full OS inside each container — just the application and its dependencies.

```
┌──────────────────────────────────────────────┐
│                  Hardware                    │
├──────────────────────────────────────────────┤
│               Host OS (Kernel)               │
├──────────────────────────────────────────────┤
│               Docker Engine                  │
├───────────────┬──────────────────────────────┤
│  Container 1  │  Container 2  │  Container 3 │
│  ┌─────────┐  │  ┌─────────┐  │  ┌────────┐  │
│  │  App A  │  │  │  App B  │  │  │  App C │  │
│  └─────────┘  │  └─────────┘  │  └────────┘  │
└───────────────┴──────────────────────────────┘
```

### Side-by-Side Comparison

| Feature | Virtual Machine | Docker Container |
|---|---|---|
| Boot Time | Minutes | Seconds |
| Size | GBs | MBs |
| OS | Full Guest OS | Shared Host OS |
| Isolation | Strong (hypervisor) | Process-level |
| Performance | Slower | Near-native |
| Portability | Heavy | Lightweight |
| Use Case | Full OS isolation | App isolation |

**The bottom line:** VMs are great when we need full OS-level isolation. Docker is perfect when we want fast, lightweight, and consistent application deployment.

---

## 4. How Docker Runs on Windows and Mac

Here's an interesting question — Docker containers are Linux-based. So how do they run on Windows or macOS?

### The Short Answer: A Linux VM Under the Hood

When we install **Docker Desktop** on Windows or Mac, Docker automatically spins up a **lightweight Linux virtual machine** in the background. Our containers actually run inside this Linux VM — not directly on Windows or macOS.

```
Windows / macOS
└── Docker Desktop
    └── Lightweight Linux VM (e.g., WSL2 on Windows, HyperKit/Apple HV on Mac)
        └── Docker Engine (Linux)
            └── Our Containers
```

### On Windows

Docker Desktop uses **WSL 2 (Windows Subsystem for Linux 2)** — a full Linux kernel built right into Windows. This gives near-native Linux performance. We can also use the Hyper-V backend as an alternative.

### On macOS

Docker Desktop uses Apple's **Hypervisor framework** (and previously HyperKit) to run a lightweight Linux VM. On Apple Silicon (M1/M2/M3 chips), Docker uses a similar approach optimized for ARM architecture.

### Docker Desktop

Docker Desktop is the all-in-one package that gives us:

- The Docker Engine (the core runtime)
- The Docker CLI (command line interface)
- Docker Compose (for multi-container apps)
- A GUI Dashboard to manage containers and images visually

We simply download Docker Desktop, install it, and we're ready to go — the Linux VM setup happens automatically behind the scenes.

---

## 5. Docker Images vs Docker Containers

This is one of the most important concepts in Docker, and it's easy to confuse the two. Let's nail it down.

### Docker Image

A Docker **image** is a **read-only template** — a blueprint for creating containers. It contains the application code, runtime, libraries, environment variables, and configuration files.

Think of an image like a **class** in object-oriented programming, or like a **recipe** in cooking.

- Images are built using a `Dockerfile`
- Images are stored in registries (like Docker Hub)
- Images are **immutable** — once built, they don't change

```
Dockerfile  →  docker build  →  Docker Image
```

### Docker Container

A Docker **container** is a **running instance** of an image. It's the live, running environment where our application actually executes.

Think of a container like an **object** created from a class, or like the **actual dish** prepared from a recipe.

- We can run multiple containers from the same image
- Containers are **ephemeral** by default — they can be stopped and removed
- Each container gets its own isolated filesystem, network, and process space

```
Docker Image  →  docker run  →  Docker Container (running)
```

### The Analogy

```
Image   ≈  A Recipe card (passive, reusable blueprint)
Container ≈  The actual meal being cooked right now (active instance)
```

| Feature | Image | Container |
|---|---|---|
| State | Static / Read-only | Dynamic / Running |
| Created by | `docker build` | `docker run` |
| Stored in | Registry or locally | Locally on the host |
| Multiple instances | One image → many containers | Each container is independent |

---

## 6. Docker Registry and Docker Hub

### What is a Docker Registry?

A Docker **registry** is a storage and distribution system for Docker images. It's essentially a server where we push our images and from where we (or others) can pull them.

### Docker Hub

**Docker Hub** (`hub.docker.com`) is the default public registry provided by Docker. It's like GitHub, but for Docker images.

With Docker Hub we can:

- Pull **official images** — like `python`, `nginx`, `postgres`, `node` — maintained by their respective teams
- Push our own images for sharing or deployment
- Use **public repositories** for free
- Use **private repositories** (with limitations on the free tier)

### Pulling an Image from Docker Hub

```bash
# Pull the official Python image
docker pull python:3.11

# Pull the official Nginx web server
docker pull nginx
```

When we run `docker pull python:3.11`, Docker reaches out to Docker Hub, finds the `python` image with the tag `3.11`, and downloads it to our machine.

### Image Tags

Tags let us specify **which version** of an image we want.

```bash
docker pull nginx:latest       # Latest version
docker pull nginx:1.25         # Specific version
docker pull python:3.11-slim   # Slim variant (smaller size)
```

Using `latest` is convenient but can lead to surprises if the image updates. In production, we should always pin to a specific version tag.

---

## 7. Main Docker Commands

Let's walk through the essential Docker commands we'll use every day. We're keeping this beginner-friendly — no advanced orchestration, just the core tools.

### Pulling Images

```bash
# Pull an image from Docker Hub
docker pull <image-name>:<tag>

# Example
docker pull ubuntu:22.04
```

### Running Containers

```bash
# Run a container from an image
docker run <image-name>

# Run in detached (background) mode
docker run -d nginx

# Give the container a name
docker run -d --name my-nginx nginx
```

### Listing Containers

```bash
# List running containers
docker ps

# List ALL containers (including stopped)
docker ps -a
```

### Listing Images

```bash
# List all locally stored images
docker images
```

### Stopping and Removing

```bash
# Stop a running container
docker stop <container-name-or-id>

# Remove a stopped container
docker rm <container-name-or-id>

# Remove a Docker image
docker rmi <image-name>:<tag>
```

### Logs and Exec

```bash
# View container logs
docker logs <container-name>

# Follow logs in real-time
docker logs -f <container-name>

# Execute a command inside a running container
docker exec -it <container-name> bash
```

### Building an Image

```bash
# Build an image from a Dockerfile in the current directory
docker build -t my-app:1.0 .
```

### Quick Reference Table

| Command | What it Does |
|---|---|
| `docker pull <image>` | Download image from registry |
| `docker images` | List local images |
| `docker run <image>` | Create and start a container |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker stop <name>` | Stop a running container |
| `docker rm <name>` | Remove a stopped container |
| `docker rmi <image>` | Remove a local image |
| `docker logs <name>` | View container output |
| `docker exec -it <name> bash` | Open a shell in a container |
| `docker build -t <name> .` | Build image from Dockerfile |

---

## 8. Port Binding

By default, containers are **isolated** from the outside world — including from our own machine. So if we run a web server inside a container, we can't reach it from our browser... unless we do **port binding**.

### The Concept

Port binding (also called port mapping or port publishing) creates a link between a **port on our host machine** and a **port inside the container**.

```
Our Browser → localhost:8080 → [Host Port 8080] → [Container Port 80] → Nginx
```

### How to Bind Ports

We use the `-p` flag with `docker run`:

```bash
docker run -p <host-port>:<container-port> <image>
```

### Examples

```bash
# Map host port 8080 to container port 80 (Nginx)
docker run -d -p 8080:80 nginx

# Now we can visit http://localhost:8080 in our browser!

# Map host port 5432 to container port 5432 (PostgreSQL)
docker run -d -p 5432:5432 --name my-postgres postgres

# Map host port 8000 to container port 8000 (Django)
docker run -d -p 8000:8000 my-django-app
```

### Multiple Port Bindings

We can bind multiple ports at once:

```bash
docker run -d -p 8080:80 -p 8443:443 nginx
```

### Important Notes

- The **left side** of the colon is always the host port (our machine)
- The **right side** is the container port (inside Docker)
- Two containers **cannot** use the same host port simultaneously
- Container ports are defined by the application (e.g., Nginx uses 80, Django uses 8000)

---

## 9. Starting and Stopping Containers

Let's understand the full lifecycle of a container.

### Creating vs Running

```bash
# docker run = creates a NEW container AND starts it
docker run -d --name my-app nginx

# docker start = starts an EXISTING (stopped) container
docker start my-app

# docker stop = gracefully stops a running container
docker stop my-app

# docker restart = stops then starts again
docker restart my-app
```

### The Container Lifecycle

```
docker run
    │
    ▼
 RUNNING ──── docker stop ──── STOPPED
    │                              │
    │                         docker start
    │                              │
    └──────────────────────────────┘
    
docker rm  →  DELETED (permanently removed)
```

### Stopping Containers

```bash
# Gracefully stop (sends SIGTERM, waits 10 seconds, then SIGKILL)
docker stop my-app

# Force stop immediately (sends SIGKILL)
docker kill my-app
```

We should always prefer `docker stop` over `docker kill` — it gives the application time to clean up (close database connections, finish writing files, etc.).

### Removing Containers

```bash
# Remove a single stopped container
docker rm my-app

# Remove ALL stopped containers at once
docker container prune

# Stop and remove in one command
docker rm -f my-app
```

### Auto-Remove on Exit

If we want a container to delete itself when it stops (great for one-off tasks):

```bash
docker run --rm ubuntu echo "Hello, Docker!"
# Container runs, prints message, then deletes itself
```

### Restart Policies

We can tell Docker to automatically restart containers:

```bash
# Always restart (even after Docker daemon restarts)
docker run -d --restart always nginx

# Restart only on failure
docker run -d --restart on-failure my-app

# Don't restart (default)
docker run -d --restart no my-app
```

---

## 10. Private Docker Registries

Docker Hub is public by default. But in real-world projects, we don't want our application images available for the whole world to see. That's where **private registries** come in.

### What is a Private Registry?

A private registry is a Docker image registry that requires **authentication** to access. Only authorized users or systems can push or pull images.

### Why Use a Private Registry?

- **Security** — Our proprietary code stays private
- **Control** — We decide who can access what
- **Speed** — Hosting images closer to our servers reduces deployment times
- **Compliance** — Some industries (finance, healthcare) require images to stay within specific infrastructure

### Options for Private Registries

| Provider | Description |
|---|---|
| **Docker Hub** (private repos) | Simple, easy — limited free private repos |
| **Amazon ECR** | AWS Elastic Container Registry |
| **Google Artifact Registry** | Google Cloud's container registry |
| **Azure Container Registry** | Microsoft Azure's solution |
| **GitHub Container Registry** | Integrated with GitHub Actions |
| **Self-hosted (Registry image)** | Run our own registry on a server |

### Authenticating and Using a Private Registry

```bash
# Login to Docker Hub (or any registry)
docker login

# Login to a private registry (e.g., AWS ECR)
docker login <registry-url>

# Push an image to a private registry
docker tag my-app:1.0 mycompany.azurecr.io/my-app:1.0
docker push mycompany.azurecr.io/my-app:1.0

# Pull from a private registry
docker pull mycompany.azurecr.io/my-app:1.0
```

### Running a Local Private Registry

Docker even offers a simple registry image we can host ourselves:

```bash
# Run a local registry on port 5000
docker run -d -p 5000:5000 --name local-registry registry:2

# Tag and push to our local registry
docker tag my-app:1.0 localhost:5000/my-app:1.0
docker push localhost:5000/my-app:1.0
```

---

## 11. Registry vs Repository

These two terms are closely related but mean different things. Let's clear up the confusion.

### Docker Registry

A **registry** is the **server** (or service) that hosts and distributes Docker images. It's the top-level system.

**Examples:** Docker Hub, Amazon ECR, GitHub Container Registry

Think of a registry like a **warehouse** that stores many items.

### Docker Repository

A **repository** is a **collection of related Docker images** within a registry — typically different versions of the same application, differentiated by tags.

Think of a repository like a **shelf in that warehouse** dedicated to a single product.

### The Hierarchy

```
Registry (Docker Hub)
│
├── Repository: python
│   ├── python:3.11
│   ├── python:3.10
│   └── python:3.9-slim
│
├── Repository: nginx
│   ├── nginx:latest
│   ├── nginx:1.25
│   └── nginx:alpine
│
└── Repository: mycompany/my-app
    ├── mycompany/my-app:1.0
    ├── mycompany/my-app:1.1
    └── mycompany/my-app:2.0
```

### Summary

| Term | What it Is | Analogy |
|---|---|---|
| **Registry** | The server hosting all images | A library building |
| **Repository** | A group of images for one app | A bookshelf for one series |
| **Image** | A specific version of an app | A specific book edition |
| **Tag** | The version label on an image | The edition/year of the book |

---

## 12. Running Multiple Containers from the Same Image

One of Docker's most powerful features is the ability to **run multiple containers from the same image**, each behaving as a completely independent environment. Let's make this concrete with a hands-on Nginx example.

### The Goal

We will spin up two Nginx containers from the same `nginx` image — but different version tags — each serving different content on different host ports:

- `nginx:latest` → accessible at `http://localhost:8080`
- `nginx:1.23` → accessible at `http://localhost:9000`

### Step 1: Run Two Containers on Different Ports

```bash
# Run the latest version
docker run -d -p 8080:80 --name nginx_latest nginx:latest

# Run a specific older version
docker run -d -p 9000:80 --name nginx_123 nginx:1.23
```

Now verify both are running:

```bash
docker ps
```

We should see something like this:

```
NAMES          PORTS
nginx_latest   0.0.0.0:8080->80/tcp
nginx_123      0.0.0.0:9000->80/tcp
```

Two containers, same image, zero conflict.

### Step 2: Customize Each Container's Content

Let's modify the default `index.html` inside each container so we can prove they're truly independent.

**Modify `nginx_latest`:**

```bash
docker exec -it nginx_latest sh
cd /usr/share/nginx/html
echo "<h1>NGINX LATEST VERSION</h1>" > index.html
exit
```

**Modify `nginx_123`:**

```bash
docker exec -it nginx_123 sh
cd /usr/share/nginx/html
echo "<h1>NGINX 1.23 VERSION</h1>" > index.html
exit
```

### Step 3: Verify in the Browser

Open both URLs and we'll see different content from each container:

- `http://localhost:8080` → **NGINX LATEST VERSION**
- `http://localhost:9000` → **NGINX 1.23 VERSION**

Same image. Same internal port. Completely different containers.

### How Is This Possible? The Isolation Model

At first glance this might seem puzzling — both containers run Nginx internally on port 80, so why is there no conflict? The answer is Docker's **network namespace isolation**.

Each container gets its own:

- Isolated filesystem
- Isolated network namespace
- Its own internal IP address

So internally, both containers listen on port 80 — but they live in completely separate network spaces. The magic happens at the host level, where Docker maps them to different host ports:

```
┌─────────────────────────────────────────────────────┐
│                    Our Machine                       │
│                                                      │
│   localhost:8080 ──────────────► nginx_latest:80     │
│                                                      │
│   localhost:9000 ──────────────► nginx_123:80        │
│                                                      │
│   (Host ports must be unique — container ports       │
│    can be identical across different containers)     │
└─────────────────────────────────────────────────────┘
```

And here's the full picture side by side:

| Container | Internal Port | Host Port | URL |
|---|---|---|---|
| `nginx_latest` | 80 | 8080 | http://localhost:8080 |
| `nginx_123` | 80 | 9000 | http://localhost:9000 |

The rule to remember: **container ports can repeat across containers — host ports cannot**.

### An Important Note on Container Edits

We edited `index.html` directly inside a running container in this example. This is fine for learning and experimentation, but there's a catch — **those changes are lost the moment the container is removed**.

```bash
docker rm -f nginx_latest
# The custom index.html we wrote? Gone.
```

In real-world applications, we should instead:

- Use **Docker volumes** to mount files from our host into the container (changes persist)
- Or bake the content directly into a **custom image** via a `Dockerfile`

### Key Takeaways

- We can run as many containers as we like from a single image
- Each container is a fully isolated environment — its own filesystem, network, and process space
- The same internal port (e.g., `80`) can be used by multiple containers simultaneously
- Only **host ports** need to be unique on our machine
- In-container edits are temporary and disappear when the container is removed

---

## 13. Dockerizing a Sample Django App

Now let's put everything together and **Dockerize a real Django application** from scratch. This is where theory meets practice.

### Our Project Structure

```
my-django-app/
├── myproject/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── myapp/
│   ├── __init__.py
│   ├── models.py
│   └── views.py
├── manage.py
├── requirements.txt
└── Dockerfile          ← We'll create this
```

### Step 1: Create `requirements.txt`

```txt
Django==4.2.7
gunicorn==21.2.0
```

### Step 2: Write the `Dockerfile`

A `Dockerfile` is a text file with step-by-step instructions for building our Docker image. Every line is a command Docker executes to assemble the image.

```dockerfile
# Step 1: Choose a base image
FROM python:3.11-slim

# Step 2: Set the working directory inside the container
WORKDIR /app

# Step 3: Copy the requirements file first (for better caching)
COPY requirements.txt .

# Step 4: Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Step 5: Copy the rest of our application code
COPY . .

# Step 6: Expose the port our app will run on
EXPOSE 8000

# Step 7: The command to run when the container starts
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "myproject.wsgi:application"]
```

### Breaking Down the Dockerfile

| Instruction | What It Does |
|---|---|
| `FROM` | Sets the base image we build on top of |
| `WORKDIR` | Sets the working directory for all following commands |
| `COPY` | Copies files from our machine into the image |
| `RUN` | Executes a command during image build |
| `EXPOSE` | Documents which port the container listens on |
| `CMD` | The default command to run when the container starts |

### Step 3: Add a `.dockerignore` File

Just like `.gitignore`, this tells Docker what **not** to copy into the image — keeping it lean and secure.

```
# .dockerignore
__pycache__/
*.pyc
*.pyo
.env
.git
.gitignore
*.sqlite3
venv/
```

### Step 4: Build the Docker Image

```bash
# Build the image and tag it
docker build -t my-django-app:1.0 .
```

We should see Docker executing each step of the Dockerfile. Once complete:

```bash
# Verify the image was created
docker images
```

### Step 5: Run the Container

```bash
docker run -d \
  --name django-app \
  -p 8000:8000 \
  my-django-app:1.0
```

Now we can visit `http://localhost:8000` in our browser to see our Django app running inside Docker!

### Step 6: Check It's Working

```bash
# Check it's running
docker ps

# View the logs
docker logs django-app

# Follow logs in real-time
docker logs -f django-app
```

### Step 7: Push to Docker Hub (Optional)

If we want to share this image or deploy it to a server:

```bash
# Login to Docker Hub
docker login

# Tag with our Docker Hub username
docker tag my-django-app:1.0 yourusername/my-django-app:1.0

# Push to Docker Hub
docker push yourusername/my-django-app:1.0
```

Now anyone (or any server) can pull and run our app with:

```bash
docker pull yourusername/my-django-app:1.0
docker run -d -p 8000:8000 yourusername/my-django-app:1.0
```

### A Note on Environment Variables

In a real application, we'd pass sensitive configuration (like database URLs and secret keys) via environment variables — never baking them into the image:

```bash
docker run -d \
  -p 8000:8000 \
  -e SECRET_KEY="your-secret-key" \
  -e DATABASE_URL="postgres://user:pass@host/db" \
  my-django-app:1.0
```

---


## 14. Debugging with Docker
 
Things will break. That's just part of development. The good news is Docker gives us a solid set of commands to figure out exactly what's going wrong and where. Here's our go-to debugging toolkit.
 
---
 
### 1. Viewing Logs
 
The first thing we should always check when something goes wrong.
 
```bash
# View logs of a container
docker logs <container-name>
 
# Follow logs in real-time
docker logs -f <container-name>
 
# Show only the last 50 lines
docker logs --tail 50 <container-name>
 
# Show logs with timestamps
docker logs -t <container-name>
 
# Combine all three — live, timestamped, last 50 lines
docker logs -f -t --tail 50 <container-name>
```
 
---
 
### 2. Getting a Shell Inside a Running Container
 
When logs aren't enough and we need to look around inside the container ourselves.
 
```bash
# Open an interactive bash shell
docker exec -it <container-name> bash
 
# If bash isn't available (slim or alpine images), use sh
docker exec -it <container-name> sh
```
 
Once inside, we can check things like:
 
```bash
env           # are our environment variables set correctly?
ls /app       # are our files actually there?
ping postgres # can we reach another container?
cat /etc/nginx/nginx.conf  # what does the config actually say?
```
 
---
 
### 3. Inspecting Containers
 
Useful when we want to verify how a container was actually configured — ports, volumes, environment variables, network settings.
 
```bash
# Full container metadata as JSON
docker inspect <container-name>
 
# Check just the exit code (0 = clean, anything else = error)
docker inspect <container-name> --format='{{.State.ExitCode}}'
 
# Check environment variables
docker inspect <container-name> --format='{{.Config.Env}}'
 
# Check network settings
docker inspect <container-name> --format='{{json .NetworkSettings}}'
```
 
---
 
### 4. Monitoring Resource Usage
 
When a container isn't crashing but feels slow or unresponsive, it might be consuming too many resources.
 
```bash
# Live resource usage for all running containers
docker stats
 
# Resource usage for a specific container
docker stats <container-name>
```
 
This shows CPU %, memory, network I/O, and disk I/O in real time — like `top`, but for Docker containers.
 
---
 
### 5. Running One-Off Commands
 
We don't always need a full shell. Sometimes we just want to run a single command and see what it returns.
 
```bash
# Run Django's system checks
docker exec django-app python manage.py check
 
# See which migrations have and haven't run
docker exec django-app python manage.py showmigrations
 
# Open the Django interactive shell inside the container
docker exec -it django-app python manage.py shell
 
# Print all environment variables
docker exec django-app env
```

 
---

## 15. Conclusion

We've covered a lot of ground together! Let's recap what we've learned on this journey:

- **Docker** lets us package applications with all their dependencies into portable, consistent containers
- It solves real problems like environment inconsistency, dependency conflicts, and slow onboarding
- **Containers are lighter and faster than VMs** because they share the host OS kernel
- On Windows and Mac, Docker Desktop runs a hidden Linux VM under the hood so our containers always have a Linux environment
- **Images are blueprints; containers are running instances** — like a class and its objects
- **Docker Hub** is the default public registry where we find and share images
- Core commands like `docker run`, `docker ps`, `docker stop`, and `docker build` are enough to get us started
- **Port binding** (`-p host:container`) is how we expose container services to our local machine
- **Private registries** keep our proprietary images secure and within our infrastructure
- A **registry** is the warehouse; a **repository** is the shelf for one application
- We can **Dockerize any app** — including Django — with a well-written `Dockerfile`
- **Multiple containers** can run from the same image simultaneously, each fully isolated with its own network and filesystem

### What's Next?

Once we're comfortable with the basics, the natural next steps are:

- **Docker Compose** — orchestrate multi-container apps (app + database + cache) with a single `docker-compose.yml` file
- **Docker Volumes** — persist data beyond the container lifecycle
- **Docker Networks** — let containers communicate with each other
- **Kubernetes** — orchestrate containers at scale in production

Docker has fundamentally changed how we build and ship software. Once we start using it, it's hard to imagine going back. We hope this guide gives us all the confidence to start containerizing our own projects!

---
