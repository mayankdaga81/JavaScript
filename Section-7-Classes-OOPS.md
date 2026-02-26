# Section 7: Classes & OOP

## Key Definitions

### What is a Class?

A **class** is a blueprint/template for creating objects with predefined properties and methods. It's a way to bundle data (properties) and functionality (methods) together. Classes make it easier to create multiple objects with the same structure.

### What is a Constructor?

A **constructor** is a special method that automatically runs when you create a new instance of a class using the `new` keyword. It's used to initialize the object's properties and set up the initial state.

### Is Constructor Mandatory?

**No**, constructor is optional. If you don't define a constructor, JavaScript automatically provides a default empty constructor.

### What is the Default Constructor?

The **default constructor** is an empty constructor `constructor() {}` that JavaScript uses if you don't define one. For child classes, the default constructor automatically calls `super()` with all arguments.

```javascript
// Without explicit constructor
class MyClass {
  greet() {
    return "Hello";
  }
}

// JavaScript treats it as:
class MyClass {
  constructor() {} // Default constructor
  greet() {
    return "Hello";
  }
}

// For child classes:
class Child extends Parent {
  // Default: constructor(...args) { super(...args); }
}
```

---

## 1. Class Syntax

**What this demonstrates:** ES6 classes provide a cleaner, more intuitive syntax for creating objects and handling inheritance compared to constructor functions and prototypes.

### Basic Class

```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `Hello, my name is ${this.name}`;
  }

  getAge() {
    return this.age;
  }
}

// Creating instances
const person1 = new Person("John", 30);
const person2 = new Person("Jane", 25);

person1.greet(); // "Hello, my name is John"
person1.getAge(); // 30
```

---

## 2. Constructor

**What this demonstrates:** The constructor is a special method that runs when creating a new instance. Used for initializing object properties.

### Basic Constructor

```javascript
class User {
  constructor(username, email) {
    // Instance properties
    this.username = username;
    this.email = email;
    this.createdAt = new Date();
  }
}

const user = new User("john_doe", "john@example.com");
console.log(user.username); // "john_doe"
console.log(user.createdAt); // Current date
```

### Constructor with Validation

**What this demonstrates:** Adding validation logic in the constructor to ensure data integrity.

```javascript
class User {
  constructor(username, email, age) {
    if (!username || username.length < 3) {
      throw new Error("Username must be at least 3 characters");
    }

    if (!email.includes("@")) {
      throw new Error("Invalid email format");
    }

    if (age < 0 || age > 120) {
      throw new Error("Invalid age");
    }

    this.username = username;
    this.email = email;
    this.age = age;
  }
}

// const user = new User("ab", "invalid");  // ✗ Error thrown
const user = new User("john_doe", "john@example.com", 30); // ✓ Works
```

### Default Parameters in Constructor

```javascript
class Product {
  constructor(name, price = 0, quantity = 1) {
    this.name = name;
    this.price = price;
    this.quantity = quantity;
  }

  getTotalPrice() {
    return this.price * this.quantity;
  }
}

const product1 = new Product("Laptop", 999, 2);
const product2 = new Product("Mouse"); // Uses defaults
console.log(product2.price); // 0
console.log(product2.quantity); // 1
```

### Constructor with Object Parameter

**What this demonstrates:** Using destructuring in constructor for more flexible initialization.

```javascript
class User {
  constructor({ username, email, role = "user", isActive = true }) {
    this.username = username;
    this.email = email;
    this.role = role;
    this.isActive = isActive;
  }
}

const user = new User({
  username: "john_doe",
  email: "john@example.com",
  role: "admin",
  // isActive uses default
});
```

### Optional Constructor

**What this demonstrates:** Constructor is optional. If not defined, a default empty constructor is used.

```javascript
// No explicit constructor
class SimpleClass {
  greet() {
    return "Hello";
  }
}

// Equivalent to:
class SimpleClass {
  constructor() {
    // Empty default constructor
  }

  greet() {
    return "Hello";
  }
}

const obj = new SimpleClass(); // ✓ Works
```

