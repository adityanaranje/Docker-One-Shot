# Docker in One Shot 🐳

A complete beginner-to-advanced guide to understanding Docker with Python examples.

---

# 📦 What is Docker?

Docker is a containerization platform that packages applications with all dependencies into lightweight, portable containers.

Instead of:
- “It works on my machine”

Docker gives:
- “It works everywhere”

---

# 🧩 Core Components of Docker

## 1. Docker Engine

The main runtime that builds and runs containers.

It contains:
- Docker Daemon (`dockerd`)
- REST API
- Docker CLI

---

## 2. Docker Client (CLI)

Command-line tool used to interact with Docker.

Example:
```bash
docker run hello-world
```

---

## 3. Docker Image

A read-only blueprint/template used to create containers.

Contains:
- Code
- Dependencies
- Runtime
- Environment variables

Example:
```bash
python:3.12-slim
```

Think of image as:
> Class in OOP

---

## 4. Docker Container

A running instance of an image.

Think of container as:
> Object of a class

---

## 5. Dockerfile

A script containing instructions to build Docker images.

Example:
```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

CMD ["python", "app.py"]
```

---

## 6. Docker Hub

Cloud registry for storing and sharing images.

Examples:
- nginx
- redis
- postgres
- python

Official website:
https://hub.docker.com

---

## 7. Volumes

Persistent storage for containers.

Used for:
- Databases
- Logs
- File persistence

Example:
```bash
docker volume create myvolume
```

---

## 8. Docker Network

Allows containers to communicate with each other.

Types:
- bridge
- host
- none
- overlay

---

## 9. Docker Compose

Tool for running multi-container applications.

Example:
```yaml
services:
  app:
    build: .
  
  redis:
    image: redis
```

---

# 🐍 Python Project Structure

```text
python-docker-app/
│
├── app.py
├── requirements.txt
├── Dockerfile
└── .dockerignore
```

---

# 🚀 Important Docker Commands

## 1. Check Docker Version

```bash
docker --version
```

Shows installed Docker version.

---

## 2. Pull Image

Downloads image from Docker Hub.

```bash
docker pull python:3.12
```

---

## 3. List Images

```bash
docker images
```

Shows all downloaded images.

---

## 4. Build Docker Image

```bash
docker build -t myapp .
```

### Explanation
- `build` → Build image
- `-t` → Tag/name
- `.` → Current directory

---

## 5. Run Container

```bash
docker run myapp
```

Run with port mapping:

```bash
docker run -p 5000:5000 myapp
```

### Explanation
- First `5000` → Host port
- Second `5000` → Container port

---

## 6. Run Container in Background

```bash
docker run -d myapp
```

`-d` means detached mode.

---

## 7. Name a Container

```bash
docker run --name flask-app myapp
```

---

## 8. Show Running Containers

```bash
docker ps
```

Show all containers:

```bash
docker ps -a
```

---

## 9. Stop Container

```bash
docker stop container_id
```

---

## 10. Start Container

```bash
docker start container_id
```

---

## 11. Restart Container

```bash
docker restart container_id
```

---

## 12. Remove Container

```bash
docker rm container_id
```

Force remove:

```bash
docker rm -f container_id
```

---

## 13. Remove Image

```bash
docker rmi image_id
```

---

## 14. View Logs

```bash
docker logs container_id
```

Live logs:

```bash
docker logs -f container_id
```

---

## 15. Execute Command Inside Container

```bash
docker exec -it container_id bash
```

### Explanation
- `-i` → Interactive
- `-t` → Terminal

---

## 16. Copy Files

Host → Container:

```bash
docker cp test.txt container_id:/app
```

Container → Host:

```bash
docker cp container_id:/app/test.txt .
```

---

## 17. Inspect Container

```bash
docker inspect container_id
```

Shows:
- IP
- Volumes
- Network
- Metadata

---

## 18. View Resource Usage

```bash
docker stats
```

---

## 19. Create Volume

```bash
docker volume create myvolume
```

Mount volume:

```bash
docker run -v myvolume:/data myapp
```

