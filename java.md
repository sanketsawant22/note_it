
# How Java Works

Java follows a flow where the source code written by the developer is first compiled into **bytecode**, and then that bytecode is executed by the **JVM**.

## Basic Flow

Developer → Java Code (.java) → Compiler (javac) → Bytecode (.class) → JVM → Output

## Important Terms

### JVM (Java Virtual Machine)

* JVM stands for **Java Virtual Machine**.
* It is responsible for **executing Java bytecode**.
* JVM does **not understand normal human-readable Java code directly**.
* It only understands **bytecode**.

### JRE (Java Runtime Environment)

* JRE stands for **Java Runtime Environment**.
* It provides the **runtime environment** needed to run Java applications.
* It contains:

  * **JVM**
  * **Libraries**
  * Other files required to run Java programs

### JDK (Java Development Kit)

* JDK stands for **Java Development Kit**.
* It is used to **develop, compile, and run** Java programs.
* It contains:

  * **JRE**
  * Development tools like **javac**
* If you want to **write Java code**, you need **JDK**.

<img width="1651" height="875" alt="image" src="https://github.com/user-attachments/assets/779a2ce5-d435-4d85-b104-88e3b277ab1a" />

# Datatypes in java

## Premitive

* Integer
  * byte 
  * short
  * int
  * long
  
* Float
  * float
  * double
  
By default, decimal values in Java are treated as double.
If you want to store a decimal number in a float, you must add f at the end.
  
  ```java
  double a = 23.25;
  float b = 23.25f;
  ```
  
* Character
  * char
  
* boolean

# Type conversion and Type casting in java

## 1. Type Conversion
If we assign a **smaller datatype to a bigger datatype**, it is called **type conversion**.

This happens **automatically** because there is no risk of data loss.

### Example:
```java
byte a = 120;
int b;

b = a;   // conversion 
```

## 2. Type Casting

If we assign a bigger datatype to a smaller datatype, it is called type casting.

This does not happen automatically because there may be data loss.
So, we must explicitly mention the datatype in brackets.

### Example:
```java
int b = 12;
byte a;

a = (byte) b;   // casting
```
### note
during casting if value is larger than types capacity then it will do modulo of that value with its range and store result 

  ```java
  int a = 257;
  byte b = (byte) a;

  // b has range of -128 to 127
  // total of 256 
  // so it will store 257 % 256 = 1
  ```


  # Object in Java

An object in Java is an **instance of a class**.

It is a real entity that has:

- **properties / data** → called **attributes**
- **behavior / actions** → called **methods**

A class is a blueprint, and an object is created from that blueprint.

## Example
```java
class Student {
    String name = "Sanket";
    int age = 20;
}

public class Main {
    public static void main(String[] args) {
        Student s1 = new Student();   // object
        System.out.println(s1.name);
        System.out.println(s1.age);
    }
}
```

# Strings in Java

### What is a String?

* In Java, a **String is a class**, not a primitive data type like `int`, `char`, or `float`.
* It is part of the `java.lang` package.

---

### Example

```java
String s1 = "sanket";
String s2 = "sanket";
```

* Here, **only one object** is created in memory.
* Both `s1` and `s2` refer to the **same object**.

---

### Memory Concept (Very Important 🚀)

Java uses:

* **Heap Memory** → stores objects
* **Stack Memory** → stores references (addresses)

Inside Heap, there is a special area called:

**String Constant Pool (SCP)**

---

### How String Pool Works

* When a string is created using quotes (`" "`):

  1. JVM checks if the string already exists in the **String Constant Pool**.
  2. If it exists → reuse the same object.
  3. If not → create a new object in the pool.

---

### Example Explained

```java
String s1 = "sanket";
String s2 = "sanket";
```

* JVM creates **only one object** `"sanket"` in SCP.
* Suppose its address is `105`.

| Variable | Stored in Stack | Points to |
| -------- | --------------- | --------- |
| s1       | 105             | "sanket"  |
| s2       | 105             | "sanket"  |

✅ Both variables point to the **same memory location**

---

### Immutability of Strings

Strings in Java are **immutable** (cannot be changed).

```java
String s1 = "sanket";
s1 = "sanket sawant";
```

What happens here?

* `"sanket"` remains unchanged in the pool.
* A **new object** `"sanket sawant"` is created.
* `s1` now points to the new object.

---

### Important Points

✔ Strings are stored in **String Constant Pool**
✔ Duplicate strings share the **same memory**
✔ Strings are **immutable**
✔ New value = new object (old one stays until garbage collected)

---

# `"abc"` vs `new String("abc")` in Java

## 1. Using String Literal

```java
String s1 = "abc";
```

### What happens:

* JVM checks **String Constant Pool (SCP)**
* If `"abc"` exists → reuse it
* If not → create it in SCP

Only **one object in SCP**

---

## 2. Using `new` Keyword

```java
String s2 = new String("abc");
```

### What happens:

* JVM **creates object in Heap (outside SCP)**
* ALSO ensures `"abc"` exists in SCP

