## Experiment 4: Docker Essentials

##### `Dockerfile` `.dockerignore` `tagging` `publishing` 

<hr>

<h4 align="center"> Containerizing Applications with Dockerfile </h4>

<hr>

**Step-1: Create a Directory for Python application**
```bash
mkdir my-flask-app
cd my-flask-app
```
![Create Directory](./Images/1.png)

**Step-2:- Create an **`app.py`:****
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Docker!"

@app.route('/health')
def health():
    return "OK"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
![Create Application](./Images/2.png)

**Step-3:- Create **`requirements.txt`:****
```
Flask==2.3.3
```
![Create Requirment](./Images/3.png)


**Step-4: Create **Dockerfile****
```dockerfile
# Use Python base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy requirements file
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY app.py .

# Expose port
EXPOSE 5000

# Run the application
CMD ["python", "app.py"]
```
![Create DockerFile](./Images/4.png)


**Step-5:- Create a **`.dockerignore`:****
```
# Python files
__pycache__/
*.pyc
*.pyo
*.pyd

# Environment files
.env
.venv
env/
venv/

# IDE files
.vscode/
.idea/

# Git files
.git/
.gitignore

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Test files
tests/
test_*.py
```
![Create dockerignore file](./Images/5.png)


_Why **`.dockerignore`** is Important_
- Prevents unnecessary files from being copied
- Reduces image size
- Improves build speed
- Increases security



**Step-6: Build image from Dockerfile**
```bash
docker build -t my-flask-app .
```
![Build Image](./Images/6.png)

**Step-7:- Verify Image**
```bash
docker images
```
![List Images](./Images/7.png)


**Step-8: Add Tag in Image**
```bash
docker build -t my-flask-app:1.0 .
```
![Add Tag](./Images/8.png)


**Step-9:- Add multiple tags**
```bash
docker build -t my-flask-app:latest -t my-flask-app:1.0 .
```
![Multi Tag](./Images/9.png)


**Step-10:- Add Tag with custom registry**
```bash
docker build -t username/my-flask-app:1.0 .
```
![Custom Registry](./Images/10.png)


**Step-11:- Tag existing image**
```bash
docker tag my-flask-app:latest my-flask-app:v1.0
```
![Tagging Existing Image](./Images/11.png)


**Step-12: View Image Details**
```bash
docker images
```
![List Images](./Images/12.png)


**Step-13:- Show image history**
```bash
docker history my-flask-app
```
![Image History](./Images/13.png)


**Step-14:- Inspect image details**
```bash
docker inspect my-flask-app
```
![Inspect Image](./Images/14.png)




**Step-15: Run Container**
```bash
docker run -d -p 5000:5000 --name flask-container my-flask-app
```
![Create DockerFile](./Images/15.png)


**Step-16:- Test the application**
```bash
curl http://localhost:5000
```
![Test Application](./Images/16.png)


**Step-17:- View running containers**
```bash
docker ps
```
![List Containers](./Images/17.png)


**Step-18:- View container logs**
```bash
docker logs flask-container
```
![See Logs](./Images/18.png)



**Step-19:- Stop container**
```bash
docker stop flask-container
```
![Stop Container](./Images/19.png)


**Step-20:- Start stopped container**
```bash
docker start flask-container
```
![Start Container](./Images/20.png)


**Step-21:- Remove container**
```bash
docker rm flask-container
```
![Remove Container](./Images/21.png)


**Step-22:- Remove container forcefully** \
Since Container is in Running State, so we will remove it forcefully...
```bash
docker rm -f flask-container
```
![Create DockerFile](./Images/22.png)




### Multi-stage Builds

**Why Multi-stage Builds?**
- Smaller final image size
- Better security (remove build tools)
- Separate build and runtime environments

**Step-23: Create a Simple **`Dockerfile.multistage`:****
```dockerfile
# STAGE 1: Builder stage
FROM python:3.9-slim AS builder

WORKDIR /app

# Copy requirements
COPY requirements.txt .

# Install dependencies in virtual environment
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"
RUN pip install --no-cache-dir -r requirements.txt

# STAGE 2: Runtime stage
FROM python:3.9-slim

WORKDIR /app

# Copy virtual environment from builder
COPY --from=builder /opt/venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Copy application code
COPY app.py .

# Create non-root user
RUN useradd -m -u 1000 appuser
USER appuser

# Expose port
EXPOSE 5000

# Run application
CMD ["python", "app.py"]
```
![Create DockerFile](./Images/23.png)



**Step-24:- Build regular image**
```bash
docker build -t flask-regular .
```
![Build Regular Image](./Images/24.png)


**Step-25:- Build multi-stage image**
```bash
docker build -f Dockerfile.multistage -t flask-multistage .
```
![Build Multi-Stage Image](./Images/25.png)


**Step-26:- Compare sizes**
```bash
docker images | grep flask-
```
![Create DockerFile](./Images/26.png)




### Publishing to Docker Hub

**Step-27:- Login to Docker Hub**
```bash
docker login
```
![Login DockerHub](./Images/27.png)