---

## 3. Methods & Properties

### Instance Methods

**What this demonstrates:** Methods defined in the class are added to the prototype and shared by all instances. 
Method chaining is a pattern where you call multiple methods on the same object in a single statement. 
Each method returns this (the object itself), allowing you to chain the next method call.
The key is: return this from each method to enable chaining.

```javascript
class Calculator {
  constructor(value = 0) {
    this.value = value;
  }

  add(num) {
    this.value += num;
    return this; // Return 'this' for method chaining
  }

  subtract(num) {
    this.value -= num;
    return this;
  }

  multiply(num) {
    this.value *= num;
    return this;
  }

  getResult() {
    return this.value;
  }
}

const calc = new Calculator(10);
calc.add(5).multiply(2).subtract(10);
console.log(calc.getResult()); // 20

// Method chaining works because each method returns 'this'
```

### Instance Properties

**What this demonstrates:** Two ways to define instance properties - in constructor or using class fields (ES2022).

```javascript
// Method 1: In constructor
class User {
  constructor(name) {
    this.name = name;
    this.posts = [];
    this.followers = 0;
  }
}

// Method 2: Class fields (ES2022)
class User {
  // Public instance fields
  posts = [];
  followers = 0;

  constructor(name) {
    this.name = name;
  }
}

// Both create the same result
const user = new User("John");
console.log(user.posts); // []
console.log(user.followers); // 0
```

### Computed Method Names

**What this demonstrates:** Using computed property names for methods.

```javascript
const methodName = "greet";
const prefix = "get";

class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  [methodName]() {
    return `Hello, ${this.name}`;
  }

  [`${prefix}Age`]() {
    return this.age;
  }
}

const person = new Person("John", 30);
person.greet(); // "Hello, John"
person.getAge(); // 30
```

---

## 4. Inheritance (extends, super)

**What this demonstrates:** Classes can inherit from other classes using `extends`, and `super` is used to call the parent class constructor and methods.
- The extends keyword creates a child class that inherits from a parent class. The child class automatically gains access to all public properties and methods of the parent class, and can add new ones or override existing ones.
- The super keyword is used in child classes to access and call the parent class's constructor and methods. In the constructor, super() must be called before using this, and in methods, super.methodName() calls the parent's version of that method.

### Basic Inheritance

```javascript
// Parent class (base class / superclass)
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    return `${this.name} makes a sound`;
  }

  sleep() {
    return `${this.name} is sleeping`;
  }
}

// Child class (derived class / subclass)
class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Call parent constructor
    this.breed = breed;
  }

  // Override parent method
  speak() {
    return `${this.name} barks`;
  }

  // Add new method
  fetch() {
    return `${this.name} fetches the ball`;
  }
}

const dog = new Dog("Buddy", "Golden Retriever");
console.log(dog.speak()); // "Buddy barks" (overridden)
console.log(dog.sleep()); // "Buddy is sleeping" (inherited)
console.log(dog.fetch()); // "Buddy fetches the ball" (new)
console.log(dog.breed); // "Golden Retriever"
```

### super Keyword

**What this demonstrates:** `super` is used in two ways - to call parent constructor and to access parent methods.
- You inherit methods automatically ✓
- You DON'T inherit property initialization automatically ✗
- super() runs the parent constructor to initialize parent properties.
  - In the child class constructor, you must call super(parameters) with whatever parameters the immediate parent's constructor expects. This initializes the parent's properties so they're available to use.

```javascript
class Animal {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  describe() {
    return `${this.name} is ${this.age} years old`;
  }
}

class Dog extends Animal {
  constructor(name, age, breed) {
    // super() MUST be called before using 'this'
    super(name, age); // Call parent constructor
    this.breed = breed;
  }

  describe() {
    // Call parent method using super
    const baseDescription = super.describe();
    return `${baseDescription} and is a ${this.breed}`;
  }
}

const dog = new Dog("Buddy", 3, "Golden Retriever");
console.log(dog.describe());
// "Buddy is 3 years old and is a Golden Retriever"
```

