# Docker Compose Demo Video Commands

> Project: `compose-app`
> Services: Flask Backend + MySQL + Redis

---

## Step 1 - Go To Project Folder

```bash
cd compose-app
```

---

## Step 2 - Check Project Files

```bash
ls
```

Expected files:

```text
backend
db
docker-compose.yml
.env
README.md
```

---

## Step 3 - Show Project Structure

If `tree` is installed:

```bash
tree
```

Expected structure:

```text
├── README.md
├── backend
│   ├── Dockerfile
│   ├── app.py
│   └── requirements.txt
├── db
│   └── init
│       └── 01-init.sql
└── docker-compose.yml
```

---

## Step 4 - Show Environment File

```bash
cat .env
```

This file is used by Docker Compose.

Important values:

```text
APP_NAME=Learn DevOps Now
MYSQL_HOST=mysql
MYSQL_PORT=3306
MYSQL_DATABASE=compose_demo
MYSQL_USER=appuser
MYSQL_PASSWORD=apppassword
REDIS_HOST=redis
REDIS_PORT=6379
```

## Step 5 - Validate Docker Compose Config

```bash
docker compose config
```

This command checks the final compose configuration after reading `.env`.

---

## Step 6 - Start All Services With Build

```bash
docker compose up -d --build
```

This will build the backend image and start:

```text
compose-backend
compose-mysql
compose-redis
```

---

## Step 7 - Check Running Services

```bash
docker compose ps
```

or:

```bash
docker ps
```

---

## Step 8 - Check Backend Logs

```bash
docker compose logs backend
```

For live logs:

```bash
docker compose logs -f backend
```

Press `CTRL + C` to exit live logs.

---

## Step 9 - Check MySQL Logs

```bash
docker compose logs mysql
```

---

## Step 10 - Check Redis Logs

```bash
docker compose logs redis
```

---

## Step 11 - Test Backend Home API

Backend is mapped from container port `5000` to local port `5001`.

```bash
curl http://localhost:5001
```

Expected response includes:

```json
{
  "message": "Hello from Flask Backend API",
  "app_name": "Learn DevOps Now"
}
```

---

## Step 12 - Test Health API

```bash
curl http://localhost:5001/health
```

Expected response includes:

```json
{
  "status": "healthy",
  "mysql": "connected",
  "redis": "connected"
}
```

---

## Step 13 - Test MySQL + Redis Integration

```bash
curl http://localhost:5001/visits
```

Run it multiple times:

```bash
curl http://localhost:5001/visits
curl http://localhost:5001/visits
curl http://localhost:5001/visits
```

This endpoint inserts rows into MySQL and increments visit count in Redis.

---

## Step 14 - Enter MySQL Container

```bash
docker compose exec -it mysql /bin/bash
```

Login to MySQL

```text
mysql -uappuser -papppassword compose_demo
```

Inside MySQL shell, run:

```sql
SHOW TABLES;
```

```sql
SELECT * FROM visits;
```

Exit MySQL:

```sql
exit;
```

## Step 15 - Inspect Docker Network

```bash
docker network ls
```

```bash
docker network inspect compose-app_compose-net
```

If the network name is different, get it with:

```bash
docker network ls | grep compose
```

---

## Step 16 - Inspect Docker Volume

```bash
docker volume ls
```

```bash
docker volume inspect compose-app_mysql_data
```

If the volume name is different, get it with:

```bash
docker volume ls | grep mysql
```

## Step 17 - Full Cleanup Including MySQL Volume

Use this only when the demo is finished.

```bash
docker compose down -v
```

---

## Step 18 - Verify Cleanup

```bash
docker ps -a
```

```bash
docker volume ls | grep mysql
```

```bash
docker network ls | grep compose
```

---

## Important Note - If MySQL Port 3306 Is Already Used On Mac

Check port:

```bash
lsof -i :3306
```

If local MySQL is using port 3306, either stop local MySQL or change this line in `.env`:

```text
MYSQL_PORT=3307
```

Then run:

```bash
docker compose up -d --build
```
