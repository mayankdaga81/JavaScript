# Section 8: Scope & Context

## Key Definitions

### What is Scope?

**Scope** determines where variables are accessible in your code. It defines the visibility and lifetime of variables - which parts of your code can "see" and use a particular variable.

### What is Context?

**Context** refers to the value of `this` keyword at a particular point in code execution. It determines what object the current code is operating on.

### What is Execution Context?

**Execution context** is the environment in which JavaScript code is evaluated and executed. It contains information about variables, functions, and the value of `this`.

---

## 1. Scope Types

### Global Scope

**What this demonstrates:** Variables declared outside any function or block are in global scope, accessible everywhere.

**Note:** In browsers, the global object is `window`. In Node.js, it's `global`. Global variables declared with `var` become properties of this global object.

```javascript
// Global scope
const globalVar = "I'm global";
var alsoGlobal = "Me too";

function showGlobal() {
  console.log(globalVar); // ✓ Can access
  console.log(alsoGlobal); // ✓ Can access
}

if (true) {
  console.log(globalVar); // ✓ Can access
}

showGlobal();

// Global variables become properties of window (in browsers)
// EXPLANATION: In browsers, 'window' is the global object
// - Variables declared with 'var' in global scope become properties of window
// - Variables declared with 'let'/'const' do NOT become window properties
// - They exist in global scope but in a separate "script scope"

console.log(window.alsoGlobal); // "Me too" (var creates window property)
console.log(window.globalVar); // undefined (let/const don't create window properties)

// You can still access globalVar directly, just not through window
console.log(globalVar); // "I'm global" (accessible in global scope)

// Why this matters:
// 1. var can accidentally overwrite existing window properties
var alert = "oops"; // ✗ Overwrites window.alert() function!

// 2. let/const are safer - they don't pollute window object
let console = "safe"; // ✗ Error: 'console' already declared (protected)
```

**Issue with Global Scope:**

```javascript
// Global pollution - bad practice
var name = "John";
var age = 30;

function createUser() {
  var name = "Jane"; // Different variable, but confusing
  age = 25; // Accidentally modifies global age!
}

createUser();
console.log(name); // "John" (unaffected)
console.log(age); // 25 (accidentally changed!)
```

---

### Function Scope

**What this demonstrates:** Variables declared with `var` inside a function are only accessible within that function.

```javascript
function myFunction() {
  var functionVar = "I'm in function scope";
  let alsoFunctionScoped = "Me too";

  console.log(functionVar); // ✓ Works

  if (true) {
    console.log(functionVar); // ✓ Still accessible
  }
}

myFunction();
// console.log(functionVar);  // ✗ ReferenceError: not defined
```

**`var` is Function-Scoped:**

```javascript
function testVar() {
  if (true) {
    var x = 10; // var is function-scoped, not block-scoped.
    const y = 20; // const/let is block-scoped
  }
  console.log(x); // ✓ 10 (accessible outside if block)
  console.log(y);  // ✗ y is not defined

testVar();
```

---

### Block Scope

**What this demonstrates:** Variables declared with `let` and `const` are only accessible within the block `{}` they're defined in.

```javascript
function testBlockScope() {
  // Block scope with let/const
  if (true) {
    let blockVar = "I'm block scoped";
    const alsoBlock = "Me too";
    var notBlock = "I'm function scoped";

    console.log(blockVar); // ✓ Works inside block
    console.log(alsoBlock); // ✓ Works inside block
  }

  // console.log(blockVar);   // ✗ ReferenceError
  // console.log(alsoBlock);  // ✗ ReferenceError
  console.log(notBlock); // ✓ Works (var is function-scoped)
}

testBlockScope();
```

**Common Block Scope Examples:**

