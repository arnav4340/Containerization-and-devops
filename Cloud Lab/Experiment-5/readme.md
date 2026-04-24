Below is a complete `README.md` file you can copy and paste into your GitHub repository. It follows the style of your Experiment‑1 example, includes all the experiments from the PDF, and references the screenshots you provided (placed in an `Images/` folder). Just save the file as `README.md` in your `Experiment-5` folder.

```markdown
<h2 align="center">Experiment 5: Docker – Volumes, Environment Variables, Monitoring & Networks</h2>

This experiment demonstrates essential Docker features for building, configuring, and managing production‑ready containerized applications – persistent storage, environment configuration, container monitoring, and network communication.

---

## 📁 Table of Contents

- [Part 1: Docker Volumes – Persistent Data Storage](#part-1-docker-volumes--persistent-data-storage)
  - [Understanding Data Persistence](#understanding-data-persistence)
  - [Volume Types](#volume-types)
  - [Practical Volume Examples](#practical-volume-examples)
  - [Volume Management Commands](#volume-management-commands)
- [Part 2: Environment Variables](#part-2-environment-variables)
  - [Setting Environment Variables](#setting-environment-variables)
  - [Environment Variables in Applications](#environment-variables-in-applications)
- [Part 3: Docker Monitoring](#part-3-docker-monitoring)
  - [Basic Monitoring Commands](#basic-monitoring-commands)
  - [Container Inspection & Events](#container-inspection--events)
- [Part 4: Docker Networks](#part-4-docker-networks)
  - [Network Types](#network-types)
  - [Network Management](#network-management)
- [Part 5: Complete Real‑World Example](#part-5-complete-real-world-example)
- [Quick Reference Cheatsheet](#quick-reference-cheatsheet)
- [Practice Exercises](#practice-exercises)
- [Cleanup Commands](#cleanup-commands)

---

## Part 1: Docker Volumes – Persistent Data Storage

### Understanding Data Persistence

By default, container data is lost when the container stops or is removed. The example below shows that manually created data survives a container restart but is lost when the container is removed.

```bash
# Start and write data to a container
docker start -ai test-container
mkdir /data
echo "hello World" > /data/Arnav-500121412.txt
exit

# Restart container – data is still there
docker start test-container
docker exec test-container cat /data/Arnav-500121412.txt
```

![Data Persistence Test](./Images/1.png)

### Volume Types

#### 1. Anonymous Volumes
Automatically created with a random name.

```bash
docker run -d -v /app/data --name web1 nginx
docker volume ls
```

![Anonymous Volume](./Images/2.png)

#### 2. Named Volumes
User‑defined, easy to reuse.

```bash
docker volume create mydata
docker run -d -v mydata:/app/data --name web2 nginx
docker volume ls
```

![Named Volume](./Images/3.png)

#### 3. Bind Mounts (Host Directory)

Mount a specific host directory into the container.

```bash
mkdir ~/myapp-data
docker run -d -v ~/myapp-data:/app/data --name web3 nginx
echo "From Host" > ~/myapp-data/host-file.txt
docker exec web3 cat /app/data/host-file.txt
```

![Bind Mounts](./Images/4.png)

### Practical Volume Examples

**Example 1: MySQL with persistent storage**

```bash
docker run -d --name mysql-db -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret mysql:8.0
docker stop mysql-db && docker rm mysql-db
# New container using the same volume – data is preserved
docker run -d --name new-mysql -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret mysql:8.0
```

![MySQL Persistent Storage](./Images/5.png)

**Example 2: Web app with custom configuration via bind mount**

```bash
mkdir ~/nginx-config
echo 'server { listen 80; server_name localhost; location / { return 200 "Hello from mounted config!"; }' > ~/nginx-config/nginx.conf
docker run -d --name nginx-custom -p 8080:80 -v ~/nginx-config/nginx.conf:/etc/nginx/conf.d/default.conf nginx
curl http://localhost:8080
```

![Web App Config](./Images/6.png)

### Volume Management Commands

```bash
docker volume ls                  # list all volumes
docker volume create app-volume   # create a named volume
docker volume inspect app-volume  # inspect volume details
docker volume prune               # remove unused volumes
docker volume rm <name>           # remove a specific volume
docker cp local-file.txt container:/path   # copy files into a volume
```

---

## Part 2: Environment Variables

### Setting Environment Variables

**Method 1: `-e` flag**

```bash
docker run -d --name app1 -e DEBUG="true" -p 3000:3000 nginx
```

![Environment Variables -e flag](./Images/6A.png)

**Method 2: `--env-file`**

```bash
echo "DATABASE_HOST=localhost" > .env
echo "DATABASE_PORT=5432" >> .env
echo "API_KEY=secret123" >> .env
docker run -d --env-file .env --name app2 nginx
```

![Environment Variables env-file](./Images/7.png)

**Method 3: In Dockerfile**

```dockerfile
FROM python:3.9-slim
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1
ENV PORT=5000
ENV DEBUG=false
EXPOSE 5000
CMD ["python", "app.py"]
```

![Dockerfile Environment Variables](./Images/20.png)

### Environment Variables in Applications (Python Flask example)

**app.py**
```python
import os
from flask import Flask

