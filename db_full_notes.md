# Password Hashing, MongoDB, Validators, Indexes, Aggregations, Transactions

## 🟡 1️⃣ PASSWORD HASHING (bcrypt)

### 🔹 What is hashing?
Hashing = converting a password into an irreversible random string.

### 🔹 Install bcrypt
```
npm install bcrypt
```

### 🔥 Hashing a password (Signup)
```js
const bcrypt = require("bcrypt");

const hashPassword = async (password) => {
    const saltRounds = 10;
    const hashed = await bcrypt.hash(password, saltRounds);
    return hashed;
};
```

### 🔵 Compare password (Login)
```js
const isMatch = await bcrypt.compare(enteredPassword, storedHashedPassword);

if (!isMatch) {
    return res.status(400).send("Invalid credentials");
}
```

---

# 🟢 2️⃣ MongoDB (with Mongoose)

MongoDB = NoSQL (JSON-like docs)  
Mongoose = ODM that interacts with MongoDB.

### 🔵 Connect Mongoose
```js
mongoose.connect(process.env.MONGO_URL)
  .then(() => console.log("DB connected"))
  .catch(err => console.log(err));
```

### 🟣 Schema
```js
const userSchema = new mongoose.Schema({
  name: String,
  email: { type: String, required: true, unique: true },
  age: Number
});
```

### 🟡 Model
```js
const User = mongoose.model("User", userSchema);
```

### CRUD Examples
Create:
```js
await User.create({ name: "Sanket", email: "s@gmail.com", age: 22 });
```

Find all:
```js
await User.find();
```

Find one:
```js
await User.findOne({ email: "s@gmail.com" });
```

Update:
```js
await User.findByIdAndUpdate(id, { age: 23 });
```

Delete:
```js
await User.findByIdAndDelete(id);
```

---

# 🧠 Mongoose Validators

### 1️⃣ required
```js
email: { type: String, required: true }
```

### 2️⃣ minLength
```js
password: { type: String, minLength: 6 }
```

### 3️⃣ maxLength
```js
username: { type: String, maxLength: 15 }
```

### 4️⃣ match (regex)
```js
email: { type: String, match: /.+\@.+\..+/ }
```

### 5️⃣ enum
```js
role: { type: String, enum: ["user", "admin", "manager"] }
```

---

# 🔥 Population (JOINS in MongoDB)

### User Schema
```js
const UserSchema = new mongoose.Schema({
  name: String,
  email: String
});
```

### Post Schema
```js
const PostSchema = new mongoose.Schema({
  title: String,
  body: String,
  author: { type: mongoose.Schema.Types.ObjectId, ref: "User" }
});
```

### Populate
```js
const posts = await Post.find().populate("author");
```

---

# 🟣 Middleware Hooks (pre-save)

### pre-save hook for password hashing
```js
UserSchema.pre("save", async function(next) {
  if (!this.isModified("password")) return next();
  this.password = await bcrypt.hash(this.password, 10);
  next();
});
```

---

# 🟩 SQL vs NoSQL (When to choose what)

### SQL (Choose when)
- ACID required
- Structured data
- Complex joins  
Examples: Banking, Payments, Employee mgmt, Ecommerce order flow

### NoSQL (Choose when)
- Flexible schema
- JSON-based
- High scalability  
Examples: Social media, Chat apps, Gaming, Product catalogs

---

# 🟢 3️⃣ Indexes (MongoDB + SQL)

Indexes = shortcuts for faster search.

### Without index → full scan  
### With index → instant lookup

### Create index (MongoDB)
```js
userSchema.index({ email: 1 });
```

### When to index:
- email
- username
- mobile
- productId
- slug
- createdAt

### Drawbacks:
- Takes disk space
- Slows inserts/updates
- Too many indexes = slower DB

---

# 🧠 SQL Index
```sql
CREATE INDEX idx_email ON users(email);
```

Works like Mongo index.

---

# 🟣 4️⃣ Aggregations (MongoDB)

Aggregation = pipeline of steps for grouping, filtering, sorting etc.

### Example
```js
User.aggregate([
  { $match: { age: { $gte: 18 } } },
  { $group: { _id: "$city", totalUsers: { $sum: 1 } } }
]);
```

### Important Pipeline Stages
1. `$match`
2. `$project`
3. `$group`
4. `$sort`
5. `$limit`
6. `$lookup` (JOIN)
7. `$unwind`

### JOIN Example
```js
{
  $lookup: {
    from: "orders",
    localField: "_id",
    foreignField: "userId",
    as: "orders"
  }
}
```

---

# 🟢 5️⃣ TRANSACTIONS – SQL vs MongoDB

Transaction = all steps succeed OR all fail.

### ⭐ SQL Transactions
```sql
BEGIN;
UPDATE accounts ...;
UPDATE accounts ...;
COMMIT;
```

### ⭐ MongoDB Transactions
```js
const session = await mongoose.startSession();
session.startTransaction();
await User.updateOne(..., { session });
await session.commitTransaction();
```

### Comparison

| Feature | SQL | MongoDB |
|--------|------|---------|
| ACID | Full | Supported but heavier |
| Speed | Fast | Slower multi-doc |
| Best for | Banking | Apps/Ecommerce |
| Setup | Simple | Requires Replica Set |

