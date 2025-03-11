# 🚀 JavaScript Important Concepts 💯🔥

---

## ✅ 1. Prototype 🧠

👉 **Definition:** Prototype allows one object to access the data and methods of another object. It is a way to achieve **inheritance** in JavaScript, similar to how Java uses classes.

👉 **Example:**
```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function() {
  console.log(`Hello, my name is ${this.name}`);
};

let person1 = new Person("Sanket");
person1.sayHello();
```
👉 Output:
```
Hello, my name is Sanket
```

👉 **Note:** Prototype is like a parent object that shares its properties or methods with child objects.

---

## ✅ 2. Closure 🌀

👉 **Definition:** Closure allows an inner function to access the **parent function's variables** even after the **parent function has finished execution**.

👉 **Example:**
```js
function outerFunction() {
  let count = 0;

  function innerFunction() {
    count++;
    console.log(count);
  }

  return innerFunction;
}

let counter = outerFunction();
counter();  // Output: 1
counter();  // Output: 2
```

👉 **Why Useful?**
- It is commonly used in **data privacy, currying, and function factories**.

---

## ✅ 3. Function vs Method 💡

| Function                | Method                     |
|------------------------|----------------------------|
| Block of code that performs a task | Function that belongs to an object.   |
| Called independently     | Called using an object like `obj.method()`  |
| No access to `this` keyword | Can use `this` to refer to the object.    |

👉 **Example:**
```js
function greet() {
  console.log("Hello");
}

let person = {
  name: "Sanket",
  greet: function() {
    console.log("Hi, " + this.name);
  }
};

greet();            // Function
person.greet();     // Method
```

---

## ✅ 4. Hoisting 📜

👉 **Definition:** JavaScript moves **function and variable declarations** to the **top of the code** during execution.

👉 **Example:**
```js
console.log(name);   // undefined
var name = "Sanket";

sayHello();
function sayHello() {
  console.log("Hello Sanket");
}
```

👉 **What Happened?**
- The variable `name` was moved to the top but was initialized as **undefined**.
- The function `sayHello` was moved to the top and executed without error.

---

## ✅ 5. Event Loop ⏳

👉 **Definition:** Event Loop handles **JavaScript tasks** in two phases:
1. **Synchronous Code** (normal JS code)
2. **Asynchronous Code** (setTimeout, API calls)

👉 **Example:**
```js
console.log("Start");

setTimeout(() => {
  console.log("Inside Timeout");
}, 2000);

console.log("End");
```

👉 Output:
```
Start
End
Inside Timeout
```

👉 **Why?** Event Loop first executes **synchronous tasks** and then handles **asynchronous tasks**.

---

## ✅ 6. Synchronous vs Asynchronous JavaScript 💨

| Synchronous                     | Asynchronous                        |
|-------------------------------|--------------------------------------|
| Executes one line after another | Doesn't wait for one task to complete.  |
| Blocking in nature              | Non-blocking in nature              |
| Example: Normal JS Code          | Example: API Calls, setTimeout      |

👉 **Example:**
```js
console.log("Start");
setTimeout(() => console.log("Async Task"), 1000);
console.log("End");
```
Output:
```
Start
End
Async Task
```

---

## ✅ 7. Async & Await 💎

👉 **Definition:**
- **async** makes a function return a Promise.
- **await** makes JavaScript wait until the Promise resolves.

👉 **Example:**
```js
async function fetchData() {
  let data = await fetch('https://api.example.com/data');
  console.log(data);
}

fetchData();
```

👉 This is used in **API calls, database calls, etc.**

---

## ✅ 8. For...in Loop 🔥

👉 **Definition:** Iterates over the **keys/indexes** of an object or array.

👉 Example:
```js
let person = { name: "Sanket", age: 21 };

for(let key in person) {
  console.log(key);  // name, age
}
```

👉 **Use:** Best for iterating objects.

---

## ✅ 9. For...of Loop ⚡

👉 **Definition:** Iterates over the **values** of an array or string.

👉 Example:
```js
let arr = [10, 20, 30];

for(let value of arr) {
  console.log(value);  // 10, 20, 30
}
```

👉 **Use:** Best for iterating arrays or strings.

---

## ✅ 10. arr.forEach() 🔥

👉 **Definition:** Executes a function for **each array element** but does not return a new array.

👉 Example:
```js
let arr = [1, 2, 3];

arr.forEach(num => console.log(num * 2));
```
Output:
```
2
4
6
```

👉 **No new array is returned.**

---

## ✅ 11. arr.map() 🔥

👉 **Definition:** Executes a function for each array element and **returns a new array**.

👉 Example:
```js
let arr = [1, 2, 3];
let newArr = arr.map(num => num * 2);

console.log(newArr);
```
Output:
```
[2, 4, 6]
```

---

## ✅ 12. arr.unshift() 🚀

👉 **Definition:** Adds an element to the **beginning of the array.**

👉 Example:
```js
let arr = [2, 3, 4];
arr.unshift(1);

console.log(arr);
```
Output:
```
[1, 2, 3, 4]
```
---

## ✅ 13. Promise.all() vs Promise.race() vs Promise.any() ⚡

👉 **Promise.all()** - Resolves when **ALL promises are resolved** or fails if any one fails.
👉 **Promise.race()** - Resolves as soon as **any one promise resolves**.
👉 **Promise.any()** - Resolves as soon as **any one promise fulfills (ignores rejection)**.

👉 **Example:**

```js
Promise.any([
  fetch(url1),
  fetch(url2),
  fetch(url3)
]).then(res => console.log(res));
```

---

## ✅ 14. Call, Apply, Bind 💡

👉 **Call:** Calls the function with a specified `this` value.
👉 **Apply:** Same as call but takes arguments as an array.
👉 **Bind:** Returns a new function with specified `this` value.

👉 **Example:**

```js
function greet(city) {
  console.log(`${this.name} from ${city}`);
}

let person = { name: 'Sanket' };
greet.call(person, 'Pune');
greet.apply(person, ['Mumbai']);
let newGreet = greet.bind(person, 'Delhi');
newGreet();
```

---

## ✅ 15. Debouncing vs Throttling 🚀

👉 **Debouncing:** Delay function execution until user stops input.
👉 **Throttling:** Limits function execution within a specific time interval.

👉 **Example (Debounce):**

```js
function debounce(func, delay) {
  let timer;
  return function() {
    clearTimeout(timer);
    timer = setTimeout(() => func.apply(this, arguments), delay);
  };
}
```

👉 **Example (Throttle):**

```js
function throttle(func, limit) {
  let lastCall = 0;
  return function() {
    let now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      func.apply(this, arguments);
    }
  };
}
```

---
