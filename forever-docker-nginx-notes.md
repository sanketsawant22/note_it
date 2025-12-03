
# Forever Project – Docker, Nginx & Routing Deep Dive (Full Revision)

This is a full revision of **everything we did step by step** for your project:

- Backend (Node + Express)
- Frontend (Vite + React)
- Admin (Vite + React)
- Dockerfiles
- Docker Compose
- Nginx (per-app + global reverse proxy)
- Routing issues and their fixes

The goal: make sure you understand **what we did, why we did it, and how it all fits together**.

---

## 1. Starting Point – Dockerizing the Backend

### 1.1 Backend Dockerfile

We started with a simple **Dockerfile** in the `backend/` folder:

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 4000

CMD ["npm", "start"]
```

What each line does:

- `FROM node:18-alpine`  
  Uses a lightweight Node.js 18 image as the base.

- `WORKDIR /app`  
  Sets the working directory inside the container to `/app`.

- `COPY package*.json ./`  
  Copies `package.json` and optionally `package-lock.json` to `/app`.

- `RUN npm install`  
  Installs all dependencies **inside** the container.

- `COPY . .`  
  Copies the rest of the backend code into the container.

- `EXPOSE 4000`  
  Documents that the app listens on port 4000 **inside** the container.

- `CMD ["npm", "start"]`  
  The command that actually starts your backend (`node server.js` under the hood).

---

### 1.2 Backend .dockerignore

We added a `.dockerignore` file in `backend/`:

```gitignore
node_modules
.git
.env
```

Why?

- `node_modules` – we don’t want to copy host-installed modules into the image.
- `.git` – no need to ship git history into the image.
- `.env` – we don’t want secrets baked into the Docker image.  
  Instead, we pass them at **runtime** using `--env-file` or `env_file` in Compose.

---

### 1.3 Building the Backend Image

From inside `backend/`:

```bash
docker build -t forever-backend .
```

- `-t forever-backend` – names the image `forever-backend`.
- `.` – uses the current directory as build context.

Docker:

1. Reads the Dockerfile
2. Uses `node:18-alpine` base image
3. Copies `package*.json`
4. Runs `npm install`
5. Copies the rest of the code
6. Produces an image named `forever-backend`

---

### 1.4 Running Backend with Environment Variables

You had a `.env` like:

```env
PORT=4000
MONGODB_URI=mongodb+srv://...
CLOUDINARY_API_KEY=...
CLOUDINARY_SECRET_KEY=...
CLOUDINARY_CLOUD_NAME=...
JWT_SECRET=...
ADMIN_EMAIL=...
ADMIN_PASSWORD=...
STRIPE_SECRET_KEY=...
RAZORPAY_KEY_ID=...
RAZORPAY_SECRET_KEY=...
```

We ran the container like:

```bash
docker run -d   --name forever-backend   -p 4000:4000   --env-file .env   forever-backend
```

What this does:

- `-d` – run in background (detached).
- `--name forever-backend` – gives the container a name.
- `-p 4000:4000` – maps **host** port 4000 → **container** port 4000.
- `--env-file .env` – injects env vars from `.env` into the container.
- `forever-backend` – image to run.

We also used:

```bash
docker run --env-file .env forever-backend printenv
```

to verify envs **inside** the container.

---

### 1.5 The MongoDB URI Quotes Bug

Initially your `.env` had quoted values like:

```env
MONGODB_URI="mongodb+srv://..."
```

This works with local `dotenv` because it strips quotes, but **Docker does not**.

In Docker:
- The value becomes literally `"mongodb+srv://..."` with quotes.
- MongoDB rejects it: *Invalid scheme, expected connection string to start with "mongodb://" or "mongodb+srv://"*.

Fix:

- Remove quotes:

```env
MONGODB_URI=mongodb+srv://...
```

Same for all env values.

---

### 1.6 app.listen Must Bind to 0.0.0.0

Inside `server.js`, for Docker you should use:

```js
app.listen(process.env.PORT, "0.0.0.0", () => {
  console.log(`Server is Listening on : http://localhost:${process.env.PORT}`);
});
```

- `"0.0.0.0"` means “listen on all network interfaces inside container”.
- If you only listen on `localhost`, other containers can’t reach it.

---

## 2. Dockerizing the Frontend (Vite + Nginx)

Your frontend is a **Vite + React** app.

### 2.1 Frontend Dockerfile (Multi-stage Build)

In `frontend/`:

```dockerfile
# 1) Build Stage
FROM node:18-alpine AS build

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

