# Full Notes (Formatted)

## Send token in HTTP-Only Cookie

**Example:**
```js
res
  .cookie("token", jwtToken, {
    httpOnly: true,
    secure: true,
    sameSite: "strict",
  })
  .json({
    message: "User registered",
    user: userData
  })
```

### 👍 Pros:
- Most secure method  
- Cookie can't be accessed by JS → safe from XSS  
- Perfect for production-grade apps (banking, enterprise)

### 👎 Cons:
- Slightly more complex  
- Need correct settings (CORS, proxy, sameSite)

---

## ✅ 1. How does frontend know the user has a token?
Frontend **cannot read** the cookie.  
But the cookie is **automatically sent** with every request.

Frontend simply calls:

```js
fetch("/api/auth/verify", {
  method: "GET",
  credentials: "include"
});
```

Backend checks token inside cookie → returns user data.

---

## ✅ 2. How to make the next API call?

```js
fetch("/api/user/profile", {
  method: "GET",
  credentials: "include"
});
```

---

## 🛡 3. Backend auth middleware

```js
export const authMiddleware = (req, res, next) => {
    const token = req.cookies.token;

    if(!token) return res.status(401).json({ message: "Unauthorized" });

    try {
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        req.user = decoded;
        next();
    } catch (error) {
        return res.status(401).json({ message: "Invalid token" });
    }
};
```

---

## Cookie Flags Explanation

### 🔥 httpOnly: true  
JS cannot access this cookie → protects from XSS.

### 🔥 secure: true  
Requires **HTTPS**. Prevents MITM attacks.

### 🔥 sameSite: "strict"  
Cookie is sent ONLY for requests coming from the same domain → protects from CSRF attacks.

---

# 🟢 1. Middleware (Express)

### ⭐ What is Middleware?
A function that runs **in the middle** of request–response cycle.  
It can read, modify, or block the request.

### Structure:
```js
(req, res, next) => {
    // do something
    next();
}
```

---

## 🟢 Types of Middleware

### 1️⃣ Application-level middleware
Runs for EVERY request.

```js
app.use((req, res, next) => {
    console.log("Request URL:", req.url);
    next();
});
```

### 2️⃣ Route-level middleware
Runs only on specific routes.

```js
app.get('/profile', authMiddleware, (req, res) => {
    res.send("User Profile");
});
```

### 3️⃣ Built-in middleware
`express.json()`, `express.urlencoded()`

### 4️⃣ Third-party middleware
Examples: `cors`, `morgan`, `helmet`

---

## 🎯 Why Middleware?
- Authentication
- Logging
- Input validation
- Sanitizing inputs
- Rate limiting
- Parsing body
- File upload (`multer`)

---

# 🟢 2. Routes in Express

### 💡 What is a Route?
An endpoint where backend listens for requests.

### Basic Syntax:
```
app.METHOD(PATH, HANDLER)
```

---

## Route Examples

### 🟢 GET
```js
app.get("/users", (req, res) => {
    res.send("All Users");
});
```

### 🟠 POST
```js
app.post("/login", (req, res) => {
    console.log(req.body);
    res.send("Login successful");
});
```

### 🔵 PUT
```js
app.put("/user/:id", (req, res) => {
    res.send("Updated user " + req.params.id);
});
```

### 🟣 PATCH
```js
app.patch("/user/:id", (req, res) => {
    res.send("Partially updated user " + req.params.id);
});
```

### 🔴 DELETE
```js
app.delete("/user/:id", (req, res) => {
    res.send("Deleted user " + req.params.id);
});
```

---

## Important Route Properties

### 🔸 req.params
```js
app.get("/user/:id", (req, res) => {
    console.log(req.params.id);
});
```

### 🔸 req.query
```js
app.get("/search", (req, res) => {
    console.log(req.query.keyword);
});
```

### 🔸 req.body
```js
app.post("/register", (req, res) => {
    console.log(req.body);
});
```

---

## Frontend Call Examples

### 🟢 Sending params
```js
fetch(`/user/${encodeURIComponent("Sanket Patel")}`);
```

### 🟡 Sending query
```js
fetch(`/search?${new URLSearchParams({ keyword: "c++ basics", page: 2 })}`);
```

---

# 🟢 3. Controllers in Express

### What is a Controller?
A function containing logic for a route.

---

## Example (Clean Structure)

### Controller (user.controller.js)
```js
exports.getUsers = (req, res) => {
    res.send("All Users from Controller");
};
```

### Router (user.routes.js)
```js
router.get("/", getUsers);
```

### Main File
```js
app.use("/users", require("./user.routes"));
```

---

# 🟢 4. Request/Response Lifecycle

### Diagram:

```
Request
  ↓
Global Middleware (json, cors)
  ↓
Route-level Middleware (auth)
  ↓
Router (matches URL)
  ↓
Controller (logic)
  ↓
Response
```

---

# 🟢 5. Express Router (Modular Structure)

### Example
```
routes/
  user.routes.js
controllers/
  user.controller.js
```

### Router Example
```js
router.get("/", getAllUsers);
router.post("/", createUser);
```

---

## Router + Middleware Example

```js
router.use(auth);

router.get("/dashboard", (req, res) => res.send("Admin dashboard"));
router.get("/users", (req, res) => res.send("Admin users"));
```

---

# 🟢 6. CORS

```js
app.use(cors({
    origin: "http://localhost:3000",
    methods: ["GET", "POST", "PUT"],
    allowedHeaders: ["Content-Type", "Authorization"],
    credentials: true
}));
```

---

# 🟢 7. Environment Variables (.env)

### Why?
Store secrets safely.

### Example:
```
PORT=5000
MONGO_URL=mongodb+srv://username:password@cluster/db
JWT_SECRET=mySuperSecretKey
```

Load using:

```js
require("dotenv").config();
```

---

# End of Document