```javascript
// for loop
for (let i = 0; i < 3; i++) {
  console.log(i); // 0, 1, 2
}
// console.log(i);  // ✗ ReferenceError (let is block-scoped)

for (var j = 0; j < 3; j++) {
  console.log(j); // 0, 1, 2
}
console.log(j); // ✓ 3 (var is function-scoped, leaks out)

// while loop
while (true) {
  let temp = "temporary";
  break;
}
// console.log(temp);  // ✗ ReferenceError

// Code blocks
{
  let blockScoped = "only here";
  console.log(blockScoped); // ✓ Works
}
// console.log(blockScoped);  // ✗ ReferenceError
```

---

## 2. Lexical Scope (Static Scope)

**What this demonstrates:** Functions are executed using the variable scope that was in effect when they were **defined**, not when they are **called**.

### Basic Lexical Scope

```javascript
const name = "Global";

function outer() {
  const name = "Outer";

  function inner() {
    console.log(name); // Looks for 'name' in its lexical scope
  }

  inner();
}

outer(); // "Outer" (uses name from where inner was defined)
```

### Scope Chain

**What this demonstrates:** JavaScript looks for variables starting from the innermost scope and moving outward until found.

```javascript
const global = "I'm global";

function level1() {
  const level1Var = "Level 1";

  function level2() {
    const level2Var = "Level 2";

    function level3() {
      const level3Var = "Level 3";

      // Scope chain: level3 → level2 → level1 → global
      console.log(level3Var); // Found in level3
      console.log(level2Var); // Found in level2 (one level up)
      console.log(level1Var); // Found in level1 (two levels up)
      console.log(global); // Found in global (three levels up)
      // console.log(notDefined);  // ✗ ReferenceError (not in chain)
    }

    level3();
  }

  level2();
}

level1();
```

### Lexical Scope with Closures

```javascript
function createCounter() {
  let count = 0; // Lexically scoped to createCounter

  return function () {
    count++; // Can access count from outer lexical scope
    return count;
  };
}

const counter1 = createCounter();
const counter2 = createCounter();

console.log(counter1()); // 1
console.log(counter1()); // 2
console.log(counter2()); // 1 (separate lexical scope)
console.log(counter1()); // 3
```

---

## 3. Execution Context

**What this demonstrates:** Every function call creates a new execution context containing variables, parameters, and `this` value.

### Types of Execution Context

```javascript
// 1. GLOBAL EXECUTION CONTEXT
const globalVar = "global";

function outer(x) {
  // 2. FUNCTION EXECUTION CONTEXT (outer)
  const outerVar = "outer";

  function inner(y) {
    // 3. FUNCTION EXECUTION CONTEXT (inner)
    const innerVar = "inner";
    console.log(x, y, outerVar, innerVar, globalVar);
  }

  inner(20);
}

outer(10);
```

### Execution Context Phases

**What this demonstrates:** Execution context is created in two phases - Creation Phase and Execution Phase.

```javascript
// CREATION PHASE:
// 1. Create scope chain
// 2. Create variables, functions, arguments (hoisting)
// 3. Determine value of 'this'

// EXECUTION PHASE:
// 1. Assign values to variables
// 2. Execute code line by line

function example() {
  console.log(a); // undefined (hoisted but not assigned)
  console.log(b); // ReferenceError (let/const not hoisted same way)

  var a = 10;
  let b = 20;
}

// During creation phase:
// - var a is hoisted and initialized to undefined
// - let b is hoisted but in "temporal dead zone"
```

---

## 4. Call Stack

**What this demonstrates:** JavaScript uses a stack to track function execution order (Last In, First Out).

### Basic Call Stack

```javascript
function first() {
  console.log("First function");
  second();
  console.log("First function end");
}

function second() {
  console.log("Second function");
  third();
  console.log("Second function end");
}

function third() {
  console.log("Third function");
}

first();

/*
Call Stack Order:
1. first() pushed
2. second() pushed (called from first)
3. third() pushed (called from second)
4. third() completes, popped
5. second() completes, popped
6. first() completes, popped

Output:
"First function"
"Second function"
"Third function"
"Second function end"
"First function end"
*/
```

