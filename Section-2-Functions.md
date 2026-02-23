# Section 2: Functions

## 1. Function Declarations vs Expressions

### Function Declaration

Hoisted - can be called before definition.

```javascript
greet("John"); // ✓ Works (hoisted)

function greet(name) {
  return `Hello, ${name}`;
}
```

### Function Expression

Not hoisted - must be defined before use - Function expressions and arrow functions are created at the point in the code where the assignment occurs during the execution phase, meaning they are not "fully" hoisted. They are treated like variables (var, let, or const), and only the variable declaration part gets hoisted..

```javascript
// sayHi("John");  // ✗ Error (not hoisted)

const sayHi = function (name) {
  return `Hi, ${name}`;
};

sayHi("John"); // ✓ Works
```

### Named Function Expression

Useful for recursion and debugging.

```javascript
const factorial = function fact(n) {
  return n <= 1 ? 1 : n * fact(n - 1); // calls itself
};

factorial(5); // 120
// fact(5);    // ✗ Error (name only available inside)
```
### Notes

- You can not call the same function inside that function in case of Function Declaration or Function Expression.
- You must use either Named function expression or Arrow functions to call the same function inside the own function.

### When to Use

- **Declaration**: Top-level utility functions
- **Expression**: Assigning to variables, passing as arguments

---

## 2. Arrow Functions

Shorter syntax, lexical `this` binding.

### Basic Syntax

```javascript
// Traditional
function add(a, b) {
  return a + b;
}

// Arrow function
const add = (a, b) => {
  return a + b;
};

// Implicit return (one expression)
const add = (a, b) => a + b;

// Single parameter (no parentheses needed)
const square = (x) => x * x;

// No parameters
const greet = () => "Hello";

// Returning object (wrap in parentheses)
const makePerson = (name, age) => ({ name, age });
```

### Key Differences from Regular Functions

#### 1. No `this` binding

Arrow functions inherit `this` from parent scope.

```javascript
const person = {
  name: "John",
  greet: function () {
    setTimeout(function () {
      console.log(this.name); // undefined (this = window/global)
    }, 1000);
  },
  greetArrow: function () {
    setTimeout(() => {
      console.log(this.name); // "John" (inherits person's this)
    }, 1000);
  },
};
```

#### 2. No `arguments` object

```javascript
function regular() {
  console.log(arguments); // [1, 2, 3]
}
regular(1, 2, 3);

const arrow = () => {
  console.log(arguments); // ✗ ReferenceError
};

// Use rest parameters instead
const arrow = (...args) => {
  console.log(args); // [1, 2, 3]
};
```

#### 3. Cannot be used as constructor

```javascript
function Person(name) {
  this.name = name;
}
new Person("John"); // ✓ Works

const PersonArrow = (name) => {
  this.name = name;
};
// new PersonArrow("John");  // ✗ TypeError
```

### When NOT to Use Arrow Functions

```javascript
// ✗ Object methods (this won't work)
const person = {
  name: "John",
  greet: () => {
    console.log(this.name); // undefined
  },
};

// ✓ Use regular function
const person = {
  name: "John",
  greet() {
    console.log(this.name); // "John"
  },
};

// ✗ Event handlers (when you need this)
button.addEventListener("click", () => {
  this.classList.toggle("active"); // this is not the button
});

// ✓ Use regular function
button.addEventListener("click", function () {
  this.classList.toggle("active"); // this is the button
});
```

---

## 3. Parameters

### Default Parameters

```javascript
function greet(name = "Guest", greeting = "Hello") {
  return `${greeting}, ${name}`;
}

greet(); // "Hello, Guest"
greet("John"); // "Hello, John"
greet("John", "Hi"); // "Hi, John"

// Default can reference previous parameters
function multiply(a, b = a * 2) {
  return a * b;
}
multiply(5); // 50 (5 * 10)

// Default can be function call
function getDefault() {
  return 10;
}
function add(a, b = getDefault()) {
  return a + b;
}
```

### Rest Parameters (...)

Collects remaining arguments into an array.

