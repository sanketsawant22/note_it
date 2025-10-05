# 🐳 Docker Notes

These notes summarize key Docker concepts and commands, from running containers to using Docker Compose.

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

## 🔹 Containerizing a Node.js Application

### 1. Create a `Dockerfile` in your app’s root directory  
*(Same folder where your `server.js` or main entry file exists)*

Example:
```dockerfile
# Use an official Node.js base image
FROM node:18

# Set working directory inside container
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy source code
COPY . .

# Expose the port your app runs on
EXPOSE 8000

# Command to run the application
ENTRYPOINT ["node", "main.js"]
```

---

### 2. Build the Docker Image
```bash
docker build -t <image_name> .
```
- `-t` → Tags the image with a name  
- `.` → Path to the Dockerfile (current directory)

---

### 3. Run the Container
```bash
docker run -it -p 6000:8000 <image_name>
```
Runs your Node.js app inside a container and maps ports for local access.

---

## 🔹 Layer Caching

Docker builds images in **layers**.  
Each command (like `FROM`, `COPY`, `RUN`, etc.) creates a separate layer.

- When rebuilding, **unchanged layers are cached**, speeding up the build.  
- Only the layers **after the modified instruction** are rebuilt.

🧠 **Tip:**  
Order your Dockerfile intelligently — put commands that change less frequently (like `npm install`) near the top, and the ones that change frequently (like copying source code) near the bottom.

---

## 🔹 Docker Compose

Docker Compose helps manage **multiple containers** (like backend + database + cache) easily using a single YAML file.

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
- All containers started by Docker Compose are grouped in a **single network** (default name: based on project folder).  
- Containers can communicate with each other using their **service names** (e.g., `postgres`, `redis`) as hostnames.

---

## 🧹 Common Tips

- Use `docker ps` as a shorthand for `docker container ls`.  
- Use `docker logs <container_name>` to view container logs.  
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
| List containers | `docker container ls` |
| Execute command | `docker exec <container> <cmd>` |
| Enter container shell | `docker exec -it <container> bash` |
| Build image | `docker build -t <image_name> .` |
| Start Compose stack | `docker compose up` |
| Stop Compose stack | `docker compose down` |

---

**🚀 With these commands and concepts, you can build, run, and manage containerized applications efficiently using Docker and Docker Compose.**
