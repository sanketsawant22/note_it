# 📘 Database Integration Notes (MongoDB, MySQL, PostgreSQL)

#️⃣ 1. Databases Overview

## 🟢 MongoDB (NoSQL)
- Document-based
- Flexible schema
- Uses BSON (JSON-like)
- ORM = Mongoose

## 🟠 MySQL (SQL)
- Relational database
- Strict schemas
- Best for structured data
- ORM = Prisma, Sequelize
- Raw driver = mysql2

## 🔵 PostgreSQL (SQL)
- Most powerful relational DB
- Supports JSON, arrays, functions
- ORM = Prisma, Sequelize
- Raw driver = pg

---

#️⃣ 2. When To Use What

| Scenario | Best Choice |
|---------|-------------|
| Unstructured/JSON-heavy data | MongoDB |
| Complex relationships | PostgreSQL |
| Banking/finance/strict schema | MySQL/PostgreSQL |
| Build fast without SQL | Prisma (PostgreSQL preferred) |
| Need SQL control / high performance | Raw SQL |
| Prototyping | MongoDB + Mongoose |

---

#️⃣ 3. Backend Connection Examples

## 🟢 MongoDB + Mongoose
```js
mongoose.connect("mongodb://127.0.0.1:27017/mydb");
```

## 🔵 PostgreSQL + Prisma
```prisma
datasource db {
  provider = "postgresql"
  url      = "postgresql://postgres:1234@localhost:5432/mydb"
}
```

## 🟠 MySQL + Prisma
```prisma
datasource db {
  provider = "mysql"
  url      = "mysql://root:@localhost:3306/mydb"
}
```

---

#️⃣ 4. Defining Models

## 🟢 MongoDB (Mongoose Schema)
```js
const UserSchema = new mongoose.Schema({
  name: String,
  age: Number,
});
```

## 🔵 SQL (Prisma Model)
```prisma
model User {
  id   Int    @id @default(autoincrement())
  name String
  age  Int
}
```

---

#️⃣ 5. CRUD Comparison (SUPER IMPORTANT)

## 🟢 MongoDB (Mongoose)
### Create
```js
await User.create({ name: "Sanket", age: 18 });
```

### Read
```js
await User.find();
```

### Update
```js
await User.findByIdAndUpdate(id, { name: "New" }, { new: true });
```

### Delete
```js
await User.findByIdAndDelete(id);
```

---

## 🔵 SQL (Prisma — MySQL + PostgreSQL SAME CODE)

### Create
```js
await prisma.user.create({ data: { name: "Sanket", age: 18 } });
```

### Read
```js
await prisma.user.findMany();
```

### Update
```js
await prisma.user.update({
  where: { id },
  data: { name: "New" }
});
```

### Delete
```js
await prisma.user.delete({ where: { id } });
```

---

## 🟣 Raw SQL (PostgreSQL - pg driver)

### Create
```js
await client.query(
  "INSERT INTO users (name, age) VALUES ($1, $2)", 
  ["Sanket", 18]
);
```

### Read
```js
const result = await client.query("SELECT * FROM users");
```

### Update
```js
await client.query(
  "UPDATE users SET name=$1 WHERE id=$2",
  ["New", id]
);
```

### Delete
```js
await client.query("DELETE FROM users WHERE id=$1", [id]);
```

---

## 🟠 Raw SQL (MySQL - mysql2)
```js
await db.execute(
  "INSERT INTO users (name, age) VALUES (?, ?)",
  ["Sanket", 18]
);
```

---

#️⃣ 6. Prisma Raw SQL (Advanced But Useful)

### Query
```js
await prisma.$queryRaw`SELECT * FROM User WHERE age > ${18}`;
```

### Execute (INSERT/UPDATE/DELETE)
```js
await prisma.$executeRaw`DELETE FROM User WHERE id = ${id}`;
```

---

#️⃣ 7. Express API Routes (Universal Blueprint)
```js
router.post("/", async (req, res) => {
  const user = await <DB>.create({ ... });
  res.json(user);
});
```

---

#️⃣ 8. Differences Between MongoDB & SQL
| Feature | MongoDB | SQL |
| Schema | Flexible | Strict |
| Relationships | Weak | Strong |
| Queries | JS-like | SQL-based |

---

#️⃣ 9. Prisma vs Mongoose vs Raw SQL
- Mongoose: Easy, no SQL
- Prisma: Best DX, strict schema
- Raw SQL: Full control, requires SQL
