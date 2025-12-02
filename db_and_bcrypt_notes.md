# Full Notes (Formatted)

## 🟡 1️⃣ PASSWORD HASHING (bcrypt)

### 🟢 What is hashing?
Hashing = converting a password into an irreversible random string.

### 🟣 bcrypt in Node.js
Install:
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

**Usage**
```js
const hashedPassword = await hashPassword("sanket123");
console.log(hashedPassword);
```

### 🔵 Comparing password (Login)
```js
const isMatch = await bcrypt.compare(enteredPassword, storedHashedPassword);

if (!isMatch) {
    return res.status(400).send("Invalid credentials");
}
```

---

# 🟢 2️⃣ MongoDB (with Mongoose)

MongoDB = NoSQL database (JSON-like documents).  
Mongoose = ODM for MongoDB.

### 🔵 Connecting
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

### 🟢 CRUD Operations

**Create**
```js
await User.create({ name: "Sanket", email: "sanket@gmail.com", age: 22 });
```

**Find All**
```js
const users = await User.find();
```

**Find One**
```js
const user = await User.findOne({ email: "sanket@gmail.com" });
```

**Find by ID**
```js
const user = await User.findById(id);
```

**Update**
```js
await User.findByIdAndUpdate(id, { age: 23 });
```

**Delete**
```js
await User.findByIdAndDelete(id);
```

---

# 🧠 Mongoose Validators (Complete)

### 1️⃣ required
Ensures field must be provided.
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

### 4️⃣ match
```js
email: { type: String, match: /.+\@.+\..+/ }
```

### 5️⃣ enum
```js
role: { type: String, enum: ["user", "admin", "manager"] }
```

---

# 🟣 Population (JOIN-like)

### 🔥 Example: Post → User

**User Schema**
```js
const UserSchema = new mongoose.Schema({
  name: String,
  email: String
});
```

**Post Schema**
```js
const PostSchema = new mongoose.Schema({
  title: String,
  body: String,
  author: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User"
  }
});
```

**Create Post**
```js
const post = await Post.create({
  title: "My first post",
  body: "Hello world",
  author: userId
});
```

**Populate**
```js
const result = await Post.find().populate("author");
```

---

# 🟣 2. Mongoose Middleware Hooks

### 1️⃣ pre-save Hook (Password hashing)
```js
UserSchema.pre("save", async function(next) {
  if (!this.isModified("password")) return next();
  this.password = await bcrypt.hash(this.password, 10);
  next();
});
```

### Case 1: Update without modifying password
Mongoose will NOT hash again.

### Case 2: Update WITH password change
Mongoose will hash again.

---

# 🟦 SQL vs 🟩 NoSQL (When to Choose What)

## 🔥 Use SQL when:
- Need ACID transactions  
- Structured data  
- Complex JOINs  
- Constraints (foreign keys)

Examples:  
Banking, payments, HR systems, order management  

## 🔥 Use NoSQL when:
- Flexible schema  
- Fast development  
- High scalability  
- JSON-based data  

Examples:  
Social media, chat apps, gaming, e-commerce product catalogs  

---

# 🟢 3️⃣ Indexes (MongoDB + SQL)

Indexes = search shortcuts.

### 🧠 Simple Explanation  
Book with index → jump directly  
Book without index → flip every page  

### 🟦 Without Index (MongoDB)
```
User.find({ email: "sanket@gmail.com" })
```
COLLECTION SCAN (slow)

### 🟩 With Index
```js
userSchema.index({ email: 1 });
```

### MUST CREATE INDEXES for:
- email  
- username  
- mobile  
- productId  
- slug  
- createdAt  

---

## ⚠️ Why NOT too many indexes?
- Takes space  
- Slows write operations  
- Bad performance  

Rule:  
**Create indexes ONLY on frequently queried fields.**

---

# 🧠 Indexes in SQL/PostgreSQL

### Create Index
```sql
CREATE INDEX idx_email ON users(email);
```

Postgres creates a B-tree like MongoDB.

---

# 🟣 4️⃣ Aggregations in MongoDB

Aggregation = series of data transformation steps (pipeline).

### Basic Example:
```js
User.aggregate([
  { $match: { age: { $gte: 18 } } },
  { $group: { _id: "$city", totalUsers: { $sum: 1 } } }
]);
```

---

## Most Important Stages
### 1️⃣ $match
```js
{ $match: { age: { $gte: 20 } } }
```

### 2️⃣ $project
```js
{ $project: { name: 1, age: 1, _id: 0 } }
```

### 3️⃣ $group
```js
{
  $group: {
    _id: "$city",
    count: { $sum: 1 },
    avgAge: { $avg: "$age" }
  }
}
```

### 4️⃣ $sort
```js
{ $sort: { age: -1 } }
```

### 5️⃣ $limit
```js
{ $limit: 5 }
```

### 6️⃣ $lookup (JOIN)
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

### 7️⃣ $unwind
Explodes array into multiple docs.

---

# 🔥 Real-World Aggregation Examples

### Count users by city
```js
User.aggregate([{ $group: { _id: "$city", total: { $sum: 1 } } }]);
```

### Top 5 oldest users
```js
User.aggregate([{ $sort: { age: -1 } }, { $limit: 5 }]);
```

### Avg rating of product
```js
Review.aggregate([
  { $match: { productId } },
  { $group: { _id: "$productId", avgRating: { $avg: "$rating" } } }
]);
```

### JOIN users & orders
```js
User.aggregate([
  { $lookup: {
      from: "orders",
      localField: "_id",
      foreignField: "userId",
      as: "orders"
  }}
]);
```

---

# 🟢 5️⃣ TRANSACTIONS – SQL vs MongoDB

## 💡 What is a Transaction?
A group of operations that must ALL succeed or ALL fail.

---

# 🟦 SQL Transactions (Best for banking)

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

Node.js:
```js
await client.query("BEGIN");
await client.query(...);
await client.query("COMMIT");
```

---

# 🟩 MongoDB Transactions
Requires Replica Set.

```js
const session = await mongoose.startSession();
session.startTransaction();

try {
  await User.updateOne(..., { session });
  await session.commitTransaction();
} catch (err) {
  await session.abortTransaction();
}
```

---

# 🥊 SQL vs MongoDB Transactions (Table)

| Feature | SQL | MongoDB |
|--------|------|---------|
| ACID | Full | Supported but heavy |
| Speed | Fast | Slower |
| Best for | Banking | E-commerce |
| Multi-row | Easy | Supported |
| Multi-collection | Easy | Yes (>=4.0) |
| Setup | Simple | Requires Replica Set |

---

# End of Document