```javascript
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

sum(1, 2, 3); // 6
sum(1, 2, 3, 4, 5); // 15

// Rest must be last parameter
function log(level, ...messages) {
  console.log(`[${level}]`, ...messages); // '[ERROR]', 'Failed', 'to', 'connect' 
}
log("ERROR", "Failed", "to", "connect");
```

### Spread Operator (...)

Expands array/object into individual elements.

```javascript
// Spreading arrays
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2]; // [1,2,3,4,5,6]

// Function arguments
const numbers = [1, 2, 3];
Math.max(...numbers); // 3 (instead of Math.max(numbers))

// Copying arrays (shallow copy) - If the original array contains nested objects or arrays, the shallow copy copies the memory addresses (references) of those inner elements, not the elements themselves.
const original = [1, 2, 3];
const copy = [...original];

// Spreading objects
const person = { name: "John", age: 30 };
const employee = { ...person, id: 123 };
// { name: "John", age: 30, id: 123 }

// Merging objects (later properties override)
const obj1 = { a: 1, b: 2 };
const obj2 = { b: 3, c: 4 };
const merged = { ...obj1, ...obj2 }; // { a: 1, b: 3, c: 4 }
```

### Destructuring Parameters

```javascript
// Object destructuring
function greet({ name, age }) {
  return `${name} is ${age} years old`;
}
greet({ name: "John", age: 30 }); // "John is 30 years old"

// With defaults - (You must add = {} at the end, this is part of Syntax)
function greet({ name = "Guest", age = 0 } = {}) {
  return `${name} is ${age} years old`;
}
greet(); // "Guest is 0 years old"

// Array destructuring
function getCoordinates([x, y]) {
  return `X: ${x}, Y: ${y}`;
}
getCoordinates([10, 20]); // "X: 10, Y: 20"
```

---

## 4. Callback Functions

Function passed as an argument to another function.

```javascript
// Basic callback
function processData(data, callback) {
  const result = data * 2;
  callback(result);
}

processData(5, function (result) {
  console.log(result); // 10
});

// With arrow function
processData(5, (result) => console.log(result));

// Real-world examples
setTimeout(() => {
  console.log("Delayed");
}, 1000);

[1, 2, 3].forEach((num) => {
  console.log(num * 2);
});

button.addEventListener("click", () => {
  console.log("Clicked");
});
```

### Callback Hell (Pyramid of Doom)

```javascript
// ✗ Nested callbacks - hard to read
getData(function (a) {
  getMoreData(a, function (b) {
    getMoreData(b, function (c) {
      getMoreData(c, function (d) {
        console.log(d);
      });
    });
  });
});

// ✓ Solution: Use Promises or async/await (Section 6)
```

---

## 5. Higher-Order Functions

Functions that take functions as arguments or return functions.

### Taking Functions as Arguments

```javascript
// Built-in higher-order functions
[1, 2, 3].map((x) => x * 2); // [2, 4, 6]
[1, 2, 3].filter((x) => x > 1); // [2, 3]
[1, 2, 3].reduce((sum, x) => sum + x, 0); // 6

// Custom higher-order function
function repeat(n, action) {
  for (let i = 0; i < n; i++) {
    action(i);
  }
}
repeat(3, (i) => console.log(`Iteration ${i}`));
```

### Returning Functions

```javascript
// Function factory
function multiplier(factor) {
  return function (number) {
    return number * factor;
  };
}

const double = multiplier(2);
const triple = multiplier(3);

double(5); // 10
triple(5); // 15

// With arrow functions
const multiplier = (factor) => (number) => number * factor;
const double = multiplier(2);

// Practical example: Creating specialized functions
function createGreeter(greeting) {
  return (name) => `${greeting}, ${name}!`;
}

const greetHello = createGreeter("Hello");
const greetHi = createGreeter("Hi");

greetHello("John"); // "Hello, John!"
greetHi("Jane"); // "Hi, Jane!"
```

---

## 6. Closures

Function that has access to variables from its outer scope, even after the outer function has returned.

### Basic Closure