### super Rules

**What this demonstrates:** Important rules when using super in constructors.

```javascript
class Parent {
  constructor(name) {
    this.name = name;
  }
}

class Child extends Parent {
  constructor(name, age) {
    // ✗ Error: Must call super() before accessing 'this'
    // this.age = age;
    // super(name);

    // ✓ Correct: Call super() first
    super(name);
    this.age = age;
  }
}

// If you don't define a constructor in child class,
// super() is automatically called
class SimpleChild extends Parent {
  // Implicit: constructor(...args) { super(...args); }
}

const child = new SimpleChild("John");
console.log(child.name); // "John" (works without explicit constructor)
```

### Multi-Level Inheritance

**What this demonstrates:** Classes can inherit from other derived classes, creating an inheritance chain.

```javascript
class LivingBeing {
  constructor(name) {
    this.name = name;
    this.isAlive = true;
  }

  breathe() {
    return `${this.name} is breathing`;
  }
}

class Animal extends LivingBeing {
  constructor(name, species) {
    super(name);
    this.species = species;
  }

  move() {
    return `${this.name} is moving`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name, "Canine");
    this.breed = breed;
  }

  bark() {
    return `${this.name} barks`;
  }
}

const dog = new Dog("Buddy", "Golden Retriever");
console.log(dog.breathe()); // From LivingBeing
console.log(dog.move()); // From Animal
console.log(dog.bark()); // From Dog
console.log(dog.species); // "Canine"
console.log(dog.breed); // "Golden Retriever"
```

### Checking Inheritance

```javascript
class Animal {}
class Dog extends Animal {}
class Cat extends Animal {}

const dog = new Dog();
const cat = new Cat();

// instanceof checks the prototype chain
console.log(dog instanceof Dog); // true
console.log(dog instanceof Animal); // true
console.log(dog instanceof Cat); // false
console.log(dog instanceof Object); // true (all inherit from Object)

// Check if class extends another
console.log(Dog.prototype instanceof Animal); // true
```

---

## 5. Static Methods

**What this demonstrates:** Static methods belong to the class itself, not to instances. Called on the class, not on objects.

### Basic Static Methods

```javascript
class MathUtils {
  // Static method - belongs to the class
  static add(a, b) {
    return a + b;
  }

  static multiply(a, b) {
    return a * b;
  }

  static circle = {
    area(radius) {
      return Math.PI * radius ** 2;
    },
    circumference(radius) {
      return 2 * Math.PI * radius;
    },
  };
}

// Called on the class, not on instances
console.log(MathUtils.add(5, 3)); // 8
console.log(MathUtils.multiply(4, 2)); // 8
console.log(MathUtils.circle.area(5)); // 78.54

// Cannot call on instance
const utils = new MathUtils();
// utils.add(5, 3);  // ✗ TypeError: utils.add is not a function
```

### Static Methods for Utility Functions

**What this demonstrates:** Common use case - utility functions that don't need instance data.

```javascript
class StringUtils {
  static capitalize(str) {
    return str.charAt(0).toUpperCase() + str.slice(1).toLowerCase();
  }

  static truncate(str, maxLength) {
    return str.length > maxLength ? str.slice(0, maxLength) + "..." : str;
  }

  static slugify(str) {
    return str
      .toLowerCase()
      .trim()
      .replace(/[^\w\s-]/g, "")
      .replace(/[\s_-]+/g, "-")
      .replace(/^-+|-+$/g, "");
  }
}

console.log(StringUtils.capitalize("hello")); // "Hello"
console.log(StringUtils.truncate("Long text", 4)); // "Long..."
console.log(StringUtils.slugify("Hello World!")); // "hello-world"
```

### Static Methods for Factory Pattern

