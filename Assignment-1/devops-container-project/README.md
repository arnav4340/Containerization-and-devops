## Containerized Web Application with PostgreSQL using Docker Compose and IPvlan
Project Assignment 1
Containerization and DevOps

This project demonstrates a containerized web application architecture using Docker.

The backend service is implemented using Node.js and Express, while PostgreSQL is used as the database. Both services are containerized and orchestrated using Docker Compose.

Networking is implemented using IPvlan, which assigns static IP addresses to containers inside a defined subnet.

System Architecture

Client → Backend API → PostgreSQL Database

The backend container communicates with the PostgreSQL container using an internal IPvlan network.

Key features implemented:

Multi-stage Docker builds

Separate Dockerfiles

Docker Compose orchestration

IPvlan networking

Static container IPs

Persistent database storage

<hr>

<h4 align="center"> Pre-requisite </h4>

![Directory Structure](./Images/0.png)

<hr>

**Step-1:-This screenshot shows the project folder structure containing two main directories: backend and database. The backend contains the Node.js application files while the database folder contains the PostgreSQL Dockerfile.**
```bash
tree /F
```
![Initialize Node Package](./Images/1.png)


**Step-2:- This file contains the Node.js Express API that connects to PostgreSQL. It automatically creates the users table and provides endpoints for health check, inserting users, and retrieving users.

GET /health → check API status

POST /user → insert user into database

GET /users → fetch users from database**
```bash
npm i express pg
```
![Install Package](./Images/2.png)


**Step-3:-Package.json Configuration**
![package.json](./Images/3.png)


**Step-4:-Backend Server Implementation-**
![server.js](./Images/4.png)


**Step-5:-Backend Dockerfile
This Dockerfile defines how the backend application container image is built using a minimal Node.js base image**
![Dockerfile of backend](./Images/5.png)


**Step-6:- The .dockerignore file excludes unnecessary files such as node_modules and logs from being copied into the Docker image**
![dockerignore](./Images/6.png)


**Step-7:-A separate Dockerfile is created for the PostgreSQL database container**
![Dockerfile](./Images/7.png)


**Step-8:- The `init.sql` will look as follows:-**
![init.sql](./Images/8.png)


**Step-9:- The `docker-compose.yml` will look as follows:-**
![docker-compose.yml](./Images/9.png)


**Step-10:- Find your interface**
```bash
ip a
```
![Find Interface](./Images/10.png)


**Step-11:- Create Network**
```bash
docker network create -d macvlan \
  --subnet=192.168.50.0/24 \
  --gateway=192.168.50.1 \
  -o parent=eth0 \
  macvlan_net
```
![Create Network](./Images/11.png)


**Step-12:- Build from Compose**
```bash
docker-compose up build --no-cache
```
![Build Compose](./Images/12.png)


**Step-13:- Start Services**
```bash
docker-compose up -d
```
![Start Service](./Images/13.png)


**Step-14:- Insert A User in DB in API**
```bash
curl -X POST http://192.168.50.20:3000/users \
-H "Content-Type: application/json" \
-d '{"name":"Dhairya"}'
```
![Insert User](./Images/14.png)


**Step-15:- GET User API**
```bash
curl http://192.168.50.20:3000/users
```
![Get User API](./Images/15.png)


**Step-16:- List Running Container**
```bash
docker ps
```
![List Containers](./Images/16.png)


**Step-17:- List Volumes**
```bash
docker volume ls 
```
![List Volumes](./Images/17.png)


**Step-18:- Inspect Network**
```bash
docker network inspect macvlan_net
```
![Inspect Network](./Images/18.png)


**Step-19:- Inspect Backend Container**
```bash
docker inspect node_backend
```
![Inspect Backend](./Images/19.png)


**Step-20:- Inspect DB**
```bash
docker inspect postgres_db
```
![Inspect DB](./Images/20.png)


**Step-21:- Verify Data Persistence**
This step will verify that data stored in DB is permanently saved irrespective of the state of the container.
```bash
docker-compose down
docker-compose up -d
curl http://192.168.50.20:3000/users
```
![Restart Compose & Run API](./Images/21.png)



<hr>

<h4 align="center"> Report </h4>

<hr>


**_Build Optimization Explanation_**

Several optimization techniques were used while building the Docker images to make them efficient, secure, and lightweight.

First, a multi-stage build was used in the backend Dockerfile. In this approach, the application dependencies are installed in a builder stage, and only the necessary files are copied to the final runtime stage. This helps reduce the final image size because build tools and unnecessary files are not included in the runtime container.

Second, a minimal base image (node:20-alpine) was used instead of a full Node.js image. Alpine Linux is lightweight and helps reduce the image size significantly, resulting in faster downloads and faster container startup.

A .dockerignore file was also used to exclude unnecessary files such as node_modules, .git, and log files from the Docker build context. This improves build performance and prevents unwanted files from being included in the image.

> Finally, the container runs using a non-root user to improve security. Running containers without root privileges reduces potential risks if the application is compromised.


<br>

**_Network Design Diagram_**

![Network Diagram](./Images/22.jpg)


<br>


**_Image Size Comparison_**

Different base images can significantly affect the final Docker image size.

- node:20 => 1.1 GB
- node:20-alpine => 180 MB

The Alpine-based image is much smaller than the standard Node.js image. Using a smaller image reduces storage usage, speeds up image downloads, and improves container startup time.

Therefore, the `node:20-alpine` image was chosen in this project to create a lightweight and efficient container.


**_Macvlan Vs IPvlan_**

| Feature | MACVLAN | IPVLAN |
|--------|---------|--------|
| MAC addresses | One per container | One shared for all |
| Network switch load | Higher (learns many MACs) | Lower (one MAC) |
| Scalability | Limited by switch | Much higher |
| Best for | Small deployments | Large-scale |