# 2) Production Stage (Nginx)
FROM nginx:alpine

# Vite outputs to `dist`, not `build`
COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

How it works:

1. **Build stage** (`node:18-alpine`):
   - Installs dependencies
   - Runs `npm run build`
   - Generates `dist/` folder

2. **Production stage** (`nginx:alpine`):
   - Copies built files into `/usr/share/nginx/html`
   - Nginx serves static files from that folder
   - Listens on port 80 inside container

This is the standard production setup for Vite/React.

---

### 2.2 Frontend .dockerignore Bug (Env Files)

Initially we had:

```gitignore
node_modules
build
.git
.env
.env.*
```

This **ignored all env files**, including `.env.production`, so Vite could not read `VITE_*` vars during build. That’s why:

- `import.meta.env.VITE_BACKEND_URL` was `undefined`
- Final requests looked like `/undefined/api/product/list`

Fix:

```gitignore
node_modules
dist
.git
```

We **stopped ignoring** `.env.production` so Vite can use it during `npm run build`.

---

### 2.3 Vite Env Setup

We used:

```env
# frontend/.env.production
VITE_BACKEND_URL=/api
```

In code:

```js
const backendUrl = import.meta.env.VITE_BACKEND_URL;
// Example:
axios.get(`${backendUrl}/product/list`);
```

Important points:

- Vite only exposes env vars that start with `VITE_`.
- You must use `import.meta.env.VITE_SOMETHING` (not `process.env`).

---

## 3. Why We Moved to Docker Compose

Running everything manually with `docker run` becomes a pain when you have:

- backend
- frontend
- admin
- reverse proxy (later)
- maybe database

We wanted:

- **One command** to start everything: `docker compose up`
- Shared private network between containers
- Service names like `backend`, `frontend`, `admin` instead of IPs
- Cleaner config for ports and envs

So we created a **docker-compose.yml** at the project root.

---

### 3.1 Initial docker-compose.yml (backend + frontend + admin)

```yaml
version: "3.9"

services:
  backend:
    build: ./backend
    container_name: forever-backend
    ports:
      - "4000:4000"
    env_file:
      - ./backend/.env
    networks:
      - forever-net

  frontend:
    build: ./frontend
    container_name: forever-frontend
    ports:
      - "3000:80"
    depends_on:
      - backend
    networks:
      - forever-net

  admin:
    build: ./admin
    container_name: forever-admin
    ports:
      - "3001:80"
    depends_on:
      - backend
    networks:
      - forever-net

networks:
  forever-net:
    driver: bridge
```

Key ideas:

- All services share the `forever-net` network.
- Containers can reach each other by service name:
  - `backend:4000`
  - `frontend:80`
  - `admin:80`
- Backend gets envs via `env_file` (secure, not baked into image).

---

### 3.2 Frontend → Backend: localhost vs backend

Inside Docker:

- Frontend container is not your host machine.
- If frontend code calls `http://localhost:4000`:
  - It points to the frontend container itself, not backend.
- With Docker Compose, backend is reachable as `http://backend:4000` **from other containers**, but:
  - The **browser** still runs on your host machine, outside Docker.
  - The browser can’t resolve `backend` hostname – only containers can.

So from the browser’s perspective, the backend is at:

```text
http://localhost:4000
```

because of:
```yaml
ports:
  - "4000:4000"
```

That’s why we set:

```env
VITE_BACKEND_URL=http://localhost:4000
```

instead of `http://backend:4000`.

Then we later improved it with reverse proxy and changed to:

```env
VITE_BACKEND_URL=/api
```

so that all calls go through Nginx.

---

## 4. SPA Refresh Problems and Nginx Fix

### 4.1 Frontend Refresh Issue

Problem:

- `http://localhost:3000/collection` works when navigating within SPA.
- Refreshing `/collection` gives **404 Not Found (nginx)**.

Reason:

- Browser sends `GET /collection` to Nginx.
- Nginx looks for `/usr/share/nginx/html/collection` – doesn’t exist.
- For SPA, all routes should return `index.html` and let React Router handle it.

Fix in `frontend/nginx.conf`:

```nginx
server {
    listen 80;
    server_name _;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri /index.html;
    }
}
```

Then Dockerfile (frontend) used:

```dockerfile
FROM nginx:alpine

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

Now, any unknown path (`/collection`, `/cart`, `/anything`) falls back to `index.html`.

---

### 4.2 Admin Refresh Issue

We had a similar issue for admin routes such as:

```text
http://localhost/admin/list
```

On refresh, we got:

- 404 from Nginx
- Logs like: `open() "/usr/share/nginx/html/list" failed`

Reason:

- Admin container’s Nginx was also trying to serve `/list` as a real file.
- It needed SPA fallback just like frontend.

Fix in `admin/nginx.conf`:

```nginx
server {
    listen 80;
    server_name _;

    root /usr/share/nginx/html;

    # SPA fallback
    location / {
        try_files $uri /index.html;
    }

    location /assets/ {
        try_files $uri =404;
    }
}
```

This means **inside admin container**, the app is served at `/`, not `/admin`.  
The `/admin` prefix is handled at the reverse proxy level, not here.

---

### 4.3 Vite Base Path and Admin

For admin, we added a `base` in Vite config:

```js
// admin/vite.config.js
export default defineConfig({
  base: '/admin/',
  plugins: [react(), tailwindcss()],
  server: { port: 5174 },
});
```

And in React Router:

```jsx
<BrowserRouter basename="/admin">
  {/* routes */}
</BrowserRouter>
```

This ensures:

- All routes are under `/admin/*` from the browser’s perspective.
- Assets use `/admin/assets/...` paths.

Reverse proxy then forwards `/admin/...` to the admin container.

---

## 5. Adding a Global Nginx Reverse Proxy

We then added a **global Nginx reverse proxy** as the public entry point.

### 5.1 Updated docker-compose.yml (with reverse-proxy)

```yaml
version: "3.9"

services:
  reverse-proxy:
    image: nginx:alpine
    container_name: forever-gateway
    ports:
      - "80:80"
    volumes:
      - ./nginx/reverse-proxy.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - backend
      - frontend
      - admin
    networks:
      - forever-net

  backend:
    build: ./backend
    container_name: forever-backend
    ports:
      - "4000:4000"
    env_file:
      - ./backend/.env
    networks:
      - forever-net

  frontend:
    build: ./frontend
    container_name: forever-frontend
    expose:
      - "80"
    networks:
      - forever-net

  admin:
    build: ./admin
    container_name: forever-admin
    expose:
      - "80"
    networks:
      - forever-net

networks:
  forever-net:
    driver: bridge
```

Changes:

- `reverse-proxy` now listens on port 80 on the host.
- Frontend and admin only `expose` port 80 **internally**, not public.
- All traffic goes through the gateway.

---

### 5.2 reverse-proxy.conf

```nginx
events {}

http {
    server {
        listen 80;
        server_name _;

        # FRONTEND (root)
        location / {
            proxy_pass http://frontend:80;
            proxy_set_header Host $host;
        }

        # ADMIN
        location /admin/ {
            proxy_pass http://admin:80/;
            proxy_set_header Host $host;
        }

        # BACKEND API
        location /api {
            proxy_pass http://backend:4000;
            proxy_set_header Host $host;
        }
    }
}
```

Routing behavior:

- `/` → `frontend:80`
- `/admin/...` → `admin:80/...`
- `/api/...` → `backend:4000/...`

From browser POV:

- `http://localhost/` – frontend
- `http://localhost/admin` – admin
- `http://localhost/api/...` – backend

This is a **clean, production-style architecture**.

---

## 6. API URLs and the `/api/api` Bug

When we set:

```env
VITE_BACKEND_URL=/api
```

Your code was doing:

```js
axios.get(`${backendUrl}/api/product/list`);
```

Result:

```text
/api + /api/product/list = /api/api/product/list    ❌
```

Correct usage:

```js
axios.get(`${backendUrl}/product/list`);
// => /api/product/list
```

This was fixed for both frontend and admin.

---

## 7. Admin UX: Redirect /admin → /admin/add

You wanted:

- Visiting `/admin` should automatically go to `/admin/add`.

We fixed this in React Router with `<Navigate>`:

```jsx
import { BrowserRouter, Routes, Route, Navigate } from "react-router-dom";

function App() {
  return (
    <BrowserRouter basename="/admin">
      <Routes>
        {/* Default redirect */}
        <Route path="/" element={<Navigate to="/add" replace />} />

        <Route path="/add" element={<AddPage />} />
        <Route path="/list" element={<ListPage />} />
        {/* other routes */}
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