**What this demonstrates:** Using static methods to create instances with predefined configurations. 
- This example shows how to create convenience methods that build objects with specific preset values, making object creation easier and more readable.

```javascript
class User {
  constructor(username, email, role) {
    this.username = username;
    this.email = email;
    this.role = role;
    this.createdAt = new Date();
  }

  // Static factory methods
  static createAdmin(username, email) {
    return new User(username, email, "admin");
  }

  static createGuest() {
    const randomId = Math.random().toString(36).substr(2, 9);
    return new User(`guest_${randomId}`, "", "guest");
  }

  static fromJSON(json) {
    const data = JSON.parse(json);
    return new User(data.username, data.email, data.role);
  }
}

// With factory methods - cleaner and less error-prone
const admin = User.createAdmin("admin_john", "john@example.com"); // "admin" role is automatic, No need to remember role
const guest = User.createGuest();  // Automatic ID generation and role
const userFromAPI = User.fromJSON(
  '{"username":"jane","email":"jane@example.com","role":"user"}',
);

console.log(admin.role); // "admin"
console.log(guest.role); // "guest"
```

### Static Properties (ES2022)

**What this demonstrates:** Static properties belong to the class, shared across all instances.

```javascript
class Database {
  static connections = 0;
  static maxConnections = 100;

  constructor(name) {
    if (Database.connections >= Database.maxConnections) {
      throw new Error("Max connections reached");
    }
    this.name = name;
    Database.connections++;
  }

  close() {
    Database.connections--;
  }

  static getConnectionCount() {
    return Database.connections;
  }
}

const db1 = new Database("db1");
const db2 = new Database("db2");
console.log(Database.getConnectionCount()); // 2
console.log(Database.maxConnections); // 100

db1.close();
console.log(Database.getConnectionCount()); // 1
// Static properties are used for data shared across ALL instances.
// Example: Total connection count and max connections limit are the same for all instances - 
// every Database instance should see and update the same connection counter.
// Instance properties are unique to each object.
// Example: Each database connection has its own name (db1, db2, db3) - 
// these names are specific to individual instances, not shared.
```

### Static Methods with 'this'

**What this demonstrates:** Inside static methods, `this` refers to the class itself, not an instance.

```javascript
class Counter {
  static count = 0;

  static increment() {
    this.count++; // 'this' refers to Counter class
    return this.count;
  }

  static reset() {
    this.count = 0;
  }

  static getCount() {
    return this.count;
  }
}

Counter.increment();
Counter.increment();
console.log(Counter.getCount()); // 2
Counter.reset();
console.log(Counter.getCount()); // 0
```

### Static Methods in Inheritance

**What this demonstrates:** Static methods are inherited and can be overridden.

```javascript
class Animal {
  static type = "Animal";

  static getType() {
    return this.type;
  }

  static describe() {
    return `This is an ${this.type}`;
  }
}

class Dog extends Animal {
  static type = "Dog";

  // Override static method
  static describe() {
    return `${super.describe()} that barks`;
  }
}

console.log(Animal.getType()); // "Animal"
console.log(Dog.getType()); // "Dog" (inherited, uses Dog's type)
console.log(Animal.describe()); // "This is an Animal"
console.log(Dog.describe()); // "This is an Dog that barks"
```

---

## 6. Getters & Setters

**What this demonstrates:** Getters and setters allow you to define methods that are accessed like properties, enabling validation and computed properties.
-  You must use the **get** and **set** keywords to define getter and setter methods

### Basic Getters and Setters

```javascript
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // Getter - accessed like a property
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // Setter - set like a property
  set fullName(name) {
    const parts = name.split(" ");
    this.firstName = parts[0] || "";
    this.lastName = parts[1] || "";
  }
}

const person = new Person("John", "Doe");
console.log(person.fullName); // "John Doe" (getter)

person.fullName = "Jane Smith"; // Setter
console.log(person.firstName); // "Jane"
console.log(person.lastName); // "Smith"
```