---

## 20. Docker Network Commands

Create network:

```bash
docker network create mynetwork
```

Run using network:

```bash
docker run --network=mynetwork myapp
```

---

## 21. Docker Compose Up

```bash
docker compose up
```

Detached:

```bash
docker compose up -d
```

---

## 22. Docker Compose Down

```bash
docker compose down
```

---

# 🐍 Python Flask Docker Example

## app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Docker in One Shot"

app.run(host="0.0.0.0", port=5000)
```

---

## requirements.txt

```text
flask
```

---

## Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

---

# ▶️ Build & Run

## Build

```bash
docker build -t flask-app .
```

## Run

```bash
docker run -p 5000:5000 flask-app
```

Open:

```text
http://localhost:5000
```

---

# ⚡ Docker Image Optimization Tips

## 1. Use Slim Images

❌ Bad

```dockerfile
FROM python:3.12
```

✅ Better

```dockerfile
FROM python:3.12-slim
```

Reduces image size significantly.

---

## 2. Use Alpine Carefully

```dockerfile
FROM python:3.12-alpine
```

Very small image size.

But:
- Some Python packages fail to compile
- Debugging harder

Use mainly for lightweight apps.

---

## 3. Use `.dockerignore`

## .dockerignore

```text
venv/
__pycache__/
.git/
.env
```

Prevents unnecessary files from entering image.

---

## 4. Layer Caching Optimization

❌ Bad

```dockerfile
COPY . .

RUN pip install -r requirements.txt
```

Every code change reinstalls dependencies.

✅ Better

```dockerfile
COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .
```

Dependencies cache properly.

---

## 5. Use `--no-cache-dir`

❌ Bad

```dockerfile
RUN pip install -r requirements.txt
```

✅ Better

```dockerfile
RUN pip install --no-cache-dir -r requirements.txt
```

Avoids storing pip cache.

---

## 6. Reduce Layers

❌ Bad

```dockerfile
RUN apt update
RUN apt install -y gcc
```

✅ Better

```dockerfile
RUN apt update && apt install -y gcc
```

---

## 7. Multi-Stage Builds

Useful for:
- AI apps
- ML apps
- Large Python projects

Example:

```dockerfile
# Builder stage
FROM python:3.12 as builder

WORKDIR /install

COPY requirements.txt .

RUN pip install --prefix=/install/deps -r requirements.txt

# Final stage
FROM python:3.12-slim

WORKDIR /app

COPY --from=builder /install/deps /usr/local

COPY . .

CMD ["python", "app.py"]
```

Benefits:
- Smaller image
- Cleaner runtime
- Faster deployments

---

## 8. Avoid Running as Root

❌ Bad

```dockerfile
USER root
```

✅ Better

```dockerfile
RUN useradd -m appuser
USER appuser
```

Improves security.

---

## 9. Use Specific Tags

❌ Bad

```dockerfile
FROM python:latest
```

✅ Better

```dockerfile
FROM python:3.12.3-slim
```

Ensures reproducible builds.

---

## 10. Minimize Installed Packages

Only install required dependencies.

❌ Bad

```dockerfile
RUN apt install -y vim curl nano gcc g++
```

✅ Better

```dockerfile
RUN apt install -y gcc
```

---

# 🧠 Best Practices

- Keep containers stateless
- One process per container
- Use environment variables
- Use health checks
- Keep images small
- Use Docker Compose for microservices
- Scan images for vulnerabilities

Official docs:
https://docs.docker.com

---

# 📚 Advanced Topics to Learn Next

- Docker Swarm
- Kubernetes
- CI/CD with Docker
- Container Security
- GPU Containers
- Docker Registry
- Docker Networking Deep Dive
- Monitoring Containers

---

# 🎯 Final Summary

Docker helps you:
- Package applications
- Ensure consistency
- Simplify deployment
- Improve scalability
- Isolate environments

For Python developers, Docker is extremely useful for:
- Flask APIs
- FastAPI apps
- AI/ML projects
- Data pipelines
- Streamlit apps
- Microservices

