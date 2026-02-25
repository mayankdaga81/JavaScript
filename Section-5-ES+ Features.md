# Section 5: ES6+ Features

## 1. Template Literals

### Basic Syntax

Use backticks (`) instead of quotes.

```javascript
// Old way
const str = "Hello, " + name + "!";

// Template literal
const str = `Hello, ${name}!`;
```

### String Interpolation

```javascript
const name = "John";
const age = 30;

`My name is ${name} and I am ${age} years old`;
// "My name is John and I am 30 years old"

// Expressions
`2 + 2 = ${2 + 2}`; // "2 + 2 = 4"
`Price: $${price * 1.1}`; // Calculate in template

// Function calls
`Hello ${getName()}!`;

// Ternary operators
`You are ${age >= 18 ? "an adult" : "a minor"}`;
```

### Multi-line Strings

```javascript
// Old way
const str = "Line 1\n" + "Line 2\n" + "Line 3";

// Template literal (preserves formatting)
const str = `Line 1
Line 2
Line 3`;

// HTML templates
const html = `
  <div class="card">
    <h2>${title}</h2>
    <p>${content}</p>
  </div>
`;
```

### Nested Templates

```javascript
const users = [
  { name: "John", active: true },
  { name: "Jane", active: false },
];

const html = `
  <ul>
    ${users
      .map(
        (user) => `
      <li class="${user.active ? "active" : "inactive"}">
        ${user.name}
      </li>
    `,
      )
      .join("")}
  </ul>
`;
```

### Tagged Templates (Advanced)

Custom string processing.

```javascript
function highlight(strings, ...values) {
  return strings.reduce((result, str, i) => {
    return `${result}${str}<span>${values[i] || ""}</span>`;
  }, "");
}

const name = "John";
const age = 30;
highlight`Name: ${name}, Age: ${age}`;
// "Name: <span>John</span>, Age: <span>30</span>"

// Real use case: styled-components, SQL query builders
```

---

## 2. Destructuring (Comprehensive Review)

### Object Destructuring

#### Basic

```javascript
const person = { name: "John", age: 30, city: "NYC" };

const { name, age } = person;
console.log(name); // "John"
console.log(age); // 30
```

#### Renaming

```javascript
const { name: firstName, age: years } = person;
console.log(firstName); // "John"
console.log(years); // 30
```

#### Default Values

```javascript
const { name, country = "USA" } = person;
console.log(country); // "USA" (default)
```

#### Nested

```javascript
const user = {
  name: "John",
  address: {
    city: "NYC",
    zip: 10001,
  },
};

const {
  name,
  address: { city, zip },
} = user;
console.log(city); // "NYC"
```

#### Rest

```javascript
const person = { name: "John", age: 30, city: "NYC", country: "USA" };

const { name, ...rest } = person;
console.log(rest); // { age: 30, city: "NYC", country: "USA" }
```

#### Dynamic Keys

```javascript
const key = "name";
const { [key]: value } = person;
console.log(value); // "John"
```

### Array Destructuring

#### Basic

```javascript
const arr = [1, 2, 3, 4, 5];

const [first, second] = arr;
console.log(first); // 1
console.log(second); // 2
```

#### Skip Elements

```javascript
const [first, , third, , fifth] = arr;
console.log(third); // 3
console.log(fifth); // 5
```

#### Rest

```javascript
const [first, second, ...rest] = arr;
console.log(rest); // [3, 4, 5]
```

#### Default Values

```javascript
const [a = 1, b = 2, c = 3] = [10];
console.log(a); // 10
console.log(b); // 2 (default)
console.log(c); // 3 (default)
```

#### Swapping

```javascript
let a = 1,
  b = 2;
[a, b] = [b, a];
console.log(a); // 2
console.log(b); // 1
```

### Function Parameters

#### Object Destructuring

```javascript
function greet({ name, age = 0 }) {
  return `${name} is ${age} years old`;
}

greet({ name: "John", age: 30 }); // "John is 30 years old"
greet({ name: "Jane" }); // "Jane is 0 years old"

// With default object
function greet({ name = "Guest", age = 0 } = {}) {
  return `${name} is ${age} years old`;
}
greet(); // "Guest is 0 years old"
```

#### Array Destructuring

```javascript
function sum([a, b]) {
  return a + b;
}

sum([5, 10]); // 15
```

### Practical Examples

#### Configuration Objects

```javascript
function createUser({
  name,
  email,
  age = 18,
  isAdmin = false,
  preferences = {},
}) {
  return { name, email, age, isAdmin, preferences };
}
```

#### API Response

```javascript
const response = {
  data: {
    user: {
      id: 1,
      profile: {
        name: "John",
        email: "john@example.com",
      },
    },
  },
  status: 200,
};

const {
  data: {
    user: {
      profile: { name, email },
    },
  },
  status,
} = response;
```

#### Extracting from Arrays

```javascript
const [header, ...rows] = csvData;
const [first, ...rest] = items;
const [, second] = arr; // Skip first
```

---

## 3. Spread & Rest Operators (Comprehensive Review)

### Spread Operator (...)

Expands iterable into individual elements.

#### Arrays

##### Copying

```javascript
const arr = [1, 2, 3];
const copy = [...arr]; // Shallow copy
```

##### Merging

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const merged = [...arr1, ...arr2]; // [1, 2, 3, 4]

// Insert in middle
const result = [0, ...arr1, 2.5, ...arr2, 5];
// [0, 1, 2, 2.5, 3, 4, 5]
```

##### Function Arguments

```javascript
const numbers = [1, 2, 3, 4, 5];
Math.max(...numbers); // 5

// Spread multiple arrays
Math.min(...arr1, ...arr2);
```

##### Converting Iterables

```javascript
// String to array
[..."hello"]; // ["h", "e", "l", "l", "o"]

// Set to array
const set = new Set([1, 2, 3]);
[...set]; // [1, 2, 3]
```

#### Objects

##### Copying

```javascript
const obj = { a: 1, b: 2 };
const copy = { ...obj }; // Shallow copy
```

##### Merging

```javascript
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };
const merged = { ...obj1, ...obj2 };
// { a: 1, b: 2, c: 3, d: 4 }

// Later properties override
const obj3 = { b: 99 };
const result = { ...obj1, ...obj3 };
// { a: 1, b: 99 }
```

##### Adding/Overriding Properties

```javascript
const user = { name: "John", age: 30 };
const updated = { ...user, age: 31, city: "NYC" };
// { name: "John", age: 31, city: "NYC" }
```

##### Conditional Properties

```javascript
const condition = true;
const obj = {
  name: "John",
  ...(condition && { age: 30 }), // Only add if true
  ...(false && { city: "NYC" }), // Not added
};
// { name: "John", age: 30 }
```

### Rest Parameter (...)

Collects remaining elements into array.

#### Functions

```javascript
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}

sum(1, 2, 3, 4, 5); // 15

// With other parameters
function multiply(factor, ...numbers) {
  return numbers.map((n) => n * factor);
}

multiply(2, 1, 2, 3); // [2, 4, 6]
```

#### Destructuring

```javascript
// Arrays
const [first, ...rest] = [1, 2, 3, 4, 5];
console.log(first); // 1
console.log(rest); // [2, 3, 4, 5]

// Objects
const { name, ...rest } = { name: "John", age: 30, city: "NYC" };
console.log(rest); // { age: 30, city: "NYC" }
```

### Spread vs Rest: Key Difference

```javascript
// Spread: EXPANDS (right side of =)
const arr = [1, 2, 3];
const copy = [...arr]; // Expand into new array
fn(...arr); // Expand as arguments

// Rest: COLLECTS (left side of =)
const [first, ...rest] = arr; // Collect remaining
function fn(...args) {} // Collect arguments
```

---

## 4. Enhanced Object Literals

All ES6+ improvements to object syntax.

### Property Shorthand

```javascript
const name = "John";
const age = 30;

// Old
const person = { name: name, age: age };

// Shorthand
const person = { name, age };
```

### Method Shorthand

```javascript
// Old
const obj = {
  greet: function () {
    return "Hello";
  },
};

// Shorthand
const obj = {
  greet() {
    return "Hello";
  },
};
```

### Computed Property Names

```javascript
const key = "name";
const dynamicKey = "get" + "Name";

const obj = {
  [key]: "John", // name: "John"
  ["age"]: 30, // age: 30
  [dynamicKey]() {
    // getName() method
    return this.name;
  },
  [`${key}Upper`]: "JOHN", // nameUpper: "JOHN"
};
```

### Combining Features

```javascript
const id = 1;
const name = "John";
const getAge = () => 30;

const user = {
  id, // Shorthand
  name, // Shorthand
  greet() {
    // Method shorthand
    return `Hello, ${this.name}`;
  },
  [Symbol.toStringTag]: "User", // Computed + symbol
  age: getAge(), // Function call
};
```

---

## 5. Optional Chaining (?.)

Safely access nested properties without errors.

### Object Properties

```javascript
const user = {
  name: "John",
  address: {
    city: "NYC",
  },
};

// Old way
const city = user && user.address && user.address.city;

// Optional chaining
const city = user?.address?.city; // "NYC"
const zip = user?.address?.zip; // undefined (no error!)

// Without optional chaining
// const zip = user.contact.phone;  // ✗ TypeError (contact is undefined)

// With optional chaining
const phone = user?.contact?.phone; // undefined (safe)
```

### Array Access

```javascript
const users = [{ name: "John" }, { name: "Jane" }];

users?.[0]?.name; // "John"
users?.[5]?.name; // undefined (safe)
users?.[0]?.age; // undefined (safe)
```

### Function Calls

```javascript
const user = {
  name: "John",
  greet() {
    return `Hello, ${this.name}`;
  },
};

user.greet?.(); // "Hello, John"
user.goodbye?.(); // undefined (method doesn't exist, no error!)
user.greet?.().toUpperCase(); // "HELLO, JOHN"

// Conditional method call - Syntax - This is useful when you’re not sure if a method exists on an object and want to avoid runtime errors.
const result = obj.method?.() || "default";
```

### Complex Chains

```javascript
const data = {
  users: [
    {
      profile: {
        getName() {
          return "John";
        },
      },
    },
  ],
};

data?.users?.[0]?.profile?.getName?.(); // "John"
data?.users?.[5]?.profile?.getName?.(); // undefined (safe)
```

### With Nullish Coalescing

```javascript
// With the nullish coalescing operator (??), if the value on the left is null or undefined, the value on the right is returned.
const name = user?.profile?.name ?? "Guest";
const age = user?.profile?.age ?? 0;

// Practical example
const displayName =
  data?.user?.profile?.displayName ?? data?.user?.name ?? "Anonymous";
```

### Common Patterns

```javascript
// API response
const email = response?.data?.user?.email;

// Configuration
const timeout = config?.server?.timeout ?? 3000;

// Event handlers
element?.addEventListener?.("click", handler);

// Callback functions
onSuccess?.(data);
```

---

## 6. Nullish Coalescing (??)

Returns right operand when left is `null` or `undefined` only.

### Basic Usage

```javascript
const value = null ?? "default"; // "default"
const value = undefined ?? "default"; // "default"
const value = 0 ?? "default"; // 0 (NOT default!)
const value = "" ?? "default"; // "" (NOT default!)
const value = false ?? "default"; // false (NOT default!)
```

### vs OR Operator (||)

```javascript
// OR (||) - returns right for ANY falsy value
0 || "default"; // "default" (0 is falsy)
"" || "default"; // "default" ("" is falsy)
false || "default"; // "default" (false is falsy)

// Nullish (??) - returns right ONLY for null/undefined
0 ?? "default"; // 0
"" ?? "default"; // ""
false ?? "default"; // false
null ?? "default"; // "default"
undefined ?? "default"; // "default"
```

### When to Use Each

```javascript
// Use ?? when 0, "", false are valid values
const port = config.port ?? 3000; // 0 is a valid port
const name = user.name ?? "Guest"; // "" might be intentional
const flag = settings.flag ?? true; // false is valid

// Use || for general default values
const count = items.length || 0;
const message = error || "Unknown error";
```

### Practical Examples

#### Configuration

```javascript
const config = {
  timeout: 0, // 0 is valid (no timeout)
  retries: null, // null means not set
};

const timeout = config.timeout ?? 5000; // 0 (keeps 0)
const retries = config.retries ?? 3; // 3 (null replaced)

// vs OR
const timeout = config.timeout || 5000; // 5000 (wrongly replaces 0!)
```

#### User Input

```javascript
const userInput = getUserInput(); // Could be "", 0, false

// Want to allow empty string, 0, false
const value = userInput ?? getDefaultValue();

// vs
const value = userInput || getDefaultValue(); // Would replace valid inputs
```

#### API Response

```javascript
const response = await fetch("/api/data");
const data = await response.json();

const count = data.count ?? 0; // 0 is valid count
const message = data.message ?? ""; // "" is valid message
```

### Chaining

```javascript
const value = a ?? b ?? c ?? "default";
// Returns first non-null/undefined value

const name = user?.profile?.name ?? user?.name ?? "Guest";
```

### Assignment with Nullish Coalescing (??=)

```javascript
let config = { timeout: null };

// Old way
config.timeout = config.timeout ?? 3000;

// Nullish coalescing assignment
config.timeout ??= 3000; // Only assigns if null/undefined

// More examples
let x = null;
x ??= 10; // x = 10
x ??= 20; // x = 10 (not null, not assigned)

let y = 0;
y ??= 10; // y = 0 (not null/undefined, not assigned)
```

---

## Quick Reference

### Template Literals

```javascript
`Hello ${name}` // String interpolation
`Line 1
Line 2`; // Multi-line
tag`Hello ${name}`; // Tagged template
```

### Destructuring

```javascript
// Object
const { name, age } = person;
const { name: n } = person; // Rename
const { age = 0 } = person; // Default
const { name, ...rest } = person; // Rest

// Array
const [a, b] = arr;
const [a, , c] = arr; // Skip
const [a, ...rest] = arr; // Rest
[a, b] = [b, a]; // Swap
```

### Spread & Rest

```javascript
// Spread (expand)
[...arr]                    // Copy array
{...obj}                    // Copy object
[...arr1, ...arr2]          // Merge arrays
{...obj1, ...obj2}          // Merge objects
fn(...arr)                  // Spread as arguments

// Rest (collect)
function fn(...args) {}     // Collect arguments
const [a, ...rest] = arr;   // Collect remaining
const {x, ...rest} = obj;   // Collect remaining
```

### Enhanced Object Literals

```javascript
{ name }                    // Property shorthand
{ greet() {} }              // Method shorthand
{ [key]: value }            // Computed property
```

### Optional Chaining & Nullish Coalescing

```javascript
obj?.prop; // Safe property access
obj?.[key]; // Safe bracket notation
obj?.method?.(); // Safe method call
arr?.[0]?.prop; // Safe array access

value ?? "default"; // Default for null/undefined only
value || "default"; // Default for any falsy value
x ??= 10; // Assign if null/undefined
```

### Common Patterns

```javascript
// Clone with modifications
const updated = { ...obj, age: 31 };

// Conditional properties
const obj = {
  name,
  ...(condition && { age: 30 }),
};

// Safe deep access with default
const value = data?.users?.[0]?.name ?? "Guest";

// Remove property
const { password, ...rest } = user;

// Function with flexible params
function fn({ name, age = 0 } = {}) {}
```

---