### Stack Overflow

**What this demonstrates:** Too many function calls (usually from infinite recursion) cause stack overflow.

```javascript
// Stack overflow example (DON'T RUN THIS!)
function infiniteRecursion() {
  infiniteRecursion(); // Calls itself forever
}

// infiniteRecursion();  // ✗ RangeError: Maximum call stack size exceeded

// Proper recursion with base case
function countdown(n) {
  if (n <= 0) return; // Base case stops recursion
  console.log(n);
  countdown(n - 1);
}

countdown(5); // 5, 4, 3, 2, 1
```

---

## 5. The `this` Keyword

**What this demonstrates:** `this` refers to different objects depending on how a function is called.

### Global Context

```javascript
console.log(this); // window (in browsers) or global (in Node.js)

function globalFunction() {
  console.log(this); // window (non-strict mode) or undefined (strict mode)
}

globalFunction();

("use strict");
function strictFunction() {
  console.log(this); // undefined (in strict mode)
}

strictFunction();
```

### Object Method Context

**What this demonstrates:** When called as an object method, `this` refers to that object.

```javascript
const person = {
  name: "John",
  age: 30,
  greet: function () {
    console.log(this); // person object
    console.log(`Hello, I'm ${this.name}`);
  },
  getName() {
    return this.name; // 'this' is person
  },
};

person.greet(); // "Hello, I'm John"
console.log(person.getName()); // "John"
```

**Issue: Losing `this` Context**

```javascript
const person = {
  name: "John",
  greet: function () {
    console.log(`Hello, I'm ${this.name}`);
  },
};

const greet = person.greet; // Assigning method to variable
greet(); // "Hello, I'm undefined" (this is window/undefined)

// Solution: Use bind
const boundGreet = person.greet.bind(person);
boundGreet(); // "Hello, I'm John"
```

### Constructor Context

**What this demonstrates:** In constructors (with `new`), `this` refers to the newly created object.

```javascript
function Person(name, age) {
  this.name = name; // 'this' is the new object
  this.age = age;
  this.greet = function () {
    console.log(`Hello, I'm ${this.name}`);
  };
}

const john = new Person("John", 30);
console.log(john.name); // "John"
john.greet(); // "Hello, I'm John"
```

### Arrow Functions and `this`

**What this demonstrates:** Arrow functions don't have their own `this` - they inherit it from the enclosing lexical scope.

```javascript
const person = {
  name: "John",

  // Regular function - has its own 'this'
  regularGreet: function () {
    console.log(this.name); // "John"
  },

  // Arrow function - inherits 'this' from surrounding scope
  arrowGreet: () => {
    console.log(this.name); // undefined (this is global)
  },

  // Nested functions
  delayedGreet: function () {
    // Regular function in setTimeout loses 'this'
    setTimeout(function () {
      console.log(this.name); // undefined (this is window)
    }, 100);

    // Arrow function preserves 'this'
    setTimeout(() => {
      console.log(this.name); // "John" (inherits from delayedGreet)
    }, 100);
  },
};

person.regularGreet(); // "John"
person.arrowGreet(); // undefined
person.delayedGreet();
```

**Practical Arrow Function Example:**

```javascript
class Counter {
  constructor() {
    this.count = 0;
  }

  // Arrow function preserves 'this'
  increment = () => {
    this.count++;
    console.log(this.count);
  };

  // Regular method
  incrementRegular() {
    this.count++;
    console.log(this.count);
  }
}

const counter = new Counter();

// Works fine
counter.increment(); // 1

// Loses context with regular method
const inc = counter.incrementRegular;
// inc();  // ✗ Error: Cannot read property 'count' of undefined