So here, **two objects are created**:

1. One in **String Constant Pool**
2. One in **Heap**

---

## Memory Visualization

```java
String s1 = "abc";
String s2 = new String("abc");
```

| Variable | Points to | Location |
| -------- | --------- | -------- |
| s1       | "abc"     | SCP      |
| s2       | new obj   | Heap     |

---

## 3. Comparison (VERY IMPORTANT ⚠️)

```java
System.out.println(s1 == s2);        // ?
System.out.println(s1.equals(s2));  // ?
```

### Output:

```java
false
true
```

---

### Why?

#### `==` (Reference Comparison)

* Checks **memory address**
* `s1` → SCP
* `s2` → Heap

👉 Different addresses → `false`

---

#### `.equals()` (Value Comparison)

* Checks **actual content**

👉 `"abc"` == `"abc"` → `true`

---

## 4. How to Make Them Same?

```java
String s3 = new String("abc").intern();
```

* `intern()` forces string into **SCP**

```java
System.out.println(s1 == s3); // true
```

---

## One-Line Answer

> `"abc"` uses String Constant Pool and reuses memory, while `new String("abc")` creates a new object in heap even if the value already exists.`

---

# 🔥 String vs StringBuilder vs StringBuffer

---

## 1. String (Immutable ❌ change not allowed)

```java
String s = "hello";
s = s + " world";
```

### What happens:

* `"hello"` → stays unchanged
* New object `"hello world"` is created
* Old object becomes garbage

👉 **Every modification = new object → slow ❌**

---

## 2. StringBuilder (Mutable ✅ fast)

```java
StringBuilder sb = new StringBuilder("hello");
sb.append(" world");
```

### What happens:

* Same object is modified
* No new object created

👉 **Fastest option 🚀**

---

## 3. StringBuffer (Mutable + Thread Safe 🔒)

```java
StringBuffer sbf = new StringBuffer("hello");
sbf.append(" world");
```

### What happens:

* Same as StringBuilder
* BUT methods are synchronized (thread-safe)
* Has buffer space of 16

👉 **Safe but slower than StringBuilder**

---

# Interview Trick Question ⚠️

```java
String s = "a";
s.concat("b");
System.out.println(s);
```

### Output:

```
a
```

👉 Because **String is immutable**, `concat()` does NOT modify original string.

---

# 🔥 Correct Way

```java
s = s.concat("b");
```
---

# 🔐 What does “Thread Safe” mean?

👉 **Thread safe = multiple threads can use the same object safely without causing issues**

### 💡 Simple Example

Imagine:

* 2 threads trying to update the same string at the same time

Without safety:

```text
Thread 1 → writing "hello"
Thread 2 → writing "world"
```

👉 Output might become:

```
heworldlo   ❌ (corrupted data)
```

---

### ✅ Thread Safe Behavior

* Only **one thread can access the object at a time**
* Others must wait

👉 This is done using **synchronization (locking)**

---

# 📘 Variables & `static` Keyword in Java

---

## 🔹 Types of Variables

### 1. Instance Variables

* Declared **inside a class but outside methods**
* Belong to **object (instance)**

```java id="d8nt0y"
class User {
    String name; // instance variable
}
```

👉 Each object gets its **own copy**

---

### 2. Local Variables

* Declared **inside methods / constructors / blocks**

```java id="j9cy9a"
void display() {
    int age = 20; // local variable
}
```

👉 Only accessible **within that method**

---

# 🔥 `static` Keyword in Java

---

## 🔹 What is `static`?

👉 `static` means **belongs to the class, not objects**

---

## 🔹 Static Variable (Class Variable)

```java id="e4n0tr"
class User {
    static String name;
}
```

### 💡 Key Idea:

* Only **one copy** is created (shared across all objects)

---

## 🔹 Example

```java id="25drf5"
class User {
    static String name;
}

public class Main {
    public static void main(String[] args) {
        User obj1 = new User();
        User obj2 = new User();

        obj1.name = "Sanket";

        System.out.println(obj2.name); // ?
    }
}
```

### ✅ Output:

```id="s6x4v0"
Sanket
```

👉 Because `name` is **shared (static)**

---

# 📘 Static Method in Java

```java
class User {
    public static void greet(String name){
        System.out.println("Hello " + name);
    }
}
```
---

## 🔹 What is a Static Method?

👉 A method that **belongs to the class, not to objects**

* You **don’t need to create an object** to call it

---

## 🔹 Calling Static Method

### ✅ Using Class Name (Best Practice)

```java
User.greet("Sanket");
```

---

### ❌ Using Object (Allowed but not recommended)

```java
User obj = new User();
obj.greet("Sanket");
```

---

## 🔥 Key Characteristics

✔ Belongs to **class, not instance**
✔ Can be called without creating object
✔ Can access **only static variables/methods directly**
✔ Cannot use `this` or `super`

---

## 🔹 Important Rule ⚠️

### ❌ Static method CANNOT access non-static (instance) variables directly

```java
class User {
    String name = "Sanket";