**Step-28:- Tag image for Docker Hub**
```bash
docker tag my-flask-app:latest username/my-flask-app:1.0
docker tag my-flask-app:latest username/my-flask-app:latest
```
![Tag Imag](./Images/28.png)


**Step-29:-  Push to Docker Hub**
```bash
docker push username/my-flask-app:1.0
```
![Push 1.0 Tag Image](./Images/29.png)

**Step-30:- Push Latest Image**
```bash
docker push username/my-flask-app:latest
```
![Push Image](./Images/30.png)


**Step-31:- Pull from Docker Hub (on another machine)**
```bash
docker pull username/my-flask-app:latest
```
![Pull Image](./Images/31.png)


**Step-32:- Run the pulled image**
```bash
docker run -d -p 5000:5000 username/my-flask-app:latest
```
![Run Container](./Images/32.png)



### Node.js Application Example (Quick Version)**

**Step-33:- Create a directory Node.js Application**
```bash
mkdir my-node-app
cd my-node-app
```
![Create DockerFile](./Images/33.png)


**Step-34:- Create a **`app.js`:****
```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
    res.send('Hello from Node.js Docker!');
});

app.get('/health', (req, res) => {
    res.json({ status: 'healthy' });
});

app.listen(port, () => {
    console.log(`Server running on port ${port}`);
});
```
![Create app.js](./Images/34.png)


**Step-35:- Create a **`package.json`:****
```json
{
  "name": "node-docker-app",
  "version": "1.0.0",
  "main": "app.js",
  "dependencies": {
    "express": "^4.18.2"
  }
}
```
![Create package file](./Images/35.png)


**Step-36:- Create a Node.js Dockerfile**
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --only=production

COPY app.js .

EXPOSE 3000

CMD ["node", "app.js"]
```
![Create DockerFile](./Images/36.png)


**Step-37:- Build image**
```bash
docker build -t my-node-app .
```
![Build Image](./Images/37.png)


**Step-38:- Run container**
```bash
docker run -d -p 3000:3000 --name node-container my-node-app
```
![Run Container](./Images/38.png)


**Step-39:- Test on Terminal**
```bash
curl http://localhost:3000
```
![Create DockerFile](./Images/39.png)


**Step-40:- Test on Browser**
![Browser Test](./Images/40.png)


<hr>

<h4 align="center"> DockerHub </h4>

View Pushed Image on [DockerHub](https://hub.docker.com/repository/docker/arnavarnav/my-flask-app/general)

![DockerHub](./Images/41.png)

<hr>



<h4 align="center"> Conclusion </h4>

<hr>


## **Essential Docker Commands Cheatsheet**

| Command | Purpose | Example |
|---------|---------|---------|
| `docker build` | Build image | `docker build -t myapp .` |
| `docker run` | Run container | `docker run -p 3000:3000 myapp` |
| `docker ps` | List containers | `docker ps -a` |
| `docker images` | List images | `docker images` |
| `docker tag` | Tag image | `docker tag myapp:latest myapp:v1` |
| `docker login` | Login to Dockerhub using username and password or token | `echo "token" | docker login -u username --password-stdin` |
| `docker push` | Push to registry | `docker push username/myapp` |
| `docker pull` | Pull from registry | `docker pull username/myapp` |
| `docker rm` | Remove container | `docker rm container-name` |
| `docker rmi` | Remove image | `docker rmi image-name` |
| `docker logs` | View logs | `docker logs container-name` |
| `docker exec` | Execute command | `docker exec -it container-name bash` |



## **Common Workflow Summary**

### **Development Workflow:**
```bash
# 1. Create Dockerfile and .dockerignore
# 2. Build image
docker build -t myapp .

# 3. Test locally
docker run -p 8080:8080 myapp

# 4. Tag for production
docker tag myapp:latest myapp:v1.0

# 5. Push to registry
docker push myapp:v1.0
```

### **Production Workflow:**
```bash
# 1. Pull from registry
docker pull myapp:v1.0

# 2. Run in production
docker run -d -p 80:8080 --name prod-app myapp:v1.0

# 3. Monitor
docker logs -f prod-app
```

---

## **Key Takeaways**

1. **Dockerfile** defines how to build your image
2. **.dockerignore** excludes unnecessary files (important for security and performance)
3. **Tagging** helps version and organize images
4. **Multi-stage builds** create smaller, more secure production images
5. **Docker Hub** allows sharing and distributing images
6. **Always test** images locally before publishing

---

## **Cleanup**
```bash
# Remove all stopped containers
docker container prune

# Remove unused images
docker image prune

# Remove everything unused
docker system prune -a
```

<hr>

<div align="center">

<a href="../Experiment-3/">
  <img src="https://img.shields.io/badge/⬅️_Previous-grey?style=for-the-badge">
</a>
&nbsp;
<a href="../../Lab/">
  <img src="https://img.shields.io/badge/⌂_Home-black?style=for-the-badge">
</a>

</div>