// Arrow function preserves context
const incArrow = counter.increment;
incArrow(); // 2 (works!)
```

---

## 6. Explicit Binding (call, apply, bind)

### call() Method

**What this demonstrates:** `call()` invokes a function with a specified `this` value and individual arguments.

```javascript
function greet(greeting, punctuation) {
  console.log(`${greeting}, I'm ${this.name}${punctuation}`);
}

const person1 = { name: "John" };
const person2 = { name: "Jane" };

// Call function with different 'this' values
greet.call(person1, "Hello", "!"); // "Hello, I'm John!"
greet.call(person2, "Hi", "."); // "Hi, I'm Jane."
```

### apply() Method

**What this demonstrates:** `apply()` is like `call()` but takes arguments as an array.

```javascript
function introduce(greeting, age, city) {
  console.log(`${greeting}, I'm ${this.name}, ${age} years old from ${city}`);
}

const person = { name: "John" };

// apply takes arguments as array
introduce.apply(person, ["Hello", 30, "NYC"]);
// "Hello, I'm John, 30 years old from NYC"

// Useful with arrays
const numbers = [5, 2, 9, 1, 7];
const max = Math.max.apply(null, numbers);
console.log(max); // 9

// Modern alternative: spread operator
const maxSpread = Math.max(...numbers);
console.log(maxSpread); // 9
```

### bind() Method

**What this demonstrates:** `bind()` creates a new function with a fixed `this` value (doesn't invoke immediately).

```javascript
const person = {
  name: "John",
  greet: function (greeting) {
    console.log(`${greeting}, I'm ${this.name}`);
  },
};

const greet = person.greet;
// greet("Hello");  // ✗ Error (lost 'this')

// bind creates new function with fixed 'this'
const boundGreet = person.greet.bind(person);
boundGreet("Hello"); // ✓ "Hello, I'm John"

// Partial application with bind
function multiply(a, b) {
  return a * b;
}

const double = multiply.bind(null, 2); // Fix first argument to 2
console.log(double(5)); // 10 (2 * 5)
console.log(double(10)); // 20 (2 * 10)
```

**Practical bind() Example:**

```javascript
class Button {
  constructor(label) {
    this.label = label;
    this.clicks = 0;
  }

  handleClick() {
    this.clicks++;
    console.log(`${this.label} clicked ${this.clicks} times`);
  }
}

const button = new Button("Submit");

// In event listeners, need to bind 'this'
// document.querySelector('button').addEventListener('click', button.handleClick);
// ✗ Would lose 'this'

// Correct: bind 'this'
const boundHandler = button.handleClick.bind(button);
// document.querySelector('button').addEventListener('click', boundHandler);
// ✓ Preserves 'this'

// Or use arrow function
// document.querySelector('button').addEventListener('click', () => button.handleClick());
```

---

## 7. Closures

**What this demonstrates:** A closure is a function that has access to variables from its outer (enclosing) function's scope, even after the outer function has finished executing.

### Basic Closure

```javascript
function outer() {
  const outerVar = "I'm from outer";

  function inner() {
    console.log(outerVar); // Can access outerVar
  }

  return inner;
}

const myFunction = outer(); // outer() has finished executing
myFunction(); // "I'm from outer" (still has access!)
```

### Practical Closure Examples

**Private Variables:**

```javascript
function createBankAccount(initialBalance) {
  let balance = initialBalance; // Private variable

  return {
    deposit(amount) {
      balance += amount;
      return balance;
    },
    withdraw(amount) {
      if (amount > balance) {
        return "Insufficient funds";
      }
      balance -= amount;
      return balance;
    },
    getBalance() {
      return balance;
    },
  };
}