    public static void greet() {
        System.out.println(name); // ❌ ERROR
    }
}
```

👉 Why?
Because static method doesn’t know which object’s `name` to use.

---

### ✅ Correct Way

```java
class User {
    String name = "Sanket";

    public static void greet() {
        User obj = new User();
        System.out.println(obj.name);
    }
}
```
---

# 📘 Static Block in Java

---

## 🔹 Correct Code

```java
class Mobile {
    String brand;
    int price;
    static String name;

    public Mobile(String brand, int price) {
        this.brand = brand;
        this.price = price;
        // this.name = "Phone"; ❌ Not recommended
    }

    // Static block
    static {
        name = "Phone";
    }
}
```

---

## 🔥 What is a Static Block?

👉 A **static block** is used to initialize static variables.

* It runs **only once**
* Runs when **class is loaded into memory**, not when objects are created

---

## 🔹 Why Not Initialize in Constructor?

```java
this.name = "Phone"; // ❌ bad practice
```

👉 Problem:

* Constructor runs **every time object is created**
* Static variable should be initialized **only once**

---

## 🔹 Static Block Behavior

```java
Mobile m1 = new Mobile("Apple", 1000);
Mobile m2 = new Mobile("Samsung", 800);
```

👉 Static block runs:

```
ONLY ONCE (when class loads)
```

👉 Constructor runs:

```
2 times (for m1 and m2)
```

---

## 🔥 Execution Order

1. Static block executes first
2. Then constructor executes

---

# Why not just do this?

```java
static String name = "Phone";
```

👉 Short answer: **You absolutely can — and in most cases, you SHOULD.**

---

# ✅ When Direct Initialization is Best

```java id="m3q7fz"
class Mobile {
    static String name = "Phone";
}
```

✔ Simple
✔ Clean
✔ Recommended

👉 This is the **best approach for constant or simple values**

---

# 🔥 Then why does `static block` exist?

Because sometimes initialization is **NOT simple**

---

## 🔹 Case 1: Complex Logic

```java id="g2ldaj"
static String name;

static {
    if (Math.random() > 0.5) {
        name = "Phone";
    } else {
        name = "Tablet";
    }
}
```

👉 You can’t do this with one-line assignment easily

---

## 🔹 Case 2: Exception Handling

```java id="4x9i0y"
static String data;

static {
    try {
        data = loadDataFromFile();
    } catch (Exception e) {
        data = "Default";
    }
}
```

👉 Static block allows **try-catch**, direct assignment doesn’t

---

## 🔹 Case 3: Multiple Steps

```java id="wz7u0y"
static int value;

static {
    value = 10;
    value *= 2;
    value += 5;
}
```

---

## 🔹 Case 4: External Resource Loading

* Database config
* File reading
* Environment setup

👉 Static block is useful here

---

# 🔥 Key Difference

| Approach                        | Use Case                 |
| ------------------------------- | ------------------------ |
| `static String name = "Phone";` | Simple values ✅          |
| `static { ... }`                | Complex logic / setup 🔥 |

---

<img width="1412" height="1062" alt="image" src="https://github.com/user-attachments/assets/e92674c5-b69c-4cee-b9b2-9affa54fad89" />

---

# 📘 Encapsulation in Java

---

## 🔹 What is Encapsulation?

👉 **Encapsulation = wrapping data (variables) and methods into a single unit (class) + controlling access**

> In simple words: **hide data and allow access only through controlled methods**

---

## 🔥 Basic Example

```java id="s0tq1o"
class Student {
    private int age;  // hidden data

    public void setAge(int age) {
        this.age = age;
    }

    public int getAge() {
        return age;
    }
}
```

---

## 🔹 Usage

```java id="hhrqcz"
Student s = new Student();
s.setAge(20);
System.out.println(s.getAge());
```

---

# 🔐 Why Encapsulation?

---

## 🔹 1. Data Hiding

```java id="kk4t5u"
s.age = -10; // ❌ Not allowed (private)
```

👉 Prevents invalid or direct access

---

## 🔹 2. Controlled Access

```java id="z5r0z9"
public void setAge(int age) {
    if(age > 0) {
        this.age = age;
    }
}
```

👉 You decide **what values are allowed**

---

## 🔹 3. Flexibility

* You can change internal logic without affecting users

---

## 🔹 4. Security

* Sensitive data stays protected

---

# 🔥 Access Modifiers (VERY IMPORTANT)

| Modifier  | Access Level         |
| --------- | -------------------- |
| private   | Only inside class 🔒 |
| default   | Same package         |
| protected | Package + subclass   |
| public    | Everywhere 🌍        |

👉 Encapsulation mainly uses **private variables + public methods**

---

# 🔥 Without Encapsulation ❌

```java id="7pqk7p"
class Student {
    public int age;
}
```

👉 Anyone can do:

```java id="1o4b4g"
s.age = -100; // ❌ No control
```

---

# 🔥 With Encapsulation ✅

```java id="vfa4ol"
class Student {
    private int age;

