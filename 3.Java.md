Here are **clean, exam-ready notes for Threads in Java** 👇

---

# 🔹 Threads in Java

## 👉 Definition

Thread = **independent path of execution inside a program**

👉 Used for **multitasking / parallel execution**

---

# 🔹 Ways to Create Thread

## 1. Extending `Thread` class

```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running");
    }
}
```

```java
MyThread t1 = new MyThread();
t1.start();
```

---

## 2. Implementing `Runnable` (Preferred ✅)

```java
class MyTask implements Runnable {
    public void run() {
        System.out.println("Task running");
    }
}
```

```java
Thread t1 = new Thread(new MyTask());
t1.start();
```

---

# 🔹 `start()` vs `run()` ⚠️

| Method    | Meaning            |
| --------- | ------------------ |
| `start()` | creates new thread |
| `run()`   | normal method call |

❌ `run()` directly → no multithreading
✔ always use `start()`

---

# 🔹 Thread Life Cycle

1. **New** → thread created
2. **Runnable** → ready to run
3. **Running** → executing
4. **Blocked/Waiting** → paused
5. **Terminated** → finished

---

# 🔹 Important Methods

```java
t.start();      // start thread
t.run();        // execute task
t.sleep(1000);  // pause (ms)
t.join();       // wait for thread
t.isAlive();    // check running
```

---

# 🔹 Thread Scheduling

👉 JVM decides which thread runs
👉 No fixed order

---

# 🔹 Synchronization (Important 🔥)

👉 Used to avoid **race condition**

```java
synchronized void show() {
    // critical section
}
```

---

# 🔹 Advantages

✔ Multitasking
✔ Better performance
✔ Efficient CPU usage

---

# 🔹 Disadvantages

❌ Complex
❌ Debugging difficult
❌ Synchronization issues

---

# 🔹 One-Line Summary 💡

👉 Thread = **lightweight process for parallel execution**

---

# 🔹 Thread Safety using `synchronized`

👉 Thread safety = **multiple threads access shared data without errors**

👉 Problem = **race condition** (multiple threads update same variable)

---

## 🔹 Example without synchronization ❌

* `count++` is **not atomic**
* Threads may overwrite each other

---

## 🔹 Using `synchronized` ✅

```java
class Counter {
    int count;

    public synchronized void inc() {
        count++;
    }
}
```

👉 Only **one thread at a time** can execute `inc()`

---

## 🔹 Full Example

```java
class Demo {
    public static void main(String[] args) throws Exception {

        Counter c = new Counter();

        Runnable obj1 = () -> {
            for (int i = 0; i < 1000; i++) {
                c.inc();
            }
        };

        Runnable obj2 = () -> {
            for (int i = 0; i < 1000; i++) {
                c.inc();
            }
        };

        Thread t1 = new Thread(obj1);
        Thread t2 = new Thread(obj2);

        t1.start();
        t2.start();

        t1.join();
        t2.join(); // wait for threads to finish

        System.out.println(c.count);
    }
}
```

---

## 🔹 Important Points ⚠️

✔ `synchronized` → prevents race condition
✔ Locks method/object for one thread at a time
✔ `join()` → main thread waits for other threads

❌ Without `join()` → result may print early

---

## 🔹 One-Line Summary 💡

👉 `synchronized` = **only one thread can access critical section at a time**
