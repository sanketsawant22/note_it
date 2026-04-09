# Rate Limiting in Express.js (Revision Notes)

## What is Rate Limiting?

Rate limiting is a technique used to **control how many requests a user/client can make** to your server in a given time period.

### Example:
- Allow only **5 requests**
- Within **15 minutes**
- If user exceeds limit → return **429 Too Many Requests**

---

# Why do we use Rate Limiting?

Rate limiting is used to:

- Prevent **spam requests**
- Protect APIs from **abuse**
- Reduce **server load**
- Prevent **brute-force attacks**
- Improve **security**
- Control **fair usage**

---

# Common Ways to Implement Rate Limiting in Express

## 1) `express-rate-limit`
Easy and beginner-friendly.

## 2) `rate-limiter-flexible` + Redis
More scalable and production-ready.

---

# Packages Used

## 1. `express`
Used to create the server.

```bash
npm i express
```

### Purpose:
- Creates routes
- Handles requests/responses
- Main backend framework

---

## 2. `dotenv`
Used to load environment variables from `.env`.

```bash
npm i dotenv
```

### Purpose:
- Store sensitive info like Redis URL
- Avoid hardcoding secrets

Example `.env`:
```env
REDIS_URL=your_redis_connection_url
```

---

## 3. `express-rate-limit`
A simple middleware for rate limiting in Express.

```bash
npm i express-rate-limit
```

### Purpose:
- Fast and easy to add rate limiting
- Good for **learning / small apps**
- Not ideal for large production apps

### Limitation:
It stores request counts in **local server memory**, so:

- It resets when server restarts
- It does **not scale well** across multiple servers
- Different servers won’t share the same request count

---

## 4. `ioredis`
Used to connect Node.js app to Redis.

```bash
npm i ioredis
```

### Purpose:
- Connect to local Redis or Redis Cloud
- Store rate limit counters in Redis

---

## 5. `rate-limiter-flexible`
Used for advanced and production-style rate limiting.

```bash
npm i rate-limiter-flexible
```

### Purpose:
- Flexible rate limiting logic
- Can use Redis as shared storage
- Better for real-world projects

---

# Option 1: Basic Rate Limiting with `express-rate-limit`

## Code

```js
import express from "express";
import expressRateLimit from "express-rate-limit";

const app = express();
const PORT = 3000;

const limiter = expressRateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // max 5 requests per IP
  message: "Too many requests from this IP, please try again after 15 minutes",
});

app.use(limiter);

app.get("/", (req, res) => {
  res.send("Hello, world!");
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

---

## How it works

### `windowMs`
```js
windowMs: 15 * 60 * 1000
```
This means:
- 15 minutes window

---

### `max`
```js
max: 5
```
This means:
- A single IP can make only **5 requests** in that 15-minute window

---

### `message`
```js
message: "Too many requests from this IP..."
```
If the user exceeds the limit, this message is returned.

---

# Why `express-rate-limit` is NOT recommended for production?

Because it stores rate limit data in **server memory**.

## Problem:
Suppose your app runs on **3 servers**:

- Server A
- Server B
- Server C

If user makes:
- 5 requests to Server A
- 5 requests to Server B

Then total becomes **10 requests**, but each server thinks only **5 happened locally**.

So rate limiting becomes inconsistent.

---

# Better Approach: Redis-Based Rate Limiting

Redis solves this because it stores request counts in a **shared central store**.

That means:
- all servers check the same Redis data
- rate limiting works consistently
- survives across multiple instances

This is why Redis-based rate limiting is preferred in production.

---

# Your Final Redis-Based Implementation

## Code

```js
import express from "express";
import dotenv from "dotenv";
import Redis from "ioredis";
import { RateLimiterRedis } from "rate-limiter-flexible";

dotenv.config();

const app = express();
const PORT = 3000;

// Redis Cloud connection
const redisClient = new Redis(process.env.REDIS_URL);

const limiter = new RateLimiterRedis({
  storeClient: redisClient,
  points: 5, // 5 requests
  duration: 15 * 60, // per 15 minutes
});

// Helper function to get client key
const getClientKey = (req) => {
  return req.headers["x-forwarded-for"] || req.ip;
};