### Getters for Computed Properties

**What this demonstrates:** Getters are perfect for properties that are calculated from other properties.

```javascript
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  get area() {
    return this.width * this.height;
  }

  get perimeter() {
    return 2 * (this.width + this.height);
  }

  get isSquare() {
    return this.width === this.height;
  }
}

const rect = new Rectangle(10, 5);
console.log(rect.area); // 50 (computed)
console.log(rect.perimeter); // 30 (computed)
console.log(rect.isSquare); // false (computed)

// Values update automatically when properties change
rect.width = 5;
console.log(rect.isSquare); // true (now it's a square)
```

### Setters with Validation

**What this demonstrates:** Setters can validate data before setting properties.

```javascript
class User {
  constructor(username, age) {
    this._username = username; // Convention: _ for internal property
    this._age = age;
  }

  get username() {
    return this._username;
  }

  set username(value) {
    if (typeof value !== "string" || value.length < 3) {
      throw new Error("Username must be a string with at least 3 characters");
    }
    this._username = value;
  }

  get age() {
    return this._age;
  }

  set age(value) {
    if (typeof value !== "number" || value < 0 || value > 120) {
      throw new Error("Age must be a number between 0 and 120");
    }
    this._age = value;
  }
}

const user = new User("john_doe", 30);
console.log(user.username); // "john_doe"

user.age = 31; // ✓ Valid
// user.age = 150;  // ✗ Error: Age must be between 0 and 120
// user.username = "ab";  // ✗ Error: Username must be at least 3 characters
```

### Read-Only Properties (Getter without Setter)

**What this demonstrates:** Properties that can be read but not modified by creating only a getter.

```javascript
class User {
  constructor(username, email) {
    this._username = username;
    this._email = email;
    this._id = Math.random().toString(36).substr(2, 9);
    this._createdAt = new Date();
  }

  // Read-only properties
  get id() {
    return this._id;
  }

  get createdAt() {
    return this._createdAt;
  }

  // Read-write property
  get username() {
    return this._username;
  }

  set username(value) {
    this._username = value;
  }
}

const user = new User("john_doe", "john@example.com");
console.log(user.id); // Random ID
console.log(user.createdAt); // Date object

user.username = "jane_doe"; // ✓ Works (has setter)
// user.id = "new_id";       // Silently fails (no setter)
// user.createdAt = new Date();  // Silently fails (no setter)
console.log(user.id); // Still the original ID
```

### Getters/Setters with Private Fields

**What this demonstrates:** Combining getters/setters with private fields for true encapsulation.

```javascript
class BankAccount {
  #balance = 0; // Private field

  constructor(initialBalance) {
    this.#balance = initialBalance;
  }

  get balance() {
    return this.#balance;
  }

  // No setter - balance can only be changed through methods
  deposit(amount) {
    if (amount <= 0) {
      throw new Error("Deposit amount must be positive");
    }
    this.#balance += amount;
  }

  withdraw(amount) {
    if (amount <= 0) {
      throw new Error("Withdrawal amount must be positive");
    }
    if (amount > this.#balance) {
      throw new Error("Insufficient funds");
    }
    this.#balance -= amount;
  }
}

const account = new BankAccount(1000);
console.log(account.balance); // 1000 (can read)

account.deposit(500);
console.log(account.balance); // 1500

// account.balance = 9999;  // Silently fails (no setter)
// account.#balance = 9999;  // ✗ SyntaxError: Private field
```

---

## 7. Private Fields (#)

**What this demonstrates:** True private fields (ES2022) that cannot be accessed or modified from outside the class.
- Private fields in JavaScript classes are properties declared with a # prefix. They are accessible only within the class where they are defined and cannot be accessed or modified from outside the class.
- You must use the # prefix every time you reference a private field, not just during initialization. The # is required for all access, assignment, and usage inside the class.

