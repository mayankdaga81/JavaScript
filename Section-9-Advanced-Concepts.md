# Section 9: Advanced Concepts

## Key Definitions

### What is Hoisting?

**Hoisting** is JavaScript's behavior of moving declarations (not assignments) to the top of their scope during the compilation phase, before code execution.

### What is the Event Loop?

**Event Loop** is the mechanism that allows JavaScript to perform non-blocking operations despite being single-threaded. It continuously checks if the call stack is empty and moves tasks from the queue to the stack.

### What are Modules?

**Modules** are reusable pieces of code that can be exported from one file and imported into another, helping organize and structure applications.

---

## 1. Hoisting

**What this demonstrates:** JavaScript moves variable and function declarations to the top of their scope during compilation.

### Function Hoisting

```javascript
// You can call function before declaration
sayHello(); // ✓ "Hello!" (hoisted)

function sayHello() {
  console.log("Hello!");
}

// Function declarations are fully hoisted (name + body)
```

**Function Expression (NOT Hoisted):**

```javascript
// sayHi();  // ✗ ReferenceError: Cannot access before initialization

const sayHi = function () {
  console.log("Hi!");
};

sayHi(); // ✓ "Hi!"
```

---

### Variable Hoisting with `var`

```javascript
// Variables declared with 'var' are hoisted and initialized to undefined
console.log(x); // undefined (hoisted but not assigned)
var x = 10;
console.log(x); // 10

// What JavaScript actually does:
// var x;              // Declaration hoisted to top
// console.log(x);     // undefined
// x = 10;             // Assignment stays in place
// console.log(x);     // 10
```

**Why This Can Be Problematic:**

```javascript
var name = "Global";

function test() {
  console.log(name); // undefined (not "Global"!)
  var name = "Local"; // Declaration hoisted to top of function
  console.log(name); // "Local"
}

test();

// What JavaScript sees:
// function test() {
//   var name;           // Hoisted to top
//   console.log(name);  // undefined
//   name = "Local";
//   console.log(name);  // "Local"
// }
```

---

### `let` and `const` Hoisting (Temporal Dead Zone)

```javascript
// let/const ARE hoisted but NOT initialized
// console.log(x);  // ✗ ReferenceError: Cannot access 'x' before initialization
let x = 10;
console.log(x); // 10

// Temporal Dead Zone (TDZ): Time between scope entry and declaration
function example() {
  // TDZ starts here for 'temp'
  // console.log(temp);  // ✗ ReferenceError
  let temp = 5; // TDZ ends here
  console.log(temp); // ✓ 5
}
```

---

### Class Hoisting

```javascript
// Classes are hoisted but NOT initialized (like let/const)
// const p = new Person();  // ✗ ReferenceError

class Person {
  constructor(name) {
    this.name = name;
  }
}

const p = new Person("John"); // ✓ Works
```

---

## 2. Event Loop & Asynchronous JavaScript

**What this demonstrates:** JavaScript is single-threaded but can handle asynchronous operations through the event loop.

### Call Stack, Web APIs, and Queues

```javascript
console.log("1: Start");

setTimeout(() => {
  console.log("2: Timeout");
}, 0);

Promise.resolve().then(() => {
  console.log("3: Promise");
});

console.log("4: End");

// Output:
// 1: Start
// 4: End
// 3: Promise    (Microtask queue - higher priority)
// 2: Timeout    (Macrotask queue - lower priority)

/*
EXPLANATION:
1. Synchronous code executes first (console.log "Start" and "End")
2. Microtasks (Promises) execute next
3. Macrotasks (setTimeout, setInterval) execute last
*/
```

### Event Loop Visualization

