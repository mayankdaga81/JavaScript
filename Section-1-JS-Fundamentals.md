# Section 1: JavaScript Fundamentals

## 1. Variables (var, let, const)

### var
- Function-scoped (not block-scoped)
- Can be redeclared and updated
- Hoisted with `undefined` initialization
- Avoid in modern JavaScript

```javascript
var name = "John";
var name = "Jane"; // ✓ Allowed (redeclaration)
name = "Bob";      // ✓ Allowed (update)

if (true) {
    var age = 25;
}
console.log(age); // 25 (no block scope)
```

### let
- Block-scoped
- Cannot be redeclared in same scope
- Can be updated
- Hoisted but not initialized (Temporal Dead Zone)

```javascript
let name = "John";
// let name = "Jane"; // ✗ Error (cannot redeclare)
name = "Bob";         // ✓ Allowed (update)

if (true) {
    let age = 25;
}
// console.log(age); // ✗ Error (block scoped)
```

### const
- Block-scoped
- Cannot be redeclared or reassigned
- Must be initialized at declaration
- Objects/arrays can be mutated (reference is constant)

```javascript
const PI = 3.14;
// PI = 3.15;        // ✗ Error (cannot reassign)
// const VALUE;      // ✗ Error (must initialize)

const person = { name: "John" };
person.name = "Jane";  // ✓ Allowed (mutation)
// person = {};       // ✗ Error (reassignment)
```

### When to Use
- **const**: Default choice (immutable reference)
- **let**: When reassignment is needed
- **var**: Never use in modern code

---

## 2. Data Types

### Primitives (Stored by Value)
Immutable, stored directly in variable.

#### String
```javascript
let str1 = "Hello";
let str2 = 'World';
let str3 = `Template ${str1}`; // Template literal
```

#### Number
```javascript
let int = 42;
let float = 3.14;
let negative = -10;
let infinity = Infinity;
let notANumber = NaN;
```

#### Boolean
```javascript
let isActive = true;
let isCompleted = false;
```

#### Undefined
```javascript
let x;              // undefined
let y = undefined;  // explicitly undefined
```

#### Null
```javascript
let empty = null;   // intentional absence of value
```

#### Symbol (ES6)
```javascript
let sym1 = Symbol("id");
let sym2 = Symbol("id");
// sym1 !== sym2 (always unique)
```

#### BigInt (ES2020)
```javascript
let big = 123456789012345678901234567890n;
let big2 = BigInt("123456789012345678901234567890");
```

### Reference Types (Stored by Reference)
Mutable, stored as reference to memory location.

#### Object
```javascript
let person = {
    name: "John",
    age: 30
};
```

#### Array
```javascript
let numbers = [1, 2, 3, 4, 5];
let mixed = [1, "two", true, null];
```

#### Function
```javascript
function greet() {
    return "Hello";
}
let sayHi = function() { return "Hi"; };
```

### typeof Operator
```javascript
typeof "text"        // "string"
typeof 42            // "number"
typeof true          // "boolean"
typeof undefined     // "undefined"
typeof null          // "object" (historical bug!)
typeof Symbol()      // "symbol"
typeof 123n          // "bigint"
typeof {}            // "object"
typeof []            // "object"
typeof function(){}  // "function"
```

### Primitive vs Reference: Key Difference
```javascript
// Primitives - copy by value
let a = 10;
let b = a;
b = 20;
console.log(a); // 10 (unchanged)

// Reference - copy by reference
let obj1 = { value: 10 };
let obj2 = obj1;
obj2.value = 20;
console.log(obj1.value); // 20 (changed!)
```

---

## 3. Type Coercion & Conversion

### Type Coercion (Implicit)
JavaScript automatically converts types.

```javascript
// String coercion
"5" + 2        // "52" (number to string)
"5" + true     // "5true"

// Number coercion
"5" - 2        // 3 (string to number)
"5" * "2"      // 10
"5" / 2        // 2.5

// Boolean coercion
if ("hello")   // true (non-empty string)
if (0)         // false
if ([])        // true (empty array is truthy!)
```

### Falsy Values
Only 6 falsy values in JavaScript:
```javascript
false
0
"" (empty string)
null
undefined
NaN
```
Everything else is truthy!

### Type Conversion (Explicit)

#### To String
```javascript
String(123)           // "123"
(123).toString()      // "123"
123 + ""              // "123"
```

#### To Number
```javascript
Number("123")         // 123
Number("12.5")        // 12.5
Number("abc")         // NaN
parseInt("123px")     // 123
parseFloat("12.5em")  // 12.5
+"123"                // 123 (unary +)
```