const account = createBankAccount(1000);
console.log(account.getBalance()); // 1000
account.deposit(500);
console.log(account.getBalance()); // 1500
account.withdraw(200);
console.log(account.getBalance()); // 1300
// console.log(account.balance);  // undefined (truly private!)
```

**Function Factory:**

```javascript
function createMultiplier(multiplier) {
  return function (number) {
    return number * multiplier;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
console.log(double(10)); // 20
```

**Event Handlers with Closures:**

```javascript
function setupButtons() {
  for (let i = 1; i <= 3; i++) {
    // Closure captures current value of 'i'
    setTimeout(() => {
      console.log(`Button ${i} clicked`);
    }, i * 1000);
  }
}

setupButtons();
// After 1s: "Button 1 clicked"
// After 2s: "Button 2 clicked"
// After 3s: "Button 3 clicked"

// With var (wrong) - closure captures reference, not value
function setupButtonsWrong() {
  for (var i = 1; i <= 3; i++) {
    setTimeout(() => {
      console.log(`Button ${i} clicked`); // All show 4!
    }, i * 1000);
  }
}

setupButtonsWrong();
// After 1-3s: All show "Button 4 clicked" (var is function-scoped)
```

---

## Quick Reference

### Scope Types

| Scope Type | Declared With         | Accessibility        |
| ---------- | --------------------- | -------------------- |
| Global     | Outside functions     | Everywhere           |
| Function   | `var` in function     | Within function only |
| Block      | `let`/`const` in `{}` | Within block only    |

### `this` Binding Rules

| Context               | `this` refers to                      |
| --------------------- | ------------------------------------- |
| Global                | `window` (browser) or `global` (Node) |
| Object method         | The object                            |
| Constructor (`new`)   | New object being created              |
| Arrow function        | Inherited from enclosing scope        |
| `call`/`apply`/`bind` | Explicitly specified object           |

### call vs apply vs bind

```javascript
const obj = { name: "John" };

function greet(greeting, punctuation) {
  return `${greeting}, ${this.name}${punctuation}`;
}

// call - invoke immediately with individual arguments
greet.call(obj, "Hello", "!"); // "Hello, John!"

// apply - invoke immediately with array of arguments
greet.apply(obj, ["Hello", "!"]); // "Hello, John!"

// bind - create new function with fixed 'this' (doesn't invoke)
const boundGreet = greet.bind(obj);
boundGreet("Hello", "!"); // "Hello, John!"
```

### Scope Chain Lookup

```javascript
// Order of variable lookup:
// 1. Current scope
// 2. Outer scope (parent function)
// 3. Outer scope's outer scope
// 4. ... continue up the chain
// 5. Global scope
// 6. ReferenceError (if not found)

const global = "global";

function level1() {
  const l1 = "level1";

  function level2() {
    const l2 = "level2";
    console.log(l2); // Found in level2
    console.log(l1); // Found in level1
    console.log(global); // Found in global
  }
}
```

### Temporal Dead Zone (TDZ)

```javascript
// With let/const
console.log(x); // ✗ ReferenceError (in TDZ)
let x = 10;

// With var
console.log(y); // undefined (hoisted)
var y = 10;

// TDZ: Time between entering scope and declaration
function example() {
  // TDZ starts here for 'temp'
  console.log(temp); // ✗ ReferenceError
  let temp = 5; // TDZ ends here
  console.log(temp); // ✓ 5
}
```

### Common Patterns

```javascript
// IIFE (Immediately Invoked Function Expression) - creates scope
(function () {
  const private = "hidden";
  console.log(private);
})();
// console.log(private);  // ✗ Not accessible

// Module pattern with closure
const module = (function () {
  let privateVar = 0;

  return {
    increment() {
      privateVar++;
    },
    getValue() {
      return privateVar;
    },
  };
})();

module.increment();
console.log(module.getValue()); // 1

// Arrow function preserving 'this'
class Timer {
  constructor() {
    this.seconds = 0;
  }

  start() {
    setInterval(() => {
      this.seconds++; // Arrow function preserves 'this'
    }, 1000);
  }
}
```

---

**That's Section 8!** You now understand scope, context, execution, and the `this` keyword - essential concepts for mastering JavaScript! 🎯
