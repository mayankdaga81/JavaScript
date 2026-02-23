# Section 3: Objects

## 1. Object Literals

### Creating Objects

```javascript
// Empty object
const obj = {};
const obj2 = new Object(); // Same, but avoid this

// Object with properties
const person = {
  name: "John",
  age: 30,
  isEmployed: true,
};

// Nested objects
const user = {
  name: "John",
  address: {
    city: "NYC",
    zip: 10001,
  },
};
```

### Accessing Properties

```javascript
const person = { name: "John", age: 30 };

// Dot notation
person.name; // "John"

// Bracket notation (dynamic access)
person["age"]; // 30
const prop = "name";
person[prop]; // "John"

// Bracket notation required for:
const obj = {
  "full name": "John Doe", // Space in key
  123: "numbers", // Starting with number
};
obj["full name"];
obj["123"];
```

### Modifying Objects

```javascript
const person = { name: "John" };

// Add property
person.age = 30;
person["city"] = "NYC";

// Update property
person.name = "Jane";

// Delete property
delete person.age; // true (removed)
delete person.xyz; // true (doesn't exist, no error)

// Check if property exists
"name" in person; // true
person.hasOwnProperty("name"); // true
person.age === undefined; // true (but not reliable)
```

---

## 2. Object Properties & Methods

### Property Shorthand (ES6)

```javascript
const name = "John";
const age = 30;

// Old way
const person = { name: name, age: age };

// Shorthand
const person = { name, age }; // Same as above
```

### Method Shorthand (ES6)

```javascript
// Old way
const person = {
  greet: function () {
    return "Hello";
  },
};

// Shorthand
const person = {
  greet() {
    return "Hello";
  },
};
```

### Computed Property Names (ES6)

```javascript
const key = "name";
const person = {
  [key]: "John", // name: "John"
  ["age"]: 30, // age: 30
  ["get" + "Name"]() {
    // getName() method
    return this.name;
  },
};
```

### Property Descriptors

```javascript
const person = { name: "John" };

Object.defineProperty(person, "age", {
  value: 30,
  writable: false, // Can't be changed
  enumerable: true, // Shows in for...in
  configurable: false, // Can't be deleted
});

person.age = 40; // Silently fails (strict mode: error)
delete person.age; // Silently fails

// Get descriptor
Object.getOwnPropertyDescriptor(person, "age");
// { value: 30, writable: false, enumerable: true, configurable: false }
```

---

## 3. this Keyword

The value of `this` depends on **how the function is called**.

### In Object Methods

```javascript
const person = {
  name: "John",
  greet() {
    return `Hello, ${this.name}`; // this = person
  },
};

person.greet(); // "Hello, John"
```

### Lost Context

```javascript
const person = {
  name: "John",
  greet() {
    return `Hello, ${this.name}`;
  },
};

const greetFn = person.greet;
greetFn(); // "Hello, undefined" (this = global/undefined in strict)
```

### Arrow Functions Don't Have `this`

```javascript
const person = {
  name: "John",
  greet: () => {
    return `Hello, ${this.name}`; // this from outer scope (not person)
  },
  delayedGreet() {
    setTimeout(() => {
      console.log(this.name); // "John" (inherits from delayedGreet)
    }, 1000);
  },
};
```

### Global Context

```javascript
console.log(this); // window (browser) or global (Node.js)

function test() {
  console.log(this); // window/global (non-strict), undefined (strict)
}
```

### In Event Handlers

```javascript
button.addEventListener("click", function () {
  console.log(this); // the button element
});

button.addEventListener("click", () => {
  console.log(this); // window/global (arrow function)
});
```

### Explicit Binding: call, apply, bind

#### call()

Invokes function with specific `this` and arguments.

```javascript
function greet(greeting, punctuation) {
  return `${greeting}, ${this.name}${punctuation}`;
}

const person = { name: "John" };
greet.call(person, "Hello", "!"); // "Hello, John!"
```

#### apply()

Like `call()` but arguments as array.

```javascript
greet.apply(person, ["Hello", "!"]); // "Hello, John!"

// Useful for spreading
const numbers = [5, 6, 2, 3, 7];
Math.max.apply(null, numbers); // 7
// Modern: Math.max(...numbers)
```

#### bind()

Returns new function with `this` permanently bound.

```javascript
const person = {
  name: "John",
  greet() {
    return `Hello, ${this.name}`;
  },
};

const greetFn = person.greet.bind(person);
greetFn(); // "Hello, John" (this is bound to person)

// Partial application
function multiply(a, b) {
  return a * b;
}
const double = multiply.bind(null, 2); // Fix first argument to 2
double(5); // 10
```

---

## 4. Constructors & new Keyword

### Constructor Functions

Convention: Start with capital letter.

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.greet = function () {
    return `Hello, ${this.name}`;
  };
}

const person1 = new Person("John", 30);
const person2 = new Person("Jane", 25);

