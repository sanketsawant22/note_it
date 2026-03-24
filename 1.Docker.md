# 🐳 Docker Notes

These notes summarize key Docker concepts and commands, from running containers to using Docker Compose.  
Includes advanced topics like **networking**, **volumes**, **layer optimization**, and **multi-stage builds**.

---

## 🔹 Basic Commands

### Run a Docker Container
```bash
docker run -it ubuntu
```
- `docker run` → Runs a new container from an image  
- `-it` → Runs the container in **interactive** mode with a terminal attached  
- `ubuntu` → Name of the image to run

---

### Run a Named Container
```bash
docker run -it --name my-container ubuntu
```
- Runs the container and names it `my-container`.  
- You can refer to the container by name instead of its auto-generated ID.

---

### List Containers
```bash
docker container ls
```
Lists **running containers**.

```bash
docker container ls -a
```
Lists **all containers**, including stopped ones.

---

### Start / Stop a Container
```bash
docker start <container_id_or_name>
docker stop <container_id_or_name>
```
Starts or stops a specific container.

---

### Execute Commands in a Running Container
```bash
docker exec <container_name> ls
```
Executes the `ls` command inside the container and prints the output in your host terminal (without entering the container).

```bash
docker exec -it <container_name> bash
```
Opens an **interactive Bash shell** inside the container (you can run commands directly within the container).

---

## 🔹 Port Mapping

Allows you to expose a container’s internal port to your local host machine.

```bash
docker run -it -p 6000:8000 <image_name>
```
- Maps **container port `8000`** to **local port `6000`**  
- So, requests to `localhost:6000` are forwarded to port `8000` inside the container.

---

## 🔹 Environment Variables

You can pass environment variables while running a container.

```bash
docker run -it -e KEY1=value1 -e KEY2=value2 <image_name>
```

Environment variables are often used for configuration (e.g., database credentials, API keys, etc.).

---

## 🔹 Docker Networking

Docker provides different **network drivers** for containers to communicate.

### List and Inspect Networks
```bash
docker network ls
```
Lists all available networks.

```bash
docker network inspect bridge
```
Displays details of the default `bridge` network — including connected containers and configuration.

---

### Network Modes

#### 🧩 Bridge (Default)
- Default network mode for containers.
- Each container gets its own **private IP** within a virtual bridge network.  
- Containers can access the internet through the host.  
- Requires **port mapping** to expose container ports to the host.

Example:
```bash
docker run -it --network=bridge ubuntu
```

#### 🌐 Host
- The container shares the **same network as the host machine**.  
- No port mapping is required — container and host use the same IP.

Example:
```bash
docker run -it --network=host ubuntu
```

#### 🚫 None
- The container has **no network access** (completely isolated).

Example:
```bash
docker run -it --network=none ubuntu
```

---

### Creating a Custom Network
```bash
docker network create -d bridge my-network
```
- Creates a new custom network using the `bridge` driver.

Now, if you run multiple containers on this custom network:
```bash
docker run -it --network=my-network --name container1 ubuntu
docker run -it --network=my-network --name container2 ubuntu
```
They can communicate with each other using container names:
```bash
ping container2
```

---

## 🔹 Volume Mounting (Data Persistence)

By default, containers are **ephemeral** — once deleted, their data is lost.  
To persist data, Docker provides **volumes**.

### Example: Mount a Volume
```bash
docker run -it -v /Users/sanket/Desktop/test:/home/abc --name sanket ubuntu
```
- Mounts a local folder (`/Users/sanket/Desktop/test`) to a folder inside the container (`/home/abc`).  
- Any changes in this directory are **synchronized** both ways.  
- Data persists even after the container is removed.

💡 **Use Case:** Database containers like MySQL or PostgreSQL often use volumes to persist data between restarts.

---

## 🔹 Layer Caching and Build Optimization

Docker builds images in **layers**.  
Each command in the `Dockerfile` creates a new layer.

### Example (Inefficient Build Order)
```dockerfile
FROM ubuntu
RUN apt-get install -y nodejs
COPY main.js .
RUN npm install
ENTRYPOINT ["node", "main.js"]
```
If you change `main.js`, Docker will re-run **`npm install` unnecessarily**.

### Optimized Order (Efficient Caching)
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --omit=dev && npm cache clean --force
COPY . .
EXPOSE 8000
CMD ["node", "main.js"]
```
By copying `package.json` before the main code, Docker can reuse cached layers for faster rebuilds.

🧠 **Tip:** Order Dockerfile instructions from least to most frequently changed.

---

## 🔹 Multi-Stage Builds

Used to optimize builds (especially for compiled languages like TypeScript, Go, Java).  
This approach separates **build** and **runtime** stages to reduce image size.

### Example: Node.js + TypeScript App
```dockerfile
# Stage 1: Build the app
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build  # compiles TS to JS

# Stage 2: Run the app
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm install --omit=dev
EXPOSE 8000
ENTRYPOINT ["node", "dist/main.js"]
```
✅ Smaller final image  
✅ Faster deployment  
✅ Cleaner separation between development and runtime

---

## 🔹 Docker Compose

Docker Compose helps manage **multiple containers** (like backend + database + cache) using a single YAML file.

### Example: `docker-compose.yml`
```yaml
version: "3.8"

services:
  postgres:
    image: postgres:latest
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydb

  redis:
    image: redis:latest
    ports:
      - "6379:6379"
```

---

### Useful Commands

```bash
docker compose up
```
Starts all services defined in `docker-compose.yml`.

```bash
docker compose down
```
Stops and removes all containers, networks, and volumes defined by Compose.

```bash
docker compose up -d
```
Runs containers in **detached mode** (in the background).

---

### 🧩 Notes
- All containers started by Docker Compose are grouped in a **single network**.  
- Containers can communicate using **service names** as hostnames (e.g., `postgres`, `redis`).

---

## 🧹 Common Tips

- Use `docker ps` as a shorthand for `docker container ls`.  
- Use `docker logs <container_name>` to view logs.  
- Use `docker rm <container_name>` to remove stopped containers.  
- Use `docker rmi <image_name>` to remove unused images.  
- To prune unused data:
  ```bash
  docker system prune
  ```

---

## ✅ Summary

| Task | Command |
|------|----------|
| Run container | `docker run -it <image>` |
| Run named container | `docker run -it --name <name> <image>` |
| List containers | `docker container ls` |
| Execute command | `docker exec <container> <cmd>` |
| Enter container shell | `docker exec -it <container> bash` |
| Build image | `docker build -t <image_name> .` |
| Create network | `docker network create -d bridge <name>` |
| Run container on network | `docker run -it --network=<network> <image>` |
| Mount volume | `docker run -v <host_dir>:<container_dir>` |
| Start Compose stack | `docker compose up` |
| Stop Compose stack | `docker compose down` |

---

**🚀 With these concepts and commands, you can efficiently build, network, and manage containerized applications using Docker and Docker Compose.**