```javascript
/*
┌─────────────────────────┐
│     Call Stack          │  Main execution thread
│  (currently executing)  │
└─────────────────────────┘
           ↑
           │
┌─────────────────────────┐
│    Microtask Queue      │  Higher priority
│  (Promises, queueMicro) │  Executes after current task
└─────────────────────────┘
           ↑
           │
┌─────────────────────────┐
│   Macrotask Queue       │  Lower priority
│  (setTimeout, setInt.)  │  Executes after microtasks
└─────────────────────────┘
           ↑
           │
┌─────────────────────────┐
│      Web APIs           │  Browser features
│  (setTimeout, fetch)    │  Handle async operations
└─────────────────────────┘

EVENT LOOP:
1. Execute all synchronous code in call stack
2. Check microtask queue → execute all microtasks
3. Check macrotask queue → execute ONE macrotask
4. Repeat from step 2
*/
```

---

### Microtasks vs Macrotasks

**Microtasks (Higher Priority):**

- `Promise.then()`, `Promise.catch()`, `Promise.finally()`
- `queueMicrotask()`
- `MutationObserver`

**Macrotasks (Lower Priority):**

- `setTimeout()`, `setInterval()`
- `setImmediate()` (Node.js)
- I/O operations
- UI rendering

```javascript
console.log("Start");

// Macrotask
setTimeout(() => {
  console.log("Timeout 1");

  // This promise is a microtask
  Promise.resolve().then(() => console.log("Promise inside Timeout 1"));
}, 0);

// Macrotask
setTimeout(() => {
  console.log("Timeout 2");
}, 0);

// Microtask
Promise.resolve()
  .then(() => {
    console.log("Promise 1");
  })
  .then(() => {
    console.log("Promise 2");
  });

console.log("End");

// Output:
// Start
// End
// Promise 1
// Promise 2
// Timeout 1
// Promise inside Timeout 1
// Timeout 2

/*
EXPLANATION:
1. Sync code: "Start", "End"
2. All microtasks: "Promise 1", "Promise 2"
3. One macrotask: "Timeout 1"
4. Microtasks from that macrotask: "Promise inside Timeout 1"
5. Next macrotask: "Timeout 2"
*/
```

---

## 3. Modules (ES6)

**What this demonstrates:** Modules allow you to split code into separate files and import/export functionality.

### Exporting

**Named Exports:**

```javascript
// utils.js

// Export individual items
export const PI = 3.14159;
export const E = 2.71828;

export function add(a, b) {
  return a + b;
}

export function multiply(a, b) {
  return a * b;
}

// Or export multiple at once
const subtract = (a, b) => a - b;
const divide = (a, b) => a / b;

export { subtract, divide };

// Export with alias
const square = (x) => x * x;
export { square as sq };
```

**Default Export (one per file):**

```javascript
// calculator.js

class Calculator {
  add(a, b) {
    return a + b;
  }
  subtract(a, b) {
    return a - b;
  }
}

export default Calculator;

// Or inline
// export default class Calculator { ... }
```

**Mixing Named and Default:**

```javascript
// math.js

export const PI = 3.14159;
export const E = 2.71828;

class MathHelper {
  static square(x) {
    return x * x;
  }
}

export default MathHelper;
```

---

### Importing

**Named Imports:**

```javascript
// Import specific items
import { add, multiply } from "./utils.js";

console.log(add(5, 3)); // 8
console.log(multiply(5, 3)); // 15

// Import with alias
import { sq as square } from "./utils.js";
console.log(square(5)); // 25

// Import all as namespace
import * as utils from "./utils.js";
console.log(utils.add(5, 3)); // 8
console.log(utils.PI); // 3.14159
```

**Default Import:**

```javascript
// Import default export (can name it anything)
import Calculator from "./calculator.js";

const calc = new Calculator();
console.log(calc.add(5, 3)); // 8
```

**Mixing Named and Default:**

```javascript
import MathHelper, { PI, E } from "./math.js";

console.log(PI); // 3.14159
console.log(MathHelper.square(5)); // 25
```

**Dynamic Imports:**

