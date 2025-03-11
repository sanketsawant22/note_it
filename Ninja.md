# 🚀 JavaScript Dark Ninja Tricks 💯🥷🔥

## ✅ 1. Convert ANYTHING to Boolean Without If/Else

```js
console.log(!!"Sanket");  // true
console.log(!!0);         // false
console.log(!!null);      // false
console.log(!!undefined); // false
console.log(!![]);        // true
console.log(!!{});        // true
```

👉 **Explanation:** Adding `!!` before any value instantly converts it to a Boolean.

---

## ✅ 2. Swap Two Variables WITHOUT Temp Variable

```js
let a = 5;
let b = 10;

[a, b] = [b, a];

console.log(a, b);
// Output: 10 5
```

👉 **No temp variable needed, cleanest way to swap values.**

---

## ✅ 3. Merge Two Arrays Without Loops

```js
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];

let merged = [...arr1, ...arr2];
console.log(merged);
// Output: [1, 2, 3, 4, 5, 6]
```

👉 **Spread operator = magic for merging arrays.**

---

## ✅ 4. Clone Array/Object Without Loop

```js
let arr = [1, 2, 3];
let newArr = [...arr];

let obj = { name: "Sanket", age: 21 };
let newObj = { ...obj };
```

👉 **Spread operator instantly clones arrays/objects.**

---

## ✅ 5. Remove Duplicates From Array In One Line

```js
let arr = [1, 2, 2, 3, 4, 4, 5];
let unique = [...new Set(arr)];

console.log(unique);
// Output: [1, 2, 3, 4, 5]
```

👉 **Set + Spread Operator = Remove Duplicates Instantly.**

---

## ✅ 6. Convert String to Number Instantly

```js
let str = "123";
let num = +str;

console.log(num);
// Output: 123 (as number)
```

👉 **Add `+` before string to instantly convert to number.**

---

## ✅ 7. Shuffle Array Randomly (Like Instagram Feed)

```js
let posts = [1, 2, 3, 4, 5];
let shuffled = posts.sort(() => Math.random() - 0.5);

console.log(shuffled);
```

👉 **Super easy way to randomize array order.**

---

## ✅ 8. Check Empty Array/Object In One Line

```js
if(!array.length) { console.log("Array is empty"); }

if(!Object.keys(obj).length) { console.log("Object is empty"); }
```

👉 **Clean and short condition checks.**

---

## ✅ 9. Create Array With Length Without Loop

```js
let arr = Array.from({ length: 100 }, (_, i) => i+1);
console.log(arr);
```

👉 **Creates an array from 1 to 100 in one line.**

---

## ✅ 10. Reverse String Without Loop

```js
let str = "hello";
let reversed = [...str].reverse().join('');

console.log(reversed);
// Output: "olleh"
```

👉 **Super clean string reversal.**

---

## ✅ 11. One-Line If-Else (Ternary Operator)

```js
age >= 18 ? console.log("Adult") : console.log("Minor");
```

👉 **Super clean conditional check.**

---

## ✅ 12. Get Random Number Between Two Values

```js
let random = Math.floor(Math.random() * (max - min + 1)) + min;
```

👉 **Perfect for games or random logic.**

---

## ✅ 13. Flatten Multidimensional Arrays

```js
let arr = [1, [2, [3, [4]]]];
let flat = arr.flat(Infinity);

console.log(flat);
// Output: [1, 2, 3, 4]
```

👉 **Converts deeply nested arrays to a flat array.**

---

## ✅ 14. Get Last Item In Array Without Length

```js
let arr = [10, 20, 30];

console.log(arr.at(-1));
// Output: 30
```

👉 **No need to calculate length anymore.**

---

## ✅ 15. Dynamic Object Keys

```js
let key = "name";
let person = {
  [key]: "Sanket"
};

console.log(person.name);
// Output: Sanket
```

👉 **Use dynamic keys in objects.**

---

## ✅ 16. Short-Circuit Evaluation

```js
let user = null;

console.log(user || "Guest");
// Output: Guest
```

👉 **Faster conditional assignment.**

---

## ✅ 17. Measure Function Execution Time

```js
console.time('test');

// Some code here

console.timeEnd('test');
```

👉 **Super useful for performance testing.**

---