app = Flask(__name__)

db_host = os.environ.get('DATABASE_HOST', 'localhost')
debug_mode = os.environ.get('DEBUG', 'false').lower() == 'true'

@app.route('/config')
def config():
    return {'db_host': db_host, 'debug': debug_mode}

if __name__ == '__main__':
    port = int(os.environ.get('PORT', 5000))
    app.run(host='0.0.0.0', port=port, debug=debug_mode)
```

**Verify environment variables**

```bash
docker exec app1 env
docker exec app2 env
```

![Check Environment app1](./Images/8.png)
![Check Environment app2](./Images/9.png)

---

## Part 3: Docker Monitoring

### Basic Monitoring Commands

`docker stats` – real‑time resource usage

```bash
docker stats
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

![Docker Stats](./Images/10.png)

`docker top` – processes inside a container

```bash
docker top container-name
docker top container-name -ef
```

`docker logs` – application logs

```bash
docker logs -f container-name
docker logs --tail 100 container-name
docker logs --since 2024-01-15 container-name
```

### Container Inspection & Events

```bash
docker inspect container-name
docker inspect --format='{{.State.Status}}' container-name
docker inspect --format='{{.NetworkSettings.IPAddress}}' container-name
```

![Docker Inspect](./Images/13.png)

**Real‑time Docker events**

```bash
docker events
docker events --filter 'type=container' --filter 'event=start'
```

![Docker Events](./Images/14.png)

**Monitoring script (`monitor.sh`)**

```bash
#!/bin/bash
echo "=== Docker Monitoring Dashboard ==="
echo "Time: $(date)"
echo "1. Running Containers:"
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
echo "2. Resource Usage:"
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
echo "3. Recent Events:"
docker events --since '5m' --until '0s' --format '{{.Time}} {{.Type}} {{.Action}}' | tail
echo "4. System Info:"
docker system df
```

![Monitoring Script](./Images/21.png)

---

## Part 4: Docker Networks

### List Available Networks

```bash
docker network ls
```

![Network List](./Images/15.png)

### Network Types

#### 1. Bridge Network (default for custom networks)

```bash
docker network create my-network
docker run -d --name web1-net --network my-network nginx
docker run -d --name web2-net --network my-network nginx
docker exec web1-net curl http://web2-net
```

![Bridge Network Communication](./Images/16.png)

#### 2. Host Network

```bash
docker run -d --name host-app --network host nginx
curl http://localhost
```

![Host Network](./Images/17.png)

#### 3. None Network (completely isolated)

```bash
docker run -d --name isolated-app --network none alpine sleep 3600
docker exec isolated-app ifconfig   # only loopback
docker exec isolated-app ping google.com  # fails
```

![None Network](./Images/18.png)

### Network Management Commands