Now:

- `/admin` → `/admin/add` automatically
- Sidebar shows + content shows
- Refresh works due to correct Nginx SPA setup.

---

## 8. Mental Model of Your Final Architecture

### 8.1 High-level Flow

```text
Browser
  ↓
http://localhost or your future domain
  ↓
[reverse-proxy container: Nginx]
  ├── /           → frontend (Nginx, Vite build)
  ├── /admin/...  → admin (Nginx, Vite build)
  └── /api/...    → backend (Node + Express)
```

### 8.2 Container Responsibilities

- **reverse-proxy (Nginx)**  
  - Public entry point
  - Routes `/`, `/admin`, `/api` to correct services
  - In future: will handle HTTPS (SSL)

- **frontend (Nginx)**  
  - Serves built Vite React SPA
  - Handles SPA routing via `try_files $uri /index.html`

- **admin (Nginx)**  
  - Serves admin SPA
  - Handles SPA routing for `/admin/...` via `index.html` inside container
  - UI redirect: `/admin` → `/admin/add`

- **backend (Node + Express)**  
  - Handles `/api/...` routes
  - Uses env vars from `.env` via `env_file`
  - Listens on `0.0.0.0:4000`

---

## 9. Why Docker Compose Was Necessary

We moved from individual `docker run` commands to `docker compose` because:

- You now have **multiple containers** that must work together:
  - backend
  - frontend
  - admin
  - reverse-proxy
- They need a **shared network** (`forever-net`).
- We want **service discovery** via names like `backend`, `frontend`, `admin`.
- You want **one command** to start everything:

```bash
docker compose up --build
```

Instead of 3–4 different `docker run` commands.

Compose also:

- Keeps your configuration versioned
- Makes it easy to deploy on a server
- Is the common industry practice for multi-service apps.

---

## 10. Summary: What You’ve Achieved

You now have:

- A backend properly containerized with Node + envs passed securely.
- Frontend packaged using multi-stage Docker builds and served via Nginx.
- Admin panel packaged the same way, with its own routing and base path.
- Docker Compose orchestrating all containers on a shared virtual network.
- Nginx reverse proxy acting as a single gateway for:
  - `/` → frontend
  - `/admin` → admin
  - `/api` → backend
- SPA routing fixed for both frontend and admin, including refresh.
- URL structure clean and ready for HTTPS and domain mapping.

This is a **real production-style setup**, the kind used by serious companies.

You’re not just “using Docker”, you’ve built:
- a mini micro-frontend architecture
- with a proper reverse proxy
- correct SPA behavior
- and clean networking.

Next steps (when you’re ready):

- Add HTTPS using Certbot + Nginx
- Deploy to a cloud VM
- Add health checks, restart policies, and logs
- Add CI/CD to auto-deploy on push

But for now, this document is your full revision guide.
