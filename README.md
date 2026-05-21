# Docker Series

This repository contains Docker learning materials and hands-on labs from the Docker series.

> This README is based only on the files included in this repository.

---

## Repository Structure

```text
├── README.md
├── day-1
│   └── day-1.md
├── day-2
│   └── day-2.md
├── day-3
│   └── day-3.md
├── day-4
│   ├── day-4.md
│   └── python-docker-app
│       ├── Dockerfile
│       ├── app.py
│       └── requirements.txt
├── day-5
│   ├── compose-app
│   │   ├── README.md
│   │   ├── backend
│   │   │   ├── Dockerfile
│   │   │   ├── app.py
│   │   │   └── requirements.txt
│   │   ├── db
│   │   │   └── init
│   │   │       └── 01-init.sql
│   │   └── docker-compose.yml
│   └── day-5.md
├── day-6
│   └── day-6.md
└── day-7
    └── day-7.md
```

---

## Day 1 — Docker Introduction

Folder:

```text
day1/
```

Current file:

```text
day1/note.md
```

> The uploaded `day1/note.md` file is currently empty.

---

## Day 2 — Docker Basic Commands

Folder:

```text
day2/
```

Main lab file:

```text
day2/lab.md
```

This lab covers basic Docker CLI usage.

### Verify Docker Installation

```bash
docker --version
docker info
```

### Run First Container

```bash
docker pull hello-world
docker run hello-world
```

### Run Containers in Different Modes

```bash
docker run -it ubuntu bash
ls
```

```bash
docker run -d -p 8080:80 nginx
docker run -d --name web-server -p 8081:80 nginx
```

```bash
docker run --rm ubuntu echo "Hello DevOps"
```

### View Containers

```bash
docker ps
docker ps -a
```

### View Logs

```bash
docker logs test-container
```

### Container Lifecycle

```bash
docker stop web-server
docker start web-server
docker restart web-server
docker rm web-server
```

### Access Running Container

```bash
docker exec -it web-server /bin/bash
cat /etc/os-release
```

### Cleanup

```bash
docker container prune
docker system prune
```

---

## Day 3 — Docker Images / Dockerfile Notes

Folder:

```text
day3/
```

Current file:

```text
day3/note.md
```

> The uploaded `day3/note.md` file is currently empty.

---

## Day 4 — Python Flask Docker App

Folder:

```text
day4/python-docker-app/
```

This lab contains a simple Python Flask application and a Dockerfile.

### Application Files

```text
python-docker-app/
├── app.py
├── Dockerfile
├── requirements.txt
└── .gitignore
```

### Dockerfile

```dockerfile
FROM python:3.11-slim
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 5000
ENV APP_NAME="Hello Docker World"
CMD ["python", "app.py"]
```

### Build Docker Image

```bash
cd day4/python-docker-app
docker build -t python-docker-app .
```

### Run Container

```bash
docker run -d -p 5000:5000 --name python-docker-app python-docker-app
```

### Test Application

```bash
curl http://localhost:5000
```

Default response:

```text
Hello Docker World
```

### Stop and Remove Container

```bash
docker stop python-docker-app
docker rm python-docker-app
```

---

## Day 5 — Docker Compose App With MySQL and Redis

Folder:

```text
day5/compose-app/
```

This lab uses Docker Compose to run:

- Flask backend
- MySQL database
- Redis cache
- MySQL init SQL script

### Compose Services

```text
backend
mysql
redis
```

### Network

```text
compose-net
```

### Volume

```text
mysql_data
```

### Environment File

```text
.env
```

Current `.env` values included in the uploaded project:

```env
APP_NAME=Learn DevOps Now

MYSQL_HOST=mysql
MYSQL_PORT=3306
MYSQL_ROOT_PASSWORD=rootpassword
MYSQL_DATABASE=compose_demo
MYSQL_USER=appuser
MYSQL_PASSWORD=apppassword

REDIS_HOST=redis
REDIS_PORT=6379
```

### Run Docker Compose App

```bash
cd day5/compose-app
docker compose up -d --build
```

### Check Running Services

```bash
docker compose ps
```

### Test Backend

Backend:

```text
http://localhost:5001
```

Health check:

```text
http://localhost:5001/health
```

MySQL + Redis test:

```text
http://localhost:5001/visits
```

### Useful Commands

```bash
docker compose logs -f backend
docker compose exec mysql mysql -uappuser -papppassword compose_demo
docker compose exec redis redis-cli
docker compose down
docker compose down -v
```

### Important Notes

- Backend connects to MySQL using service name `mysql`.
- Backend connects to Redis using service name `redis`.
- MySQL data is stored in the named volume `mysql_data`.
- Initial database table is created from:

```text
db/init/01-init.sql
```

---

## Day 6 — Advanced Docker Notes

Folder:

```text
day6/
```

Current file:

```text
day6/note.md
```

> The uploaded `day6/note.md` file is currently empty.

---

## Day 7 — Docker Swarm 3-Node Lab

Folder:

```text
day7/
```

Main lab file:

```text
day7/docker-swarm-lab.md
```

This lab creates a Docker Swarm cluster with:

```text
1 manager node
2 worker nodes
```

### Example Architecture

```text
manager-1   10.20.1.5
worker-1    10.20.1.6
worker-2    10.20.1.7
```

### Required Swarm Ports

| Port | Protocol | Purpose |
|---:|---|---|
| 2377 | TCP | Cluster management |
| 7946 | TCP/UDP | Node discovery and gossip |
| 4789 | UDP | Overlay network traffic |

### Initialize Swarm on Manager

```bash
docker swarm init --advertise-addr 10.20.1.5
```

### Join Worker Nodes

Run the generated join command on worker nodes:

```bash
docker swarm join --token SWMTKN-1-xxxxx 10.20.1.5:2377
```

### Check Nodes

```bash
docker node ls
```

### Deploy First Service

```bash
docker service create \
  --name my-web \
  --replicas 3 \
  --publish 8080:80 \
  nginx:alpine
```

### Check Service

```bash
docker service ls
docker service ps my-web
```

### Test Routing Mesh

```bash
curl http://10.20.1.5:8080
curl http://10.20.1.6:8080
curl http://10.20.1.7:8080
```

### Scale Service

```bash
docker service scale my-web=5
docker service ps my-web
docker service scale my-web=2
```

### Rolling Update

```bash
docker service update \
  --image nginx:1.25-alpine \
  --update-parallelism 1 \
  --update-delay 10s \
  my-web
```

### Rollback

```bash
docker service rollback my-web
```

### Create Overlay Network

```bash
docker network create \
  --driver overlay \
  app-net
```

### Deploy Docker Stack

```bash
docker stack deploy -c docker-compose.yml myapp
```

### Check Stack

```bash
docker stack ls
docker stack services myapp
docker stack ps myapp
```

### Scale Stack Service

```bash
docker service scale myapp_backend=5
```

### Cleanup

```bash
docker service rm my-web
docker stack rm myapp
docker network rm app-net
docker swarm leave
docker swarm leave --force
```

---

## Learning Topics Covered

```text
Docker installation check
Docker image pull
Docker container run modes
Port mapping
Container lifecycle
Container logs
Container exec
Docker cleanup
Dockerfile basics
Python Flask containerization
Docker Compose
Compose networking
Compose named volumes
MySQL container
Redis container
Docker Swarm cluster
Swarm services
Swarm replicas
Routing mesh
Rolling updates
Docker Stack
Overlay network
```

---