    public void setAge(int age) {
        if(age > 0) this.age = age;
    }

    public int getAge() {
        return age;
    }
}
```
---

# 🔥 Interview Question

👉 “Is encapsulation only about getters/setters?”

❌ NO

👉 It’s about:

* Data hiding
* Controlled access
* Abstraction of internal logic

---

# 📘 Constructors in Java

---

## 🔹 What is a Constructor?

👉 A **constructor** is a special method used to **initialize objects**

> It runs automatically when an object is created

---

## 🔹 Key Properties

✔ Same name as class
✔ No return type (not even `void`)
✔ Called automatically on object creation

---

## 🔥 Basic Example

```java id="y9v8kp"
class Student {
    String name;

    // Constructor
    Student() {
        System.out.println("Constructor called");
    }
}
```

```java id="n1b3kx"
Student s = new Student();
```

👉 Output:

```id="b0sx3q"
Constructor called
```

---

# 🔹 Types of Constructors

---

## 1️⃣ Default Constructor

👉 Provided by JVM if you don’t write one

```java id="0w7j8p"
class Student {
    int age;
}
```

👉 JVM creates:

```java id="1z2x5m"
Student() {}
```

---

## 2️⃣ No-Argument Constructor

```java id="3y6c1n"
class Student {
    Student() {
        System.out.println("No-arg constructor");
    }
}
```

---

## 3️⃣ Parameterized Constructor

```java id="5z9d2k"
class Student {
    String name;
    int age;

    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

```java id="4u8l0v"
Student s = new Student("Sanket", 20);
```

---

# 🔥 `this` Keyword (VERY IMPORTANT)

👉 Refers to current object

```java id="9x7q2r"
this.name = name;
```

👉 Used to differentiate:

* instance variable vs parameter

---

# 🔥 Constructor Overloading

👉 Multiple constructors with different parameters

```java id="8n3k2q"
class Student {
    String name;
    int age;

    Student() {
        name = "Unknown";
        age = 0;
    }

    Student(String name) {
        this.name = name;
    }

    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

---

### ✅ Constructor can be overloaded

---

# 🔥 `this()` Constructor Chaining

```java id="1k9z7p"
class Student {
    String name;
    int age;

    Student() {
        this("Unknown", 0); // calls another constructor
    }

    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```
---

### Q: Can constructor be private?

👉 YES (used in Singleton pattern)

---

### Q: Can constructor be static?

👉 ❌ NO

---

# 🔒 Private Constructor in Java

---

## 🔹 What is a Private Constructor?

👉 A constructor declared with `private` access modifier

```java id="x7v2kq"
class Test {
    private Test() {
        System.out.println("Private constructor");
    }
}
```

---

## 🔥 Key Idea

👉 **You cannot create an object of that class from outside the class**

```java id="g8p3lm"
Test t = new Test(); // ❌ ERROR
```

---

# 🔥 Then WHY use it?

This is the real question interviewers care about 👇

---

## 🔹 1. Singleton Pattern (MOST IMPORTANT 🚀)

👉 Only **one object** should exist

```java id="z1k9rx"
class Singleton {
    private static Singleton obj = new Singleton();

    private Singleton() {} // restrict object creation

    public static Singleton getInstance() {
        return obj;
    }
}
```

### Usage:

```java id="q5n2jw"
Singleton s1 = Singleton.getInstance();
Singleton s2 = Singleton.getInstance();

System.out.println(s1 == s2); // true
```

👉 Only one object is ever created ✅

---

## 🔹 2. Utility Classes (No Object Needed)

👉 Example: `Math` class

```java id="2l8x4p"
class Utility {
    private Utility() {} // prevent object creation