**Static VS Private:**
- static fields: Belong to the class itself, not to instances. Accessed as ClassName.field, not via objects. Used for shared data (e.g., counters).
- private fields (#): Belong to each instance, but are only accessible inside the class definition. Cannot be accessed from outside, not even via ClassName or instances.

### Basic Private Fields

```javascript
class User {
  // Private fields - start with #
  #password;
  #email;

  // Public fields
  username;

  constructor(username, email, password) {
    this.username = username;
    this.#email = email;
    this.#password = password;
  }

  // Public method to access private field
  checkPassword(inputPassword) {
    return this.#password === inputPassword;
  }

  // Public method to update private field
  changePassword(oldPassword, newPassword) {
    if (this.#password !== oldPassword) {
      throw new Error("Incorrect password");
    }
    this.#password = newPassword;
  }
}

const user = new User("john_doe", "john@example.com", "secret123");
console.log(user.username); // "john_doe" (public)
// console.log(user.#password);  // ✗ SyntaxError: Private field
// console.log(user.#email);     // ✗ SyntaxError: Private field

console.log(user.checkPassword("secret123")); // true
user.changePassword("secret123", "newSecret456");
console.log(user.checkPassword("newSecret456")); // true
```

### Private Methods

**What this demonstrates:** Methods can also be private, only callable from within the class.

```javascript
class DataProcessor {
  #data = [];

  // Private method
  #validate(item) {
    return item !== null && item !== undefined;
  }

  // Private method
  #transform(item) {
    return typeof item === "string" ? item.toUpperCase() : item;
  }

  // Public method using private methods
  add(item) {
    if (!this.#validate(item)) {
      throw new Error("Invalid item");
    }
    this.#data.push(this.#transform(item));
  }

  getData() {
    return this.#data;
  }
}

const processor = new DataProcessor();
processor.add("hello");
processor.add("world");
console.log(processor.getData()); // ["HELLO", "WORLD"]

// processor.#validate("test");  // ✗ SyntaxError: Private field
// processor.#transform("test");  // ✗ SyntaxError: Private field
```

### Static Private Fields and Methods

**What this demonstrates:** Private fields and methods can also be static.
- Static private fields and methods in JavaScript classes are declared with both static and #. They belong to the class itself (not instances) and are accessible only within the class definition.
- When you want a counter which you do wish to disclose to anyone, in that case static private fields/emthods are usefull.

```javascript
class Database {
  // Static private field
  static #instances = [];
  static #maxInstances = 3;

  #name;

  constructor(name) {
    if (!Database.#canCreateInstance()) {
      throw new Error("Max instances reached");
    }
    this.#name = name;
    Database.#instances.push(this);
  }

  // Static private method
  static #canCreateInstance() {
    return Database.#instances.length < Database.#maxInstances;
  }

  // Public static method
  static getInstanceCount() {
    return Database.#instances.length;
  }

  getName() {
    return this.#name;
  }
}

const db1 = new Database("db1");
const db2 = new Database("db2");
const db3 = new Database("db3");
// const db4 = new Database("db4");  // ✗ Error: Max instances reached

console.log(Database.getInstanceCount()); // 3
// console.log(Database.#instances);  // ✗ SyntaxError: Private field
```

### Private Fields with Getters/Setters

**What this demonstrates:** Combining private fields with getters/setters for controlled access.

```javascript
class Temperature {
  #celsius;

  constructor(celsius) {
    this.#celsius = celsius;
  }

  // Getter for private field
  get celsius() {
    return this.#celsius;
  }

  // Setter with validation
  set celsius(value) {
    if (value < -273.15) {
      throw new Error("Temperature below absolute zero");
    }
    this.#celsius = value;
  }

  // Computed property using private field
  get fahrenheit() {
    return (this.#celsius * 9) / 5 + 32;
  }

  set fahrenheit(value) {
    this.celsius = ((value - 32) * 5) / 9; // Uses setter for validation
  }

  get kelvin() {
    return this.#celsius + 273.15;
  }
}

const temp = new Temperature(25);
console.log(temp.celsius); // 25
console.log(temp.fahrenheit); // 77
console.log(temp.kelvin); // 298.15

temp.fahrenheit = 100;
console.log(temp.celsius); // ~37.78
```

### Checking for Private Fields

**What this demonstrates:** Using the `in` operator to check if a private field exists.

```javascript
class MyClass {
  #privateField = 42;

  static hasPrivateField(obj) {
    return #privateField in obj;
  }

  getPrivateField() {
    return this.#privateField;
  }
}

const obj1 = new MyClass();
const obj2 = {};

console.log(MyClass.hasPrivateField(obj1)); // true
console.log(MyClass.hasPrivateField(obj2)); // false
```

**Note:** Before ES2022, developers used underscore prefix (`_propertyName`) as a convention to indicate private fields, but they were still accessible. True private fields with `#` are genuinely inaccessible from outside the class.

---

## Quick Reference

### Class Basics

```javascript
class MyClass {
  // Class fields (ES2022)
  publicField = "value";
  #privateField = "private";

  // Constructor
  constructor(param) {
    this.property = param;
  }

  // Instance method
  method() {}

  // Static method
  static staticMethod() {}

  // Getter
  get computed() {
    return this.property;
  }

  // Setter
  set computed(value) {
    this.property = value;
  }

  // Private method
  #privateMethod() {}
}
```

### Inheritance

```javascript
class Child extends Parent {
  constructor(param) {
    super(param); // Call parent constructor (required)
    this.childProperty = "value";
  }

  method() {
    super.method(); // Call parent method
    // Additional logic
  }
}
```

### Key Concepts

| Feature       | Description                    | Example                                   |
| ------------- | ------------------------------ | ----------------------------------------- |
| `constructor` | Initializes instance           | `constructor(name) { this.name = name; }` |
| `extends`     | Inherit from class             | `class Dog extends Animal`                |
| `super`       | Call parent constructor/method | `super(name)` or `super.method()`         |
| `static`      | Class-level method/property    | `static method() {}`                      |
| `get`         | Define getter                  | `get fullName() { return ...; }`          |
| `set`         | Define setter                  | `set fullName(value) { ... }`             |
| `#field`      | Private field                  | `#privateField = 0;`                      |
| `#method()`   | Private method                 | `#privateMethod() {}`                     |

### Common Patterns

```javascript
// Factory methods
class User {
  static createAdmin(name) {
    return new User(name, "admin");
  }
}

// Method chaining
class Builder {
  setName(name) {
    this.name = name;
    return this;  // Return 'this'
  }
}

// Validation in setter
set age(value) {
  if (value < 0) throw new Error("Invalid age");
  this._age = value;
}

// Read-only property
get id() {
  return this._id;
}
// No setter - read-only

// Private field with public accessor
#value = 0;
getValue() {
  return this.#value;
}
```

### Instance vs Static vs Private

```javascript
class Example {
  // Instance (each object has its own)
  instanceProperty = "instance";
  instanceMethod() {}

  // Static (belongs to class)
  static staticProperty = "static";
  static staticMethod() {}

  // Private (only accessible within class)
  #privateProperty = "private";
  #privateMethod() {}
}

Example.staticMethod(); // ✓ Call on class
const obj = new Example();
obj.instanceMethod(); // ✓ Call on instance
// obj.staticMethod();                // ✗ Not on instance
// Example.instanceMethod();          // ✗ Not on class
// obj.#privateMethod();              // ✗ SyntaxError
```

### Checking Types

```javascript
class Animal {}
class Dog extends Animal {}

const dog = new Dog();

// Check instance
dog instanceof Dog; // true
dog instanceof Animal; // true
dog instanceof Object; // true

// Check constructor
dog.constructor === Dog; // true

// Check prototype
Object.getPrototypeOf(dog) === Dog.prototype; // true
```

---

