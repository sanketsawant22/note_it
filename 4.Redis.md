
---

# Redis Cheat Sheet

## 🔥 Running Redis in Docker

```bash
docker exec -it <redis-container-id> bash
```

Start Redis CLI:

```bash
redis-cli
```

Verify Redis is running:

```bash
ping
# Output: PONG
```

---

## 🔑 Basic Commands

### Set a key

```bash
SET name sanket
```

### Get a key

```bash
GET name
```

### Best-practice key naming

```bash
SET user:1 sanket
SET user:2 ujwal
```

### Conditional set

```bash
SET user:1 sanket NX
# Only sets if key doesn't exist
```

---

## 🔄 Multiple Key Operations

### Get multiple values

```bash
MGET user:1 user:2 msg:1
```

### Set multiple key-value pairs

```bash
MSET user:1 sanket user:2 ujwal
```

---

## 🔢 Counters

```bash
SET count 1
INCR count         # 2
INCRBY count 10    # 12
```

---

## ⏳ Expiry and TTL

### Set expiry on a key

```bash
EXPIRE user:1 10   # expires in 10 seconds
```

### Get remaining TTL

```bash
TTL user:1
```

Why expiry matters:

* Prevent stale data
* Keeps cache fresh
* Controls memory usage

---

## 🧑‍💻 Using Redis in Node.js

Install:

```bash
npm install ioredis
```

### client.js

```js
const Redis = require("ioredis");
const client = new Redis();

module.exports = client;
```

### Usage

```js
import client from "./client.js";

client.get("user:1");
client.expire("user:1", 10);
```

---

# 📚 Redis Lists

Lists support both stack & queue behavior.

---

## ➕ Inserting

### Left push

```bash
LPUSH messages "sanket"
LPUSH messages "hello"
```

List becomes:

```
hello
sanket
```

### Right push

```bash
RPUSH messages "sawant"
```

List becomes:

```
hello
sanket
sawant
```

---

## ➖ Removing

### Pop from left

```bash
LPOP messages
```

### Pop from right

```bash
RPOP messages
```

---

## 📏 List Info

```bash
LLEN messages
```

View all elements:

```bash
LRANGE messages 0 -1
```

---

## 📥 Blocking Operations

### BLPOP

```bash
BLPOP messages 10
```

* waits for 10 seconds
* pops element if available
* otherwise returns nil after timeout

Useful for:

* job queues
* realtime processing

---

## ✂️ List Editing

### LTRIM (keep a range)

```bash
LTRIM messages 0 10
```

### LMOVE

```bash
LMOVE list1 list2 LEFT RIGHT
```

Moves element accordingly.

---

## ❌ Delete a key

```bash
DEL messages
```

---

# ✅ Quick Summary Table

| Command            | Description  |
| ------------------ | ------------ |
| `SET key value`    | Set key      |
| `GET key`          | Get value    |
| `MSET k1 v1 k2 v2` | Multi-set    |
| `MGET k1 k2`       | Multi-get    |
| `EXPIRE key sec`   | Set expiry   |
| `TTL key`          | Check expiry |
| `INCR key`         | Increment    |
| `LPUSH key val`    | Push left    |
| `RPUSH key val`    | Push right   |
| `LPOP key`         | Pop left     |
| `LRANGE key 0 -1`  | Get list     |

---

# ✅ Redis Use Cases

* Caching
* Rate limiting
* Sessions
* Message queues
* Counters
* Leaderboards
* Pub/Sub

---

If you want, I can also create:

✅ ASCII flow diagrams
✅ A printable one-page cheat-sheet
✅ Extended version with Hashes / Sets / Sorted Sets / Streams

Just say the word!