    public static void printHello() {
        System.out.println("Hello");
    }
}
```

👉 Use like:

```java id="3m7v0c"
Utility.printHello();
```

---

## 🔹 3. Restrict Object Creation

👉 You control **how objects are created**

* Factory methods
* Controlled instantiation

---

# 🔥 Real-Life Analogy 🧠

👉 Think of a **company CEO**

* You can’t directly create a CEO 😄
* Company decides how CEO is selected

---

# 🔥 Important Points

✔ Prevents object creation from outside
✔ Used in **Singleton & Utility classes**
✔ Can still create object **inside the class itself**
✔ Often combined with `static methods`

---

# 🔥 One-Line Answer

> A private constructor restricts object creation from outside the class and is mainly used in Singleton and utility classes.

---

# 💣 Pro Insight

👉 Even with private constructor, object can be created using:

* Reflection ❗ (advanced topic)

---

# 📘 Anonymous Object in Java

---

## 🔹 Normal Object Creation

```java
Student s1 = new Student();
```

### What happens:

* `new Student()` → creates object in heap
* `s1` → stores reference (address)

👉 You can reuse `s1` multiple times

---

## 🔹 Anonymous Object

```java
new Student();
```

### What happens:

* Object is created
* Constructor is called
* ❌ No reference is stored

👉 So:

* You **cannot reuse it**
* It becomes eligible for **garbage collection**

---

## 🔹 Calling Method Using Anonymous Object

```java
new Student().greet();
```

### What happens:

* Object is created
* `greet()` is called
* Object is discarded immediately

---

## 🔥 Example

```java
class Student {
    void greet() {
        System.out.println("Hello");
    }
}

public class Main {
    public static void main(String[] args) {
        new Student().greet();
    }
}
```

### ✅ Output:

```
Hello
```

---

# 🔥 Key Points

✔ No reference variable
✔ Used only once
✔ Not reusable
✔ Eligible for garbage collection immediately

---

# 🔥 When to Use?

👉 Use anonymous objects when:

* You need object **only once**
* No need to reuse it

---

## 🔹 Example Use Case

```java
new Scanner(System.in).nextInt();
```

👉 Create → use → discard

---

Alright — this is a **big core OOP topic**, and interviewers LOVE combining **inheritance + `this` + `super`** in tricky questions. Let’s make it crystal clear 👇

---

# 📘 Inheritance in Java

---

## 🔹 What is Inheritance?

👉 **Inheritance = acquiring properties and behavior of another class**

* Child class (subclass) inherits from Parent class (superclass)

---

## 🔥 Basic Example

```java
class Animal {
    void eat() {
        System.out.println("Eating...");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("Barking...");
    }
}
```

---

## 🔹 Usage

```java
Dog d = new Dog();
d.eat();  // inherited
d.bark(); // own method
```

---

## 🔥 Types of Inheritance (Java)

| Type                   | Supported? |
| ---------------------- | ---------- |
| Single                 | ✅ Yes      |
| Multilevel             | ✅ Yes      |
| Hierarchical           | ✅ Yes      |
| Multiple (via classes) | ❌ No       |

👉 Multiple inheritance is possible using **interfaces**

---

# 🔥 `this` Keyword

---

## 🔹 What is `this`?

👉 Refers to **current object**

---

## 🔹 Uses of `this`

---

### 1️⃣ Refer current object variables

```java
class Student {
    String name;

    Student(String name) {
        this.name = name; // differentiate
    }
}
```

---

### 2️⃣ Call current class method

```java
void display() {
    this.show();
}
```

---

### 3️⃣ Constructor chaining

```java
class Test {
    Test() {
        this(10);
    }

    Test(int x) {
        System.out.println(x);
    }
}
```

👉 Must be **first statement**

---

---

# 🔥 `super` Keyword

---

## 🔹 What is `super`?

👉 Refers to **parent class object**

---

## 🔹 Uses of `super`

---

### 1️⃣ Access parent class variable

```java
class A {
    int x = 10;
}

class B extends A {
    int x = 20;

    void show() {
        System.out.println(super.x); // 10
    }
}
```

---

### 2️⃣ Call parent class method

```java
class A {
    void show() {
        System.out.println("Parent");
    }
}

class B extends A {
    void show() {
        super.show(); // call parent
        System.out.println("Child");
    }
}
```

---

### 3️⃣ Call parent constructor

```java
class A {
    A() {
        System.out.println("Parent constructor");
    }
}

class B extends A {
    B() {
        super(); // called automatically
        System.out.println("Child constructor");
    }
}
```

---

## 🔥 Execution Flow (VERY IMPORTANT ⚠️)

```java
B obj = new B();
```

👉 Order:

```text
Parent constructor
Child constructor
```

---

# 🔥 `this` vs `super`

| Feature          | this           | super         |
| ---------------- | -------------- | ------------- |
| Refers to        | Current object | Parent object |
| Access           | Current class  | Parent class  |
| Constructor call | this()         | super()       |

---

# 🔥 Important Rules ⚠️

✔ `this()` and `super()` must be **first statement in constructor**
✔ Cannot use both together in same constructor
✔ If not written → `super()` is called automatically

---

# Up-casting and Down-casting




# 📘 Polymorphism in Java

---

## 🔹 What is Polymorphism?

👉 **Polymorphism = one thing, many forms**

> Same method name behaves **differently in different situations**

---

# 🔥 Types of Polymorphism

| Type         | When it happens    | Also Called   |
| ------------ | ------------------ | ------------- |
| Compile-time | Method Overloading | Early Binding |
| Runtime      | Method Overriding  | Late Binding  |

---

# 🔹 1. Compile-Time Polymorphism (Method Overloading)

👉 Same method name, different parameters

---

## 🔥 Example

```java
class Calculator {

    int add(int a, int b) {
        return a + b;
    }