```javascript
// Load module conditionally or on-demand
async function loadModule() {
  if (userWantsFeature) {
    const module = await import("./feature.js");
    module.doSomething();
  }
}

// Or with .then()
import("./utils.js")
  .then((utils) => {
    console.log(utils.add(5, 3));
  })
  .catch((err) => console.error("Failed to load module"));
```

---

### Module Best Practices

```javascript
// ❌ BAD: Exporting implementation details
// file.js
export const internalHelper = () => {
  /* ... */
};
export const publicAPI = () => {
  /* uses internalHelper */
};

// ✓ GOOD: Only export public API
// file.js
const internalHelper = () => {
  /* ... */
}; // Not exported
export const publicAPI = () => {
  /* uses internalHelper */
};

// ✓ GOOD: Organize exports at bottom
function helper1() {
  /* ... */
}
function helper2() {
  /* ... */
}
class MyClass {
  /* ... */
}

export { helper1, helper2, MyClass };

// ✓ GOOD: One responsibility per module
// userService.js - handles user operations
// authService.js - handles authentication
// apiClient.js - handles HTTP requests
```

---

## 4. Map and Set

### Map

**What this demonstrates:** `Map` is a collection of key-value pairs where keys can be any type (not just strings).

```javascript
// Create a Map
const map = new Map();

// Set key-value pairs
map.set("name", "John");
map.set("age", 30);
map.set(true, "boolean key");
map.set({ id: 1 }, "object key");

// Get values
console.log(map.get("name")); // "John"
console.log(map.get("age")); // 30

// Check if key exists
console.log(map.has("name")); // true
console.log(map.has("email")); // false

// Size
console.log(map.size); // 4

// Delete
map.delete("age");
console.log(map.has("age")); // false

// Clear all
map.clear();
console.log(map.size); // 0
```

**Initialize with Array:**

```javascript
const map = new Map([
  ["name", "John"],
  ["age", 30],
  ["city", "NYC"],
]);

console.log(map.get("name")); // "John"
```

**Iterating Map:**

```javascript
const map = new Map([
  ["name", "John"],
  ["age", 30],
  ["city", "NYC"],
]);

// forEach
map.forEach((value, key) => {
  console.log(`${key}: ${value}`);
});

// for...of with entries
for (const [key, value] of map) {
  console.log(`${key}: ${value}`);
}

// Get all keys
for (const key of map.keys()) {
  console.log(key);
}

// Get all values
for (const value of map.values()) {
  console.log(value);
}

// Get entries
for (const [key, value] of map.entries()) {
  console.log(key, value);
}
```

**Map vs Object:**

```javascript
// Object limitations
const obj = {};
obj["key1"] = "value1";
obj[{ id: 1 }] = "value2"; // Key becomes "[object Object]"
console.log(obj); // { key1: 'value1', '[object Object]': 'value2' }

// Map advantages
const map = new Map();
map.set("key1", "value1");
map.set({ id: 1 }, "value2"); // Object key works properly!

// Object keys are always strings (or symbols)
// Map keys can be ANY type: objects, arrays, functions, primitives
```

**Practical Map Example:**

```javascript
// Cache function results
const cache = new Map();

function expensiveOperation(n) {
  if (cache.has(n)) {
    console.log("Cache hit!");
    return cache.get(n);
  }

  console.log("Computing...");
  const result = n * n; // Simulate expensive operation
  cache.set(n, result);
  return result;
}

console.log(expensiveOperation(5)); // Computing... 25
console.log(expensiveOperation(5)); // Cache hit! 25
```

---

### Set

**What this demonstrates:** `Set` is a collection of unique values (no duplicates).

```javascript
// Create a Set
const set = new Set();

// Add values
set.add(1);
set.add(2);
set.add(3);
set.add(2); // Duplicate - won't be added

console.log(set.size); // 3 (not 4)

// Check if value exists
console.log(set.has(2)); // true
console.log(set.has(5)); // false

// Delete value
set.delete(2);
console.log(set.has(2)); // false

// Clear all
set.clear();
console.log(set.size); // 0
```

