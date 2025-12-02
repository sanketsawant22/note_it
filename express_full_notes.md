# Sending Token in HTTP-Only Cookie

## Example
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

## 👍 Pros
- Most secure method  
- Cookie can't be accessed by JS → safe from XSS  
- Perfect for production-grade apps

## 👎 Cons
- Slightly more complex  
- Need correct settings (CORS, proxy, sameSite)

---

# 1. How frontend knows user has a token
Frontend can't read the cookie but it is sent automatically:

```js
fetch("/api/auth/verify", {
  method: "GET",
  credentials: "include"
});
```

---

# 2. Next API call
```js
fetch("/api/user/profile", {
  method: "GET",
  credentials: "include"
});
```

---

# 3. Auth Middleware
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

# Cookie Flags

### httpOnly: true  
Protects from XSS.

### secure: true  
Only over HTTPS.

### sameSite: "strict"  
Prevents CSRF.

---

# Middleware

Middleware = function that runs between request → response.

```js
(req, res, next) => {
  next();
}
```

Types:
- Application-level
- Route-level
- Built-in
- Third-party

Uses:
- Auth
- Logging
- Validation
- Sanitization
- Rate limiting
- File upload

---

# Routes in Express

```js
app.get("/users", (req, res) => {
    res.send("All Users");
});
```

Types: GET, POST, PUT, PATCH, DELETE

---

# Controllers

```js
exports.getUsers = (req, res) => {
    res.send("All Users");
};
```

---

# Request Lifecycle
```
Client → Middleware → Router → Controller → Response
```

---

# Express Router Structure
```
routes/
controllers/
middlewares/
```

---

# CORS
```js
app.use(cors({
    origin: "http://localhost:3000",
    credentials: true
}));
```

---

# Environment Variables
```
PORT=5000
MONGO_URL=
JWT_SECRET=
```

Load:
```js
require("dotenv").config();
```