    int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

👉 Same method name `add()` → different forms

---

## 🔹 Key Points

✔ Decided at **compile time**
✔ Method signature must be different
✔ Faster (no runtime decision)

---

# 🔹 2. Runtime Polymorphism (Method Overriding)

👉 Child class provides its own implementation of parent method

---

## 🔥 Example

```java
class Animal {
    void sound() {
        System.out.println("Animal makes sound");
    }
}

class Dog extends Animal {
    void sound() {
        System.out.println("Dog barks");
    }
}
```

---

## 🔹 Usage

```java
Animal a = new Dog();
a.sound();
```

### ✅ Output:

```text
Dog barks
```

---

## 🔥 Key Idea

👉 Method call depends on **object type (not reference type)**

---

# 🔥 Important Concepts

---

## 🔹 Upcasting

```java
Animal a = new Dog();
```

👉 Parent reference → Child object

---

## 🔹 Dynamic Method Dispatch

👉 JVM decides at runtime which method to call

---

# 🔥 Rules for Overriding

✔ Same method name
✔ Same parameters
✔ Same return type (or covariant)
✔ Cannot reduce access level

---

# 🔥 `final`, `static`, `private` (IMPORTANT ⚠️)

| Keyword | Overriding allowed?  |
| ------- | -------------------- |
| final   | ❌ No                 |
| static  | ❌ No (method hiding) |
| private | ❌ No                 |

---

# 🔥 Overloading vs Overriding

| Feature     | Overloading  | Overriding     |
| ----------- | ------------ | -------------- |
| Time        | Compile-time | Runtime        |
| Method name | Same         | Same           |
| Parameters  | Different    | Same           |
| Class       | Same class   | Parent + Child |

---

# 🔥 Real-Life Analogy 🧠

👉 Think of **“shape”**

* Circle → draw()
* Rectangle → draw()

Same method → different behavior

---

# 🔥 Interview Trick ⚠️

```java
Animal a = new Animal();
a.sound();
```

👉 Output:

```
Animal makes sound
```

```java
Animal a = new Dog();
a.sound();
```

👉 Output:

```
Dog barks
```

👉 **Object decides behavior, not reference**

---

# 📘 Method vs Variable Behavior in Polymorphism

---

## ❗ for METHODS:

> **Methods → Object decides (runtime polymorphism)**

---

## ❗ But for VARIABLES:

> **Variables → Reference type decides (NO polymorphism)**

---

# 🔹 Example

```java
class Animal {
    int x = 10;
}

class Dog extends Animal {
    int x = 20;
}
```

---

## 🔹 Case 1

```java
Dog d = new Dog();
System.out.println(d.x);
```

### ✅ Output:

```
20
```

👉 Direct object → child variable used

---

## 🔹 Case 2 (IMPORTANT ⚠️)

```java
Animal a = new Dog();
System.out.println(a.x);
```

### ✅ Output:

```
10
```

---

# 🔥 Why this happens?

👉 Because:

* Variables are resolved at **compile time**
* Based on **reference type (Animal)**

👉 No runtime decision here

---

# 🔥 Compare with Method

```java
class Animal {
    void show() {
        System.out.println("Animal");
    }
}

class Dog extends Animal {
    void show() {
        System.out.println("Dog");
    }
}
```

```java
Animal a = new Dog();
a.show();
```

### ✅ Output:

```
Dog
```

👉 Methods → runtime → object decides

---

# 🔥 Final Comparison

| Feature       | Methods | Variables    |
| ------------- | ------- | ------------ |
| Decision time | Runtime | Compile-time |
| Based on      | Object  | Reference    |
| Polymorphism  | ✅ Yes   | ❌ No         |

---

# 💣 Pro Insight (Very Important)

👉 Variables are **not overridden**, they are **hidden**

* Method → overriding
* Variable → hiding

---

# Can `final`, `static`, or `private` methods be overridden?

## ✅ Final Answer

> ❌ **They cannot be overridden**

But **each has a different reason** — that’s what interviewers care about.

---

# 🔹 1. `final` Method

```java
class A {
    final void show() {}
}

class B extends A {
    void show() {} // ❌ ERROR
}
```

### 💡 Why?

👉 `final` = **cannot be changed**

✔ Prevents overriding completely

---

# 🔹 2. `static` Method

```java
class A {
    static void show() {}
}

class B extends A {
    static void show() {} // ❌ Not overriding
}
```

### 💡 Important:

👉 This is **NOT overriding**, it's **method hiding**

* Decided at compile time
* Based on reference

✔ So technically:

> ❌ Not overridden
> ✔ Only hidden

---

# 🔹 3. `private` Method

```java
class A {
    private void show() {}
}

class B extends A {
    void show() {} // ✅ This is NOT overriding
}
```

### 💡 Why?

👉 `private` methods are **not visible to child class**

* So child class creates a **new method**, not override

---

# 🔥 Summary Table

| Modifier | Overriding Allowed? | Reason               |
| -------- | ------------------- | -------------------- |
| final    | ❌ No                | Cannot be changed    |
| static   | ❌ No (hidden)       | Compile-time binding |
| private  | ❌ No                | Not accessible       |

---

# ⚠️ Trap

```java
class A {
    private void show() {
        System.out.println("A");
    }
}

class B extends A {
    void show() {
        System.out.println("B");
    }
}
```

👉 This is **NOT overriding**, it's a completely new method.

---

<img width="1552" height="749" alt="image" src="https://github.com/user-attachments/assets/fa2b67d7-f03a-4eec-b2fa-f15a37b8e2c9" />

---

# 📘 `abstract` Keyword in Java

---

## 🔹 What is `abstract`?

👉 `abstract` means:

> **incomplete / not fully implemented**

Used for:

* Classes
* Methods

---

# 🔥 1. Abstract Class

👉 A class declared with `abstract`

```java
abstract class Animal {
    void eat() {
        System.out.println("Eating...");
    }
}
```

---

## 🔹 Key Points

✔ Cannot create object
✔ Can have:

* Abstract methods
* Normal methods

---

```java
Animal a = new Animal(); // ❌ ERROR
```

---

# 🔥 2. Abstract Method

👉 A method **without body**

```java
abstract void sound();
```

---

## 🔹 Example

```java
abstract class Animal {
    abstract void sound(); // no body

    void eat() {
        System.out.println("Eating...");
    }
}
```

---

# 🔥 Child Class Must Implement

```java
class Dog extends Animal {
    void sound() {
        System.out.println("Dog barks");
    }
}
```

---

## 🔹 Usage

```java
Animal a = new Dog();
a.sound();
```

---

# 🔥 Why Use Abstract?

👉 To **force child classes to implement certain methods**

---

## 🔹 Real-Life Example 🧠

👉 Animal:

* All animals **eat** ✅
* But sound is **different** ❗

So:

* `eat()` → defined
* `sound()` → abstract

---

# 🔥 Important Rules ⚠️

---

## ✔ Rule 1

👉 If class has abstract method → class must be abstract

---

## ✔ Rule 2

👉 Abstract class can have:

* Constructors
* Variables
* Normal methods

---

## ✔ Rule 3

👉 Child class must override abstract methods

---

## ✔ Rule 4

👉 Cannot make:

```java
abstract final class A {} // ❌
```

👉 Because:

* `abstract` → needs inheritance
* `final` → prevents inheritance

---
* `note` : normal class cant have abstract methods
---

# 🔥 Abstract vs Concrete Class

| Feature          | Abstract Class | Normal Class |
| ---------------- | -------------- | ------------ |
| Object creation  | ❌ No           | ✅ Yes        |
| Abstract methods | ✅ Yes          | ❌ No         |
| Implementation   | Partial        | Full         |

---

# 🔥 Abstract vs Interface (Quick Intro)

| Feature     | Abstract Class    | Interface           |
| ----------- | ----------------- | ------------------- |
| Methods     | Abstract + normal | Mostly abstract     |
| Variables   | Allowed           | public static final |
| Inheritance | extends           | implements          |

---

# 📘 Inner Classes in Java

👉 A class defined **inside another class**

---

# 🔥 Types of Inner Classes

1. Non-static Inner Class
2. Static Inner Class
3. Anonymous Inner Class

---

# 🔹 1. Non-Static Inner Class

👉 Also called **regular inner class**

---

## 🔥 Example

```java id="6h7w8q"
class Outer {
    int x = 10;

    class Inner {
        void display() {
            System.out.println(x);
        }
    }
}
```

---

## 🔹 Usage

```java id="x7y9k2"
Outer obj = new Outer();
Outer.Inner in = obj.new Inner();

in.display();
```

---

## 🔹 Key Points

✔ Needs outer class object
✔ Can access all outer class members (even private)

---

# 🔥 2. Static Inner Class

👉 Declared using `static`

---

## 🔥 Example

```java id="5k9n2z"
class Outer {
    static int x = 10;

    static class Inner {
        void display() {
            System.out.println(x);
        }
    }
}
```

---

## 🔹 Usage

```java id="3p8v6c"
Outer.Inner obj = new Outer.Inner();
obj.display();
```

---

## 🔹 Key Points

✔ No need of outer class object
✔ Can access only **static members of outer class**

---

# 🔥 3. Anonymous Inner Class

👉 Class **without name**, used only once

---

## 🔥 Example

```java id="9f2m4b"
abstract class Animal {
    abstract void sound();
}

public class Main {
    public static void main(String[] args) {

        Animal a = new Animal() {
            void sound() {
                System.out.println("Dog barks");
            }
        };

        a.sound();
    }
}
```

---

## 🔹 What’s happening?

* Creating a class + object **at same time**
* No class name
* Used once

---

## 🔹 Key Points

✔ No class name
✔ One-time use
✔ Often used with:

* Abstract classes
* Interfaces

---

# 🔥 Comparison

| Feature              | Inner Class | Static Inner | Anonymous |
| -------------------- | ----------- | ------------ | --------- |
| Needs outer object   | ✅ Yes       | ❌ No         | ❌ No      |
| Has name             | ✅ Yes       | ✅ Yes        | ❌ No      |
| Reusable             | ✅ Yes       | ✅ Yes        | ❌ No      |
| Access outer members | All         | Only static  | Depends   |

---

# 📘 Interface in Java

---

## 🔹 What is an Interface?

👉 An **interface is a blueprint of a class**

> It defines **what to do**, not how to do it

---

## 🔹 Basic Example

```java
interface Animal {
    void sound(); // by default public abstract
}
```

---

## 🔹 Implementation

```java
class Dog implements Animal {
    public void sound() {
        System.out.println("Dog barks");
    }
}
```

---

## 🔹 Usage

```java
Animal a = new Dog();
a.sound();
```

---

# 🔥 Key Rules (Cleaned & Corrected)

---

## ✔ 1. Methods in Interface

👉 By default:

```java
public abstract void sound();
```

Even if you write:

```java
void sound();
```

👉 Compiler treats it as:

```java
public abstract void sound();
```

---

## ✔ 2. Cannot Create Object

```java
Animal a = new Animal(); // ❌ ERROR
```

👉 Because interface is **incomplete**

---

## ✔ 3. Variables in Interface

```java
interface Test {
    int x = 10;
}
```

👉 Internally:

```java
public static final int x = 10;
```

---

### 🔹 Important Points

✔ Must be initialized
✔ Cannot change value
✔ Access using:

```java
Test.x
```

---

### ❌ Not Allowed

```java
Test.x = 20; // ❌ ERROR (final)
```

---

# 🔥 Why Do We Need Interface?

---

## 🔹 1. Achieve Abstraction

👉 Only define **what should happen**

```java
interface Payment {
    void pay();
}
```

Different implementations:

* CreditCard
* UPI
* NetBanking

---

## 🔹 2. Multiple Inheritance (VERY IMPORTANT)

👉 Java does NOT support multiple inheritance with classes

```java
class A {}
class B {}

// class C extends A, B ❌ NOT allowed
```

---

👉 But with interface:

```java
interface A {}
interface B {}

class C implements A, B {} // ✅ allowed
```

---

## 🔹 3. Loose Coupling (BIG REAL-WORLD USE)

👉 Code depends on **interface, not implementation**

```java
Animal a = new Dog();
```

👉 Tomorrow:

```java
Animal a = new Cat();
```

✔ No code change needed
✔ Flexible design

---

## 🔹 4. Standardization

👉 Forces classes to follow rules

Example:

* All vehicles must implement `start()`

---

# 🔥 Modern Interface Features (IMPORTANT ⚠️)

---

## 🔹 1. Default Methods (Java 8)

```java
interface A {
    default void show() {
        System.out.println("Default method");
    }
}
```

👉 Has body
👉 Optional to override

---

## 🔹 2. Static Methods

```java
interface A {
    static void display() {
        System.out.println("Static method");
    }
}
```

👉 Call using:

```java
A.display();
```

---

# 🔥 Interface vs Abstract Class

| Feature              | Interface           | Abstract Class    |
| -------------------- | ------------------- | ----------------- |
| Methods              | Abstract by default | Abstract + normal |
| Variables            | public static final | Any type          |
| Multiple inheritance | ✅ Yes               | ❌ No              |
| Constructor          | ❌ No                | ✅ Yes             |

---

# 🔥 One-Line Answer

> An interface is a blueprint of a class that defines abstract methods and supports multiple inheritance, abstraction, and loose coupling.

---

# 📘 Why Multiple Inheritance is a Problem in Classes?

👉 Problem = **Diamond Problem**

```java
class A {
    void show() { System.out.println("A"); }
}

class B extends A {
    void show() { System.out.println("B"); }
}

class C extends A {
    void show() { System.out.println("C"); }
}

// class D extends B, C ❌
```

👉 Now:

```java
D d = new D();
d.show();
```

❗ Which `show()` should run?

* B’s?
* C’s?

👉 **Ambiguity = confusion**

---

# 🔥 How Interfaces Solve This?

👉 Key idea:

> Interfaces **DON’T have implementation** (traditionally)

---

## 🔹 Case 1: Abstract Methods (No Problem)

```java
interface A {
    void show();
}

interface B {
    void show();
}

class C implements A, B {
    public void show() {
        System.out.println("C implementation");
    }
}
```

👉 No confusion because:

* A and B **don’t define logic**
* C **must implement it**

✔ Problem solved ✅

---

# 🔥 But What About Java 8 Default Methods?

👉 Now interfaces CAN have methods with body → possible conflict again 😈

---

## 🔹 Conflict Example

```java
interface A {
    default void show() {
        System.out.println("A");
    }
}

interface B {
    default void show() {
        System.out.println("B");
    }
}
```

```java
class C implements A, B {
    public void show() {
        System.out.println("C resolves conflict");
    }
}
```

👉 ❗ Java forces you to override

✔ So ambiguity is **resolved explicitly**

---

# 🔥 If You Want Parent’s Version

```java
class C implements A, B {
    public void show() {
        A.super.show(); // call A’s version
        B.super.show(); // call B’s version
    }
}
```
---