**Initialize with Array:**

```javascript
const set = new Set([1, 2, 3, 2, 1, 4]);
console.log(set); // Set(4) { 1, 2, 3, 4 } (duplicates removed)

// Convert Set to Array
const arr = [...set];
console.log(arr); // [1, 2, 3, 4]

// Or
const arr2 = Array.from(set);
console.log(arr2); // [1, 2, 3, 4]
```

**Iterating Set:**

```javascript
const set = new Set(["apple", "banana", "orange"]);

// forEach
set.forEach((value) => {
  console.log(value);
});

// for...of
for (const value of set) {
  console.log(value);
}

// values() and keys() (same for Sets)
for (const value of set.values()) {
  console.log(value);
}

// entries() returns [value, value] for compatibility
for (const [key, value] of set.entries()) {
  console.log(key, value); // Same value twice
}
```

**Practical Set Examples:**

**Remove Duplicates:**

```javascript
const numbers = [1, 2, 2, 3, 4, 4, 5];
const unique = [...new Set(numbers)];
console.log(unique); // [1, 2, 3, 4, 5]
```

**Set Operations:**

```javascript
const setA = new Set([1, 2, 3, 4]);
const setB = new Set([3, 4, 5, 6]);

// Union (all unique values from both)
const union = new Set([...setA, ...setB]);
console.log(union); // Set(6) { 1, 2, 3, 4, 5, 6 }

// Intersection (common values)
const intersection = new Set([...setA].filter((x) => setB.has(x)));
console.log(intersection); // Set(2) { 3, 4 }

// Difference (in A but not in B)
const difference = new Set([...setA].filter((x) => !setB.has(x)));
console.log(difference); // Set(2) { 1, 2 }
```

---

## 5. WeakMap and WeakSet

### WeakMap

**What this demonstrates:** `WeakMap` is like `Map` but keys must be objects and are "weakly held" (can be garbage collected).

```javascript
let user = { name: "John" };

// Regular Map prevents garbage collection
const map = new Map();
map.set(user, "user data");
user = null; // user object still exists in map (memory leak!)

// WeakMap allows garbage collection
const weakMap = new WeakMap();
let user2 = { name: "Jane" };
weakMap.set(user2, "user data");
user2 = null; // user2 object can now be garbage collected
```

**WeakMap Methods (Limited):**

```javascript
const weakMap = new WeakMap();
const obj = { id: 1 };

// Only 4 methods (no iteration, no size)
weakMap.set(obj, "value");
console.log(weakMap.get(obj)); // "value"
console.log(weakMap.has(obj)); // true
weakMap.delete(obj);
console.log(weakMap.has(obj)); // false

// No size, keys(), values(), entries(), forEach(), or clear()
```

**Practical WeakMap Use Case (Private Data):**

```javascript
const privateData = new WeakMap();

class Person {
  constructor(name, age) {
    // Store private data in WeakMap
    privateData.set(this, { name, age });
  }

  getName() {
    return privateData.get(this).name;
  }

  getAge() {
    return privateData.get(this).age;
  }

  setAge(age) {
    const data = privateData.get(this);
    data.age = age;
  }
}

const person = new Person("John", 30);
console.log(person.getName()); // "John"
console.log(person.getAge()); // 30
// console.log(person.name);    // undefined (truly private!)

// When person is deleted, privateData is automatically cleaned up
```

**Caching with WeakMap:**

```javascript
const cache = new WeakMap();

function processUser(user) {
  if (cache.has(user)) {
    console.log("Returning cached data");
    return cache.get(user);
  }

  console.log("Processing user...");
  const processed = {
    ...user,
    fullName: `${user.firstName} ${user.lastName}`,
  };

  cache.set(user, processed);
  return processed;
}

let user = { firstName: "John", lastName: "Doe" };
processUser(user); // Processing user...
processUser(user); // Returning cached data

user = null; // Cache automatically cleaned up
```