person1.greet(); // "Hello, John"
```

### What `new` Does

```javascript
// When you call: new Person("John", 30)
// JavaScript does:
1. Creates empty object: {}
2. Sets prototype: {}.__proto__ = Person.prototype
3. Binds this: Person.call({}, "John", 30)
4. Returns the object (unless constructor explicitly returns object)
```

### Constructor Without `new` (Problem)

```javascript
function Person(name) {
  this.name = name; // this = window/global!
}

const person = Person("John"); // Forgot 'new'
console.log(person); // undefined
console.log(window.name); // "John" (polluted global!)

// Solution: Check with new.target (ES6)
function Person(name) {
  if (!new.target) {
    return new Person(name);
  }
  this.name = name;
}
```

---

## 5. Prototypes & Prototype Chain

### Understanding Prototypes

Every object has an internal `[[Prototype]]` link (accessed via `__proto__`).

```javascript
function Person(name) {
  this.name = name;
}

// Add method to prototype (shared by all instances)
Person.prototype.greet = function () {
  return `Hello, ${this.name}`;
};

const person1 = new Person("John");
const person2 = new Person("Jane");

person1.greet(); // "Hello, John"
person2.greet(); // "Hello, Jane"

// Both share the SAME greet method
person1.greet === person2.greet; // true
```

### Prototype vs Instance Properties

```javascript
function Person(name) {
  // Instance property (each object has its own)
  this.name = name;
}

// Prototype property (shared)
Person.prototype.species = "human";

const person1 = new Person("John");
const person2 = new Person("Jane");

person1.name; // "John" (own property)
person2.name; // "Jane" (own property)
person1.species; // "human" (from prototype)
person2.species; // "human" (from prototype)

// Modifying prototype affects all instances
Person.prototype.species = "homo sapiens";
person1.species; // "homo sapiens"

// Own property shadows prototype
person1.species = "alien";
person1.species; // "alien" (own property)
person2.species; // "homo sapiens" (still from prototype)
```

### Prototype Chain

```javascript
const person = { name: "John" };

// Chain: person -> Object.prototype -> null

person.toString(); // From Object.prototype
person.hasOwnProperty("name"); // From Object.prototype

// Check prototype
Object.getPrototypeOf(person); // Object.prototype
person.__proto__; // Same (but avoid __proto__)

// Check if property is own or inherited
person.hasOwnProperty("name"); // true (own)
person.hasOwnProperty("toString"); // false (inherited)
```

### Setting Prototype

```javascript
// Create object with specific prototype
const personProto = {
  greet() {
    return `Hello, ${this.name}`;
  },
};

const person = Object.create(personProto);
person.name = "John";
person.greet(); // "Hello, John"

// Check
Object.getPrototypeOf(person) === personProto; // true
```

### instanceof

```javascript
function Person(name) {
  this.name = name;
}

const person = new Person("John");

person instanceof Person; // true
person instanceof Object; // true (all objects inherit from Object)

// How it works: checks if Person.prototype is in person's prototype chain
```

---

## 6. Object Destructuring

### Basic Destructuring

```javascript
const person = { name: "John", age: 30, city: "NYC" };

// Extract properties
const { name, age } = person;
console.log(name); // "John"
console.log(age); // 30

// Remaining properties
const { name, ...rest } = person;
console.log(rest); // { age: 30, city: "NYC" }
```

### Renaming Variables

```javascript
const person = { name: "John", age: 30 };

const { name: firstName, age: years } = person;
console.log(firstName); // "John"
console.log(years); // 30
// console.log(name);    // ✗ ReferenceError
```

### Default Values

```javascript
const person = { name: "John" };

const { name, age = 25, city = "Unknown" } = person;
console.log(age); // 25 (default)
console.log(city); // "Unknown" (default)
```

### Nested Destructuring

```javascript
const person = {
  name: "John",
  address: {
    city: "NYC",
    zip: 10001,
  },
};

const {
  name,
  address: { city, zip },
} = person;
console.log(city); // "NYC"
console.log(zip); // 10001
// console.log(address);  // ✗ ReferenceError (not extracted)

// Extract both address and its properties
const {
  address,
  address: { city },
} = person;
console.log(address); // { city: "NYC", zip: 10001 }
console.log(city); // "NYC"
```

### Function Parameters

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

---

## 7. Object Methods

### Object.keys()

Returns array of own enumerable property names.

```javascript
const person = { name: "John", age: 30, city: "NYC" };
Object.keys(person); // ["name", "age", "city"]

// Iterate
Object.keys(person).forEach((key) => {
  console.log(`${key}: ${person[key]}`);
});
```

### Object.values()

Returns array of own enumerable property values.

```javascript
const person = { name: "John", age: 30, city: "NYC" };
Object.values(person); // ["John", 30, "NYC"]

// Sum all values
const scores = { math: 90, english: 85, science: 92 };
const total = Object.values(scores).reduce((sum, score) => sum + score, 0);
```

### Object.entries()

Returns array of [key, value] pairs.

```javascript
const person = { name: "John", age: 30 };
Object.entries(person); // [["name", "John"], ["age", 30]]