const rateLimiterMiddleware = async (req, res, next) => {
  try {
    const key = getClientKey(req);

    const result = await limiter.consume(key);

    console.log("User:", key);
    console.log("Remaining:", result.remainingPoints);

    next();
  } catch (err) {
    res.status(429).json({ message: "Too many requests" });
  }
};

app.use(rateLimiterMiddleware);

app.get("/", (req, res) => {
  res.send("Hello, world!");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

---

# Step-by-Step Explanation of Your Code

---

## 1) Import packages

```js
import express from "express";
import dotenv from "dotenv";
import Redis from "ioredis";
import { RateLimiterRedis } from "rate-limiter-flexible";
```

### What each does:
- `express` → creates backend server
- `dotenv` → loads environment variables
- `ioredis` → connects app to Redis
- `RateLimiterRedis` → applies rate limiting using Redis

---

## 2) Load `.env`

```js
dotenv.config();
```

This loads values from `.env` into `process.env`.

Example:
```env
REDIS_URL=your_redis_connection_url
```

---

## 3) Create Express app

```js
const app = express();
const PORT = 3000;
```

---

## 4) Connect Redis

```js
const redisClient = new Redis(process.env.REDIS_URL);
```

This connects your app to Redis Cloud.

If you are using local Redis, you can do:

```js
const redisClient = new Redis({
  host: "localhost",
  port: 6379,
});
```

---

## 5) Create Rate Limiter

```js
const limiter = new RateLimiterRedis({
  storeClient: redisClient,
  points: 5,
  duration: 15 * 60,
});
```

### Meaning:

### `storeClient`
```js
storeClient: redisClient
```
Tells limiter to use Redis for storing request counts.

---

### `points`
```js
points: 5
```
User gets **5 points**.

Each request consumes **1 point**.

So user can make only **5 requests**.

---

### `duration`
```js
duration: 15 * 60
```
Time window = **15 minutes**

(Important: here duration is in **seconds**, not milliseconds)

So:
- 5 requests allowed
- every 15 minutes

---

# Why did you create `getClientKey()`?

```js
const getClientKey = (req) => {
  return req.headers["x-forwarded-for"] || req.ip;
};
```

This is a **very useful trick**, especially during testing.

---

## Why not just use `req.ip`?

Because during local development:
- all requests often come from same IP (`::1` or localhost)

So if you want to simulate **different users**, you can manually pass:

```bash
x-forwarded-for: user1
x-forwarded-for: user2
x-forwarded-for: user3
```

Then your limiter treats them like different clients.

---

# Middleware Logic

## Code

```js
const rateLimiterMiddleware = async (req, res, next) => {
  try {
    const key = getClientKey(req);

    const result = await limiter.consume(key);

    console.log("User:", key);
    console.log("Remaining:", result.remainingPoints);

    next();
  } catch (err) {
    res.status(429).json({ message: "Too many requests" });
  }
};
```

---

# What happens here?

---

## Step 1: Get client identity

```js
const key = getClientKey(req);
```

This identifies who is making the request.

Example:
- `user1`
- `user2`
- actual IP

---

## Step 2: Consume 1 request point

```js
const result = await limiter.consume(key);
```

This means:

> “This user made one request, reduce their remaining points by 1.”

If user still has points left:
- request is allowed

If user has no points left:
- error is thrown
- goes to `catch`

---

## Step 3: Log remaining requests

```js
console.log("User:", key);
console.log("Remaining:", result.remainingPoints);
```

This helps in debugging.

Example output:
```bash
User: user1
Remaining: 4
```

---

## Step 4: Allow request to continue

```js
next();
```

If user is under the limit, Express moves to next middleware/route.

---

## Step 5: Block excessive requests

```js
res.status(429).json({ message: "Too many requests" });
```

If request limit is exceeded, return:

- HTTP Status: `429`
- JSON response:
```json
{
  "message": "Too many requests"
}
```

---

# Apply Middleware Globally

```js
app.use(rateLimiterMiddleware);
```

This means **all routes** after this line are protected.

So:

```js
app.get("/", ...)
```

will also be rate-limited.

---

# Test Route

```js
app.get("/", (req, res) => {
  res.send("Hello, world!");
});
```

This is just a sample endpoint.

---

# Start Server

```js
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

---

# How to Test This

## Start server

```bash
node index.js
```

or if using nodemon:

```bash
nodemon index.js
```

---

# Test in Browser

Open:
```bash
http://localhost:3000
```

Refresh more than 5 times quickly.

After limit is crossed, you should get:

```json
{
  "message": "Too many requests"
}
```

---

# Better Testing with Different Users

Since you added `x-forwarded-for`, you can simulate multiple users.

---

## PowerShell (Windows)

### User 1
```powershell
curl -Headers @{"x-forwarded-for"="user1"} http://localhost:3000
```

### User 2
```powershell
curl -Headers @{"x-forwarded-for"="user2"} http://localhost:3000
```

### User 3
```powershell
curl -Headers @{"x-forwarded-for"="user3"} http://localhost:3000
```

Each one gets separate rate limits.

---

## CMD (using curl.exe)

```bash
curl.exe -H "x-forwarded-for: user1" http://localhost:3000
curl.exe -H "x-forwarded-for: user2" http://localhost:3000
```

---

# Real Production Note

In production, `x-forwarded-for` is commonly set by:

- Nginx
- Load balancer
- Reverse proxy
- Cloudflare

So this is actually very relevant.

But:

## Important Security Note
In production, **do not blindly trust client-provided headers** unless your proxy/load balancer is configured properly.

Because users can fake headers.

A safer production setup usually involves:
- trusting proxy properly in Express
- using real forwarded IP from trusted infra

Example:

```js
app.set("trust proxy", 1);
```

Then `req.ip` becomes more reliable behind proxies.

---

# Best Practice Version (Recommended Improvement)

You can improve your code like this:

```js
import express from "express";
import dotenv from "dotenv";
import Redis from "ioredis";
import { RateLimiterRedis } from "rate-limiter-flexible";

dotenv.config();

const app = express();
const PORT = 3000;

app.set("trust proxy", 1);

const redisClient = new Redis(process.env.REDIS_URL);

const limiter = new RateLimiterRedis({
  storeClient: redisClient,
  points: 5,
  duration: 15 * 60,
});

const rateLimiterMiddleware = async (req, res, next) => {
  try {
    const key = req.ip;

    const result = await limiter.consume(key);

    console.log("User:", key);
    console.log("Remaining:", result.remainingPoints);

    next();
  } catch (err) {
    res.status(429).json({
      success: false,
      message: "Too many requests. Please try again later.",
    });
  }
};

app.use(rateLimiterMiddleware);

app.get("/", (req, res) => {
  res.send("Hello, world!");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

---

# Difference Between Your Version and Recommended Version

## Your version
```js
const getClientKey = (req) => {
  return req.headers["x-forwarded-for"] || req.ip;
};
```

### Good for:
- learning
- local testing
- simulating multiple users

---

## Recommended production version
```js
app.set("trust proxy", 1);
const key = req.ip;
```

### Better for:
- deployed apps
- reverse proxies
- real production use

---

# Interview-Level Understanding

If someone asks:

## "Why use Redis for rate limiting?"

You can answer:

> Redis stores request counts in a shared place, so rate limiting works consistently even when the app runs on multiple servers or instances. In-memory rate limiting does not scale because each server tracks requests separately.

---

## "Why not just use express-rate-limit?"

You can answer:

> `express-rate-limit` is simple and useful for small apps or learning, but it usually stores data in memory, which is not ideal for scalable production systems.

---

## "What does `limiter.consume(key)` do?"

You can answer:

> It deducts one point from the client’s available request quota. If the client has exceeded the allowed requests, it throws an error.

---

# Quick Revision Summary

## `express-rate-limit`
- Easy
- beginner-friendly
- stores data in memory
- not ideal for scale

## `rate-limiter-flexible + Redis`
- scalable
- production-style
- shared storage
- better for multiple servers

## Important concepts
- `points` = max requests
- `duration` = time window
- `consume(key)` = reduce available requests by 1
- `429` = Too Many Requests

---

# Final Recommended Install Command

```bash
npm i express dotenv ioredis rate-limiter-flexible
```

If you want to test basic version also:

```bash
npm i express-rate-limit
```

---

# Folder Example

```bash
project/
│
├── index.js
├── .env
├── package.json
└── notes/
    └── rate-limiting-notes.md
```

---

# Final Takeaway

If you are:
- learning → `express-rate-limit` is okay
- building real backend → use **Redis-based rate limiting**

Your current Redis implementation is **much closer to how actual backend systems are built**.

That means you are not just “using a package” —
you are understanding **how backend protection is done in real projects**.