```bash
docker network create app-network
docker network connect app-network existing-container
docker network disconnect app-network container-name
docker network inspect bridge
docker network prune
```

### Port Publishing vs Exposing

```bash
docker run -d -p 80:8080 nginx          # host:container
docker run -d -p 8080 nginx             # dynamic host port
docker run -d -p 80:80 -p 443:443 nginx # multiple ports
```

### View All Running Containers

```bash
docker ps
```

![All Running Containers](./Images/19.png)

---

## Part 5: Complete Real‑World Example

**Architecture**: Flask web app + PostgreSQL + Redis, all on a custom network.

```bash
# 1. Create network
docker network create myapp-network

# 2. Start database with volume
docker run -d --name postgres --network myapp-network \
  -e POSTGRES_PASSWORD=mysecretpassword -e POSTGRES_DB=mydatabase \
  -v postgres-data:/var/lib/postgresql/data postgres:15

# 3. Start Redis
docker run -d --name redis --network myapp-network -v redis-data:/data redis:7-alpine

# 4. Start Flask app (custom image)
docker run -d --name flask-app --network myapp-network -p 5000:5000 \
  -v $(pwd)/app:/app -v app-logs:/var/log/app \
  -e DATABASE_URL="postgresql://postgres:mysecretpassword@postgres:5432/mydatabase" \
  -e REDIS_URL="redis://redis:6379" -e DEBUG="false" \
  --env-file .env.production flask-app:latest

# Monitoring
docker stats postgres redis flask-app
docker logs -f flask-app
docker exec flask-app ping -c 2 postgres
```

---

## Quick Reference Cheatsheet

| Category | Command |
|----------|---------|
| **Volumes** | `docker volume create <name>`<br>`docker run -v <volume>:/path`<br>`docker run -v /host/path:/container/path` |
| **Environment** | `docker run -e VAR=value`<br>`docker run --env-file .env`<br>`ENV VAR=value` (Dockerfile) |
| **Monitoring** | `docker stats`<br>`docker logs -f <container>`<br>`docker top <container>`<br>`docker inspect <container>` |
| **Networks** | `docker network create <name>`<br>`docker run --network <name>`<br>`docker network connect <network> <container>` |

---

## Practice Exercises

1. **Database Backup** – Create a PostgreSQL container with a volume, backup the data using `docker cp`, and restore to a new container.
2. **Multi‑Service Setup** – Run a web app + database + cache on a custom network, configure them with environment variables, and monitor all services.
3. **Log Analysis** – Run a container that generates logs, use `docker logs` filters, and redirect logs to a host file using a bind mount.
4. **Network Isolation** – Create two separate networks, place containers in each, test connectivity, then connect one container to both networks.

---

## Cleanup Commands

```bash
# Stop and remove all containers
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)

# Remove all unused volumes, networks, images
docker volume prune -f
docker network prune -f
docker image prune -f
```

---

## Key Takeaways

- **Volumes** persist data beyond container lifecycles.
- **Environment variables** make containers configurable and reusable.
- **Monitoring commands** (`stats`, `logs`, `top`, `events`) help debug and optimise containers.
- **Networks** allow secure, named communication between containers.
- Always use **named volumes** for production data and **custom networks** for service isolation.
- Use **.env files** for sensitive configuration.

---

*This experiment covers the essential Docker features needed to build, configure, and manage production‑ready containerised applications.*

**Author:** Arnav  
**Date:** April 24, 2026
```

**Important notes for GitHub:**

1. Create an `Images/` folder inside your `Experiment-5` directory and place all the PNG files (`1.png`, `2.png`, …, `21.png`, `6A.png`, etc.) there.
2. The `monitor.sh` script and the `app.py` + `Dockerfile` shown in the screenshots are already included as code blocks – you can also add them as separate files in your repository.
3. Adjust the image paths if you prefer to keep the screenshots in the same folder (just remove `Images/` from each `src`). The above assumes the standard `./Images/` subdirectory.

Now you can copy the whole block into your `README.md` and push it to GitHub.