// Iterate
Object.entries(person).forEach(([key, value]) => {
  console.log(`${key}: ${value}`);
});

// Convert to Map
const map = new Map(Object.entries(person));
```

### Object.fromEntries()

Creates object from [key, value] pairs.

```javascript
const entries = [
  ["name", "John"],
  ["age", 30],
];
Object.fromEntries(entries); // { name: "John", age: 30 }

// Convert Map to object
const map = new Map([
  ["name", "John"],
  ["age", 30],
]);
Object.fromEntries(map); // { name: "John", age: 30 }

// Filter object
const person = { name: "John", age: 30, city: "NYC" };
const filtered = Object.fromEntries(
  Object.entries(person).filter(([key, value]) => typeof value === "string"),
);
// { name: "John", city: "NYC" }
```

### Object.assign()

Copies properties from source(s) to target (shallow copy).

```javascript
const target = { a: 1 };
const source = { b: 2, c: 3 };
Object.assign(target, source); // { a: 1, b: 2, c: 3 }
console.log(target); // { a: 1, b: 2, c: 3 } (mutated)

// Clone object (shallow)
const person = { name: "John", age: 30 };
const clone = Object.assign({}, person);

// Merge multiple objects
const obj1 = { a: 1 };
const obj2 = { b: 2 };
const obj3 = { c: 3 };
const merged = Object.assign({}, obj1, obj2, obj3);
// { a: 1, b: 2, c: 3 }

// Modern alternative: spread
const merged = { ...obj1, ...obj2, ...obj3 };
```

### Object.freeze()

Makes object immutable (shallow).

```javascript
const person = { name: "John", age: 30 };
Object.freeze(person);

person.age = 40; // Silently fails (strict mode: error)
person.city = "NYC"; // Silently fails
delete person.name; // Silently fails

// Check if frozen
Object.isFrozen(person); // true

// Shallow freeze only
const person = {
  name: "John",
  address: { city: "NYC" },
};
Object.freeze(person);
person.address.city = "LA"; // ✓ Works (nested not frozen)
```

### Object.seal()

Prevents adding/removing properties, but allows modification.

```javascript
const person = { name: "John", age: 30 };
Object.seal(person);

person.age = 40; // ✓ Works (can modify)
person.city = "NYC"; // Silently fails (can't add)
delete person.name; // Silently fails (can't delete)

Object.isSealed(person); // true
```

### Object.create()

Creates object with specified prototype.

```javascript
const personProto = {
  greet() {
    return `Hello, ${this.name}`;
  },
};

const person = Object.create(personProto);
person.name = "John";
person.greet(); // "Hello, John"

// Create object with null prototype (no inherited properties)
const bareObj = Object.create(null);
bareObj.toString(); // ✗ TypeError (no inherited methods)
```

### Object.hasOwn() (ES2022)

Safer alternative to `hasOwnProperty`.

```javascript
const person = { name: "John" };

Object.hasOwn(person, "name"); // true
Object.hasOwn(person, "toString"); // false

// vs hasOwnProperty (can be overridden)
const obj = {
  hasOwnProperty() {
    return false;
  },
  name: "John",
};
obj.hasOwnProperty("name"); // false (overridden)
Object.hasOwn(obj, "name"); // true (reliable)
```

---

## Quick Reference

### Object Creation & Access

```javascript
const obj = { key: "value" }; // Literal
obj.key; // Dot notation
obj["key"]; // Bracket notation
"key" in obj; // Check existence
obj.hasOwnProperty("key"); // Own property
Object.hasOwn(obj, "key"); // Modern check
```

### ES6 Features

```javascript
const person = { name, age }; // Shorthand
const person = { greet() {} }; // Method shorthand
const obj = { [key]: value }; // Computed property
const { name, age } = person; // Destructuring
const { name: n } = person; // Rename
const { x = 10 } = obj; // Default
```

### this Binding

```javascript
obj.method(); // this = obj
fn(); // this = global/undefined
new Fn(); // this = new object
fn.call(obj, args); // this = obj
fn.apply(obj, [args]); // this = obj
fn.bind(obj); // Returns bound function
```

### Key Object Methods

```javascript
Object.keys(obj); // ["key1", "key2"]
Object.values(obj); // [value1, value2]
Object.entries(obj); // [["key1", value1], ...]
Object.fromEntries(arr); // Create from entries
Object.assign(t, s); // Copy/merge (shallow)
Object.create(proto); // Create with prototype
Object.freeze(obj); // Immutable (shallow)
Object.seal(obj); // No add/delete, can modify
```

### Prototype

```javascript
Object.getPrototypeOf(obj); // Get prototype
Object.setPrototypeOf(obj, proto); // Set prototype
obj instanceof Constructor; // Check prototype chain
obj.hasOwnProperty(prop); // Check own property
```

---