```javascript
function outer() {
  const message = "Hello";

  function inner() {
    console.log(message); // Accesses outer's variable
  }

  return inner;
}

const myFunc = outer();
myFunc(); // "Hello" (outer has finished, but inner remembers message)
```

### Practical Examples

#### Private Variables

```javascript
function createCounter() {
  let count = 0; // Private variable

  return {
    increment() {
      return ++count;
    },
    decrement() {
      return --count;
    },
    getCount() {
      return count;
    },
  };
}

const counter = createCounter();
counter.increment(); // 1
counter.increment(); // 2
counter.getCount(); // 2
// count is not accessible directly (private)
```

#### Function Factories

```javascript
function createMultiplier(multiplier) {
  return function (number) {
    return number * multiplier;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

double(10); // 20
triple(10); // 30
```

#### Event Handlers with State

```javascript
function setupButton(buttonName) {
  let clickCount = 0;

  document.getElementById(buttonName).addEventListener("click", function () {
    clickCount++;
    console.log(`${buttonName} clicked ${clickCount} times`);
  });
}

setupButton("myButton");
// Each button maintains its own clickCount
```

### Common Closure Pitfall: Loop with var

```javascript
// ✗ Problem with var
for (var i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i); // 3, 3, 3 (all reference same i)
  }, 1000);
}

// ✓ Solution 1: Use let (block-scoped)
for (let i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i); // 0, 1, 2
  }, 1000);
}

// ✓ Solution 2: IIFE (with var)
for (var i = 0; i < 3; i++) {
  (function (j) {
    setTimeout(function () {
      console.log(j); // 0, 1, 2
    }, 1000);
  })(i);
}
```

---

## 7. IIFE (Immediately Invoked Function Expression)

Function that executes immediately after creation.

### Syntax

```javascript
// Standard IIFE
(function () {
  console.log("Executed immediately");
})();

// With parameters
(function (name) {
  console.log(`Hello, ${name}`);
})("John");

// Arrow function IIFE
(() => {
  console.log("Executed immediately");
})();

// Alternative syntax (both parentheses patterns work)
(function () {
  console.log("Works");
})();
```

### Use Cases

#### 1. Avoid Polluting Global Scope

```javascript
// ✗ Global pollution
var count = 0;
var increment = function () {
  count++;
};

// ✓ Encapsulated with IIFE
(function () {
  var count = 0;
  var increment = function () {
    count++;
  };
  // count and increment are private
})();
```

#### 2. Module Pattern (Pre-ES6)

```javascript
const Calculator = (function () {
  // Private variables/functions
  let result = 0;

  function log(msg) {
    console.log(`[Calculator] ${msg}`);
  }

  // Public API
  return {
    add(n) {
      result += n;
      log(`Added ${n}`);
      return this;
    },
    subtract(n) {
      result -= n;
      log(`Subtracted ${n}`);
      return this;
    },
    getResult() {
      return result;
    },
  };
})();

Calculator.add(5).subtract(2).getResult(); // 3
```

#### 3. Creating Private Scope

```javascript
(function () {
  var temp = "This won't leak";
  // Do work here
})();

// console.log(temp);  // ✗ ReferenceError
```

#### 4. Async IIFE (Modern)

```javascript
(async () => {
  const data = await fetchData();
  console.log(data);
})();
```

---

## Quick Reference

### Function Types

```javascript
// Declaration (hoisted)
function name() {}

// Expression (not hoisted)
const name = function () {};

// Arrow (lexical this)
const name = () => {};

// Method shorthand
const obj = {
  name() {},
};
```

### Parameter Patterns

```javascript
// Default
function fn(a = 1) {}

// Rest (collect)
function fn(...args) {}

// Spread (expand)
fn(...array);

// Destructure
function fn({ x, y }) {}
function fn([a, b]) {}
```

### Common Array HOF Methods

```javascript
.map(fn)        // Transform each element
.filter(fn)     // Keep elements that pass test
.reduce(fn, initial)  // Combine to single value
.forEach(fn)    // Execute for each (no return)
.find(fn)       // First element that passes
.some(fn)       // True if any pass
.every(fn)      // True if all pass
```

---