#### To Boolean
```javascript
Boolean(1)            // true
Boolean(0)            // false
Boolean("")           // false
Boolean("text")       // true
!!value               // double negation trick
```

### == vs === (Equality)
```javascript
// == (loose equality - allows coercion)
5 == "5"              // true
null == undefined     // true
0 == false            // true

// === (strict equality - no coercion)
5 === "5"             // false
null === undefined    // false
0 === false           // false
```

**Best Practice**: Always use `===` and `!==`

---

## 4. Operators

### Arithmetic Operators
```javascript
10 + 5    // 15 (addition)
10 - 5    // 5  (subtraction)
10 * 5    // 50 (multiplication)
10 / 5    // 2  (division)
10 % 3    // 1  (modulus/remainder)
10 ** 2   // 100 (exponentiation ES2016)

let x = 5;
x++       // 5, then x becomes 6 (post-increment)
++x       // 7 (pre-increment)
x--       // 7, then x becomes 6 (post-decrement)
--x       // 5 (pre-decrement)
```

### Assignment Operators
```javascript
let x = 10;
x += 5    // x = x + 5  → 15
x -= 5    // x = x - 5  → 10
x *= 2    // x = x * 2  → 20
x /= 4    // x = x / 4  → 5
x %= 3    // x = x % 3  → 2
x **= 2   // x = x ** 2 → 4
```

### Comparison Operators
```javascript
5 > 3     // true (greater than)
5 < 3     // false (less than)
5 >= 5    // true (greater than or equal)
5 <= 3    // false (less than or equal)
5 == "5"  // true (loose equality)
5 === 5   // true (strict equality)
5 != "5"  // false (loose inequality)
5 !== "5" // true (strict inequality)
```

### Logical Operators
```javascript
// && (AND) - returns first falsy or last value
true && true          // true
true && false         // false
"hi" && "bye"         // "bye"
"" && "hi"            // ""

// || (OR) - returns first truthy or last value
true || false         // true
false || false        // false
"" || "default"       // "default"
null || 0 || "yes"    // "yes"

// ! (NOT)
!true                 // false
!false                // true
!"hello"              // false
```

### Nullish Coalescing (??) - ES2020
Returns right operand when left is `null` or `undefined` only.
```javascript
null ?? "default"     // "default"
undefined ?? "default" // "default"
0 ?? "default"        // 0 (not null/undefined)
"" ?? "default"       // "" (not null/undefined)

// vs OR operator
0 || "default"        // "default" (0 is falsy)
0 ?? "default"        // 0 (0 is not null/undefined)
```

### Optional Chaining (?.) - ES2020
Safe property access without errors.
```javascript
const user = {
    name: "John",
    address: {
        city: "NYC"
    }
};

user.address?.city         // "NYC"
user.contact?.phone        // undefined (no error!)
user.getDetails?.()        // undefined (method doesn't exist)

// Old way (without ?.)
user.contact && user.contact.phone  // undefined
```

### Ternary Operator
```javascript
condition ? valueIfTrue : valueIfFalse

let age = 18;
let status = age >= 18 ? "Adult" : "Minor";

// Can be nested (but avoid for readability)
let result = age < 13 ? "Child" : age < 18 ? "Teen" : "Adult";
```

### String Operators
```javascript
"Hello" + " " + "World"    // "Hello World" (concatenation)
let name = "John";
`Hello ${name}`            // "Hello John" (template literal)
```

### typeof & instanceof
```javascript
typeof "text"              // "string"
typeof 42                  // "number"

let arr = [];
arr instanceof Array       // true
arr instanceof Object      // true
```

---

## Quick Reference: Variable Declaration

| Feature | var | let | const |
|---------|-----|-----|-------|
| Scope | Function | Block | Block |
| Redeclare | ✓ | ✗ | ✗ |
| Reassign | ✓ | ✓ | ✗ |
| Hoisting | Initialized | Uninitialized | Uninitialized |
| Use in modern JS | ✗ | ✓ | ✓ (default) |

## Quick Reference: Type Checking

```javascript
// Checking primitives
typeof value === "string"
typeof value === "number"
typeof value === "boolean"

// Checking null/undefined
value === null
value === undefined
value == null  // catches both null and undefined

// Checking arrays
Array.isArray(value)

// Checking objects
typeof value === "object" && value !== null && !Array.isArray(value)

// Checking NaN
Number.isNaN(value)
```

---

**Next**: Ready for Section 2 (Functions)? Just ask!
