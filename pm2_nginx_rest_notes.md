# PM2, NGINX & REST Best Practices — Full Notes (Formatted)

## 🔥 What PM2 Actually Is (Simple)

PM2 = a **process manager** for Node.js.

### PM2 Does:
- Runs Node server in background  
- Auto restarts on crash  
- Server keeps running even if terminal closes  
- Provides logs, monitoring, clustering  

### Without PM2:
```
node server.js
```
❌ Terminal must stay open  
❌ Crash = server offline  

### With PM2:
✔ App becomes a **service**  
✔ Always running  
✔ Auto restarts  
✔ Better logs & monitoring  

---

# 🧠 Why Do We Use PM2? (Real-world reasoning)

### ✔ 1. Auto-Restart (Crash Recovery)
If the server crashes, PM2 restarts instantly.

### ✔ 2. Start Server in Background
PM2 keeps the app running after terminal closes.

### ✔ 3. Log Management
PM2 stores:
- console logs  
- error logs  
- warnings  

Check logs:
```
pm2 logs
```

### ✔ 4. Cluster Mode (Use all CPU cores)
```
pm2 start server.js -i max
```

### ✔ 5. Zero Downtime Deployment
```
pm2 reload server
```

---

# 🟢 Core PM2 Commands

Install:
```
npm install -g pm2
```

Start:
```
pm2 start server.js
```

Restart:
```
pm2 restart server
```

Stop:
```
pm2 stop server
```

Delete:
```
pm2 delete server
```

Logs:
```
pm2 logs
```

---

# 🧩 How PM2 Keeps Server Alive
- Monitors process  
- Restarts on crash  
- Restarts on memory spike  
- Balances CPU usage  

PM2 = watchdog for your Node.js app.

---

# 🧠 Production Deployment Flow (PM2)
```
pm2 start server.js
pm2 save
pm2 startup
```

---

# ⭐ PM2 Summary
PM2 =
- Keeps server alive forever  
- Auto restarts  
- Runs app in background  
- Clustering support  
- Log handling  
- Zero downtime deploy  

---

# 🔥 What NGINX Really Does (Simple Explanation)

NGINX sits **in front** of Node.js.

Client → NGINX → PM2 → Node.js

### NGINX = Bodyguard  
Node.js = Brain  

Node cannot directly face the internet efficiently or safely.

---

# 🧠 Why Use NGINX With Node.js?

### ✔ 1. Reverse Proxy (Front Door)
All traffic → NGINX → Node

### ✔ 2. Serves Static Files FAST
NGINX is optimized in C for:
- images  
- CSS  
- JS  
- HTML  

### ✔ 3. Load Balancing
Example:
```nginx
upstream backend {
    server localhost:5000;
    server localhost:5001;
    server localhost:5002;
}
location / {
    proxy_pass http://backend;
}
```

### ✔ 4. SSL/HTTPS Termination
SSL is NEVER handled by Node.

```nginx
listen 443 ssl;
ssl_certificate /etc/nginx/cert.pem;
ssl_certificate_key /etc/nginx/key.pem;
```

### ✔ 5. Security & Protection
NGINX handles:
- rate limiting  
- timeouts  
- DDoS resistance  
- IP blocking  

### ✔ 6. Zero Downtime Deployment

---

# 🔧 Basic NGINX Reverse Proxy Config
```nginx
server {
    listen 80;

    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

---

# 🧠 Real-Life Flow

1. User hits domain  
2. DNS → Linux server  
3. NGINX listens (80/443)  
4. NGINX → Node (5000)  
5. PM2 keeps Node alive  
6. Node sends response  

---

# ⚙️ Folder Setup in Production
- Node app → `pm2 start index.js`
- NGINX config → `/etc/nginx/sites-available/default`

---

# 🎯 Final Summary

### NGINX =
- Reverse Proxy  
- Load Balancer  
- Static File Server  
- SSL handler  
- Security layer  

### Node =
- Business logic  
- Routing  
- DB queries  

---

# 🟢 5️⃣ Environment Separation (IMPORTANT)

Use separate env files:
```
.env.development
.env.staging
.env.production
```

### Dev:
```
PORT=5000
MONGO_URL=devCluster
JWT_SECRET=dev123
```

### Prod:
```
PORT=8080
MONGO_URL=prodCluster
JWT_SECRET=superSecretProdKey
```

### Scripts:
```json
"scripts": {
  "dev": "NODE_ENV=development nodemon server.js",
  "start": "NODE_ENV=production pm2 start server.js"
}
```

---

# 🟢 REST API Best Practices

---

# ✅ 2. Use Proper HTTP Methods
| Action | Method | Example |
|--------|--------|---------|
| Get all | GET | /users |
| Get one | GET | /users/:id |
| Create | POST | /users |
| Full update | PUT | /users/:id |
| Partial update | PATCH | /users/:id |
| Delete | DELETE | /users/:id |

---

# ✅ 6. Return Proper Status Codes
| Code | Meaning |
|------|---------|
| 200 | OK |
| 201 | Created |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 500 | Server Error |

---

# ✅ 11. Use Pagination (VERY IMPORTANT)

### ❌ Bad  
`/users` → returns 50k users  

### ✔ Good  
`/users?page=1&limit=20`

---

# 🔵 How We Limit Data (Backend Logic)

### Formula:
```
skip = (page - 1) * limit
```

### MongoDB Example
```js
app.get("/users", async (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 20;

  const skip = (page - 1) * limit;

  const users = await User.find().skip(skip).limit(limit);

  res.json({ page, limit, users });
});
```

### Aggregation Example:
```js
User.aggregate([
  { $sort: { createdAt: -1 } },
  { $skip: skip },
  { $limit: limit }
]);
```

---

# 🔥 SQL Example (PostgreSQL)
```sql
SELECT * FROM users
ORDER BY id
LIMIT 20 OFFSET 40;
```

---

# End of Document