---

### WeakSet

**What this demonstrates:** `WeakSet` is like `Set` but values must be objects and are weakly held.

```javascript
let user1 = { name: "John" };
let user2 = { name: "Jane" };

const weakSet = new WeakSet();
weakSet.add(user1);
weakSet.add(user2);

console.log(weakSet.has(user1)); // true

user1 = null; // user1 object can be garbage collected

// Only 3 methods (no iteration, no size)
// add(), has(), delete()
```

**Practical WeakSet Use Case (Tracking Objects):**

```javascript
const visitedObjects = new WeakSet();

function process(obj) {
  if (visitedObjects.has(obj)) {
    console.log("Already processed");
    return;
  }

  console.log("Processing...");
  // Do processing
  visitedObjects.add(obj);
}

let data = { id: 1 };
process(data); // Processing...
process(data); // Already processed

data = null; // Automatically removed from visitedObjects
```

**Marking Objects as "Read-Only":**

```javascript
const readOnlyObjects = new WeakSet();

function makeReadOnly(obj) {
  readOnlyObjects.add(obj);
}

function isReadOnly(obj) {
  return readOnlyObjects.has(obj);
}

const config = { apiKey: "secret" };
makeReadOnly(config);

function modifyObject(obj, key, value) {
  if (isReadOnly(obj)) {
    throw new Error("Cannot modify read-only object");
  }
  obj[key] = value;
}

// modifyObject(config, "apiKey", "new");  // ✗ Error
```

---

## Quick Reference

### Hoisting Rules

| Declaration Type | Hoisted? | Initialized?    | TDZ? |
| ---------------- | -------- | --------------- | ---- |
| `var`            | Yes      | Yes (undefined) | No   |
| `let`            | Yes      | No              | Yes  |
| `const`          | Yes      | No              | Yes  |
| `function`       | Yes      | Yes (full)      | No   |
| `class`          | Yes      | No              | Yes  |

### Event Loop Priority

```
1. Synchronous code (call stack)
2. Microtasks (Promises, queueMicrotask)
3. Macrotasks (setTimeout, setInterval)
```

### Module Patterns

```javascript
// Named exports (multiple per file)
export const PI = 3.14;
export function add() {}

// Default export (one per file)
export default class {}

// Import
import Default, { named1, named2 } from "./module.js";
import * as all from "./module.js";
```

### Map vs Object

| Feature     | Object                      | Map                     |
| ----------- | --------------------------- | ----------------------- |
| Key types   | String/Symbol               | Any type                |
| Key order   | Not guaranteed              | Insertion order         |
| Size        | Manual count                | `.size` property        |
| Iteration   | `for...in`, `Object.keys()` | `forEach()`, `for...of` |
| Performance | Slower for add/remove       | Faster for add/remove   |

### Set vs Array

| Feature         | Array               | Set                |
| --------------- | ------------------- | ------------------ |
| Duplicates      | Allowed             | Not allowed        |
| Order           | Maintained          | Maintained         |
| Access by index | Yes                 | No                 |
| Has value check | `includes()` O(n)   | `has()` O(1)       |
| Add/Remove      | `push()`/`splice()` | `add()`/`delete()` |

### WeakMap/WeakSet vs Map/Set

| Feature            | Map/Set               | WeakMap/WeakSet                 |
| ------------------ | --------------------- | ------------------------------- |
| Keys/Values        | Any type              | Objects only                    |
| Iteration          | Yes                   | No                              |
| Size property      | Yes                   | No                              |
| Garbage collection | No (keeps references) | Yes (weak references)           |
| Use cases          | General collections   | Private data, caching, tracking |

---

**That's Section 9!** You've now learned advanced JavaScript concepts including hoisting, the event loop, modules, and collection types. You're ready for TypeScript! 🚀
