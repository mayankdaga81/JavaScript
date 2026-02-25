# Section 4: Arrays

## 1. Array Creation & Access

### Creating Arrays

```javascript
// Array literal
const arr = [1, 2, 3, 4, 5];

// Array constructor
const arr2 = new Array(1, 2, 3); // [1, 2, 3]
const arr3 = new Array(5); // [empty × 5] (5 empty slots)

// Array.of() - consistent constructor
Array.of(5); // [5] (not 5 empty slots)
Array.of(1, 2, 3); // [1, 2, 3]

// Array.from() - create from iterable
Array.from("hello"); // ["h", "e", "l", "l", "o"]
Array.from([1, 2, 3], (x) => x * 2); // [2, 4, 6] (with map)

// Mixed types (not recommended in TypeScript)
const mixed = [1, "two", true, null, { a: 1 }];

// Empty array
const empty = [];
```

### Accessing Elements

```javascript
const arr = ["a", "b", "c", "d"];

arr[0]; // "a" (first element)
arr[3]; // "d"
arr[10]; // undefined (out of bounds)
arr[-1]; // undefined (no negative indexing)

// Length
arr.length; // 4

// Last element
arr[arr.length - 1]; // "d"
arr.at(-1); // "d" (ES2022, supports negative)

// First element
arr[0]; // "a"
arr.at(0); // "a"
```

### Modifying Elements

```javascript
const arr = [1, 2, 3];

// Update
arr[0] = 10; // [10, 2, 3]

// Add (sparse array)
arr[5] = 6; // [10, 2, 3, empty × 2, 6]

// Length property
arr.length = 2; // [10, 2] (truncates)
arr.length = 5; // [10, 2, empty × 3] (adds empty slots)
```

---

## 2. Array Methods (Mutating & Non-Mutating)

### push() - Add to End (Mutates)

```javascript
const arr = [1, 2, 3];
arr.push(4); // 4 (returns new length)
arr.push(5, 6); // 6 (multiple items)
// arr: [1, 2, 3, 4, 5, 6]
```

### pop() - Remove from End (Mutates)

```javascript
const arr = [1, 2, 3];
const last = arr.pop(); // 3 (returns removed item)
// arr: [1, 2]
```

### unshift() - Add to Beginning (Mutates)

```javascript
const arr = [1, 2, 3];
arr.unshift(0); // 4 (returns new length)
arr.unshift(-2, -1); // 6
// arr: [-2, -1, 0, 1, 2, 3]
```

### shift() - Remove from Beginning (Mutates)

```javascript
const arr = [1, 2, 3];
const first = arr.shift(); // 1 (returns removed item)
// arr: [2, 3]
```

### splice() - Add/Remove at Position (Mutates)

Most versatile array method.

```javascript
const arr = [1, 2, 3, 4, 5];

// Remove elements
arr.splice(2, 2); // [3, 4] (removed items)
// arr: [1, 2, 5]

// Insert elements
arr.splice(1, 0, "a", "b"); // [] (nothing removed)
// arr: [1, "a", "b", 2, 5]

// Replace elements
arr.splice(2, 1, "X"); // ["b"] (removed)
// arr: [1, "a", "X", 2, 5]

// Remove all from index
arr.splice(2); // ["X", 2, 5] (removed)
// arr: [1, "a"]
```

**Syntax**: `array.splice(start, deleteCount, item1, item2, ...)`

### slice() - Extract Portion (Non-Mutating)

```javascript
const arr = [1, 2, 3, 4, 5];

arr.slice(1, 3); // [2, 3] (from index 1 to 3, excluding 3)
arr.slice(2); // [3, 4, 5] (from index 2 to end)
arr.slice(-2); // [4, 5] (last 2 elements)
arr.slice(1, -1); // [2, 3, 4] (from 1 to second-last)

// Shallow copy
const copy = arr.slice(); // [1, 2, 3, 4, 5]
```

### concat() - Merge Arrays (Non-Mutating)

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const arr3 = [5, 6];

arr1.concat(arr2); // [1, 2, 3, 4]
arr1.concat(arr2, arr3); // [1, 2, 3, 4, 5, 6]
arr1.concat([3, 4], 5, 6); // [1, 2, 3, 4, 5, 6]

// Modern alternative: spread
[...arr1, ...arr2, ...arr3]; // [1, 2, 3, 4, 5, 6]
```

### join() - Array to String

```javascript
const arr = ["a", "b", "c"];

arr.join(); // "a,b,c" (default: comma)
arr.join(""); // "abc"
arr.join(" - "); // "a - b - c"
arr.join(", "); // "a, b, c"
```

### reverse() - Reverse Order (Mutates)

```javascript
const arr = [1, 2, 3, 4, 5];
arr.reverse(); // [5, 4, 3, 2, 1]
// arr: [5, 4, 3, 2, 1] (mutated)

// Non-mutating reverse
const reversed = [...arr].reverse();
// or
const reversed = arr.slice().reverse();
```

### sort() - Sort Elements (Mutates)

```javascript
const arr = [3, 1, 4, 1, 5, 9, 2];

// Default: converts to strings and sorts
arr.sort(); // [1, 1, 2, 3, 4, 5, 9]

// Problem with numbers
[10, 5, 40, 25].sort(); // [10, 25, 40, 5] (string sort!)

// Fix: compare function
[10, 5, 40, 25].sort((a, b) => a - b); // [5, 10, 25, 40] (ascending)
[10, 5, 40, 25].sort((a, b) => b - a); // [40, 25, 10, 5] (descending)

// Strings (case-insensitive)
// In the example below, with arrX.sort() - we would have got ["Banana", "Cherry", "apple"];
// This is based on ASCII numbers, 'A' (65) and 'a' (97)
["Banana", "apple", "Cherry"].sort((a, b) =>
  a.toLowerCase().localeCompare(b.toLowerCase()),
);
// ["apple", "Banana", "Cherry"]

// Objects
const people = [
  { name: "John", age: 30 },
  { name: "Jane", age: 25 },
  { name: "Bob", age: 35 },
];
people.sort((a, b) => a.age - b.age); // Sort by age ascending
```

### indexOf() / lastIndexOf() - Find Index

```javascript
const arr = [1, 2, 3, 2, 1];

arr.indexOf(2); // 1 (first occurrence)
arr.lastIndexOf(2); // 3 (last occurrence)
arr.indexOf(10); // -1 (not found)
arr.indexOf(2, 2); // 3 (start searching from index 2)
```

### includes() - Check Existence (ES2016)

```javascript
const arr = [1, 2, 3, NaN];

arr.includes(2); // true
arr.includes(10); // false
arr.includes(NaN); // true (works with NaN, unlike indexOf)

[1, 2, 3].indexOf(NaN); // -1 (doesn't find NaN)
[1, 2, 3].includes(NaN); // false
```

### flat() - Flatten Nested Arrays (ES2019)

```javascript
const arr = [1, [2, 3], [4, [5, 6]]];

arr.flat(); // [1, 2, 3, 4, [5, 6]] (default depth: 1)
arr.flat(2); // [1, 2, 3, 4, 5, 6] (depth: 2)
arr.flat(Infinity); // [1, 2, 3, 4, 5, 6] (fully flatten)

// Remove empty slots
[1, 2, , 4, 5].flat(); // [1, 2, 4, 5]
```

### fill() - Fill with Value (Mutates)

```javascript
const arr = [1, 2, 3, 4, 5];

arr.fill(0); // [0, 0, 0, 0, 0]
arr.fill(9, 2); // [0, 0, 9, 9, 9] (from index 2)
arr.fill(7, 1, 3); // [0, 7, 7, 9, 9] (from index 1 to 3)

// Create array with default value
new Array(5).fill(0); // [0, 0, 0, 0, 0]
```

---

## 3. Iteration Methods

### forEach() - Execute for Each (No Return)

```javascript
const arr = [1, 2, 3];

arr.forEach((value, index, array) => {
  console.log(`${index}: ${value}`);
});
// 0: 1
// 1: 2
// 2: 3
// Note - The forEach contains the syntax of the normal case, where -
// value: The current element in the array.
// index: The index of the current element.
// array: The entire array being iterated (in this case, arr). So, array is just a reference to arr itself, available in each callback call.

// Can't break/return early
arr.forEach((value) => {
  if (value === 2) return; // Only skips this iteration
  console.log(value);
});
// 1, 3
```

### map() - Transform Each Element - (Non-Mutating)

```javascript
const arr = [1, 2, 3, 4, 5];

// Double each number
arr.map((x) => x * 2); // [2, 4, 6, 8, 10]

// With index
arr.map((x, i) => x + i); // [1, 3, 5, 7, 9]

// Objects
const users = [
  { name: "John", age: 30 },
  { name: "Jane", age: 25 },
];
users.map((user) => user.name); // ["John", "Jane"]

// Chaining
arr.map((x) => x * 2).map((x) => x + 1); // [3, 5, 7, 9, 11]
```

### filter() - Keep Elements that Pass Test - (Non-Mutating)

```javascript
const arr = [1, 2, 3, 4, 5, 6];

// Even numbers
arr.filter((x) => x % 2 === 0); // [2, 4, 6]

// Greater than 3
arr.filter((x) => x > 3); // [4, 5, 6]

// With index
arr.filter((x, i) => i % 2 === 0); // [1, 3, 5] (even indices)

// Objects
const users = [
  { name: "John", age: 30 },
  { name: "Jane", age: 17 },
  { name: "Bob", age: 25 },
];
users.filter((user) => user.age >= 18); // John, Bob

// Remove falsy values
[0, 1, false, 2, "", 3].filter(Boolean); // [1, 2, 3]
```

### reduce() - Reduce to Single Value - (Non-Mutating)

```javascript
const arr = [1, 2, 3, 4, 5];

// Sum
arr.reduce((sum, num) => sum + num, 0); // 15

// Product
arr.reduce((product, num) => product * num, 1); // 120

// Max value
arr.reduce((max, num) => Math.max(max, num), -Infinity); // 5

// Count occurrences
const fruits = ["apple", "banana", "apple", "orange", "banana", "apple"];
fruits.reduce((count, fruit) => {
  count[fruit] = (count[fruit] || 0) + 1;
  return count;
}, {});
// { apple: 3, banana: 2, orange: 1 }

// Flatten array
[[1, 2], [3, 4], [5]].reduce((flat, arr) => flat.concat(arr), []);
// [1, 2, 3, 4, 5]

// Group by property
const people = [
  { name: "John", age: 30 },
  { name: "Jane", age: 25 },
  { name: "Bob", age: 30 },
];
people.reduce((groups, person) => {
  const age = person.age;
  groups[age] = groups[age] || [];
  groups[age].push(person);
  return groups;
}, {});
// { 25: [{Jane}], 30: [{John}, {Bob}] }

// Notes -
// In reduce, the value you return from the arrow function becomes the accumulator for the next iteration.
// When you use a concise arrow function (without curly braces), like (acc, val) => acc + val, the result of the expression (acc + val) is automatically returned. This is shorthand for return acc + val.
// If you use curly braces, you must explicitly write return, like:
// (acc, val) => { return acc + val; }
```

**Syntax**: `array.reduce((accumulator, currentValue, index, array) => {}, initialValue)`

### reduceRight() - Reduce from Right to Left

```javascript
const arr = [1, 2, 3, 4];

arr.reduceRight((acc, val) => acc - val, 0); // -10
// 0 - 4 - 3 - 2 - 1 = -10

// vs reduce
arr.reduce((acc, val) => acc - val, 0); // -10
// 0 - 1 - 2 - 3 - 4 = -10
```

### find() - First Element that Passes Test

```javascript
const arr = [1, 2, 3, 4, 5];

arr.find((x) => x > 3); // 4 (first match)
arr.find((x) => x > 10); // undefined (not found)

// Objects
const users = [
  { id: 1, name: "John" },
  { id: 2, name: "Jane" },
  { id: 3, name: "Bob" },
];
users.find((user) => user.id === 2); // { id: 2, name: "Jane" }
```

### findIndex() - Index of First Match

```javascript
const arr = [1, 2, 3, 4, 5];

arr.findIndex((x) => x > 3); // 3 (index of 4)
arr.findIndex((x) => x > 10); // -1 (not found)

// vs indexOf (can use condition)
arr.indexOf(4); // 3 (value-based)
arr.findIndex((x) => x === 4); // 3 (condition-based)
```

### findLast() / findLastIndex() - From End (ES2023)

```javascript
const arr = [1, 2, 3, 4, 5, 4, 3];

arr.findLast((x) => x > 3); // 4 (last occurrence)
arr.findLastIndex((x) => x > 3); // 5 (last index)
```

### some() - Check if Any Pass Test

```javascript
const arr = [1, 2, 3, 4, 5];

arr.some((x) => x > 3); // true (4 and 5 pass)
arr.some((x) => x > 10); // false (none pass)
arr.some((x) => x % 2 === 0); // true (has even numbers)

// Check if array has any truthy values
[false, 0, ""].some(Boolean); // false
[false, 0, "hello"].some(Boolean); // true
```

### every() - Check if All Pass Test

```javascript
const arr = [2, 4, 6, 8];

arr.every((x) => x % 2 === 0); // true (all even)
arr.every((x) => x > 5); // false (not all > 5)

const arr2 = [1, 2, 3];
arr2.every((x) => x > 0); // true (all positive)

// Check if all values are truthy
[1, 2, 3].every(Boolean); // true
[1, 0, 3].every(Boolean); // false
```

### flatMap() - Map then Flatten (ES2019)

```javascript
const arr = [1, 2, 3];

// Regular map
arr.map((x) => [x, x * 2]); // [[1, 2], [2, 4], [3, 6]]

// flatMap
arr.flatMap((x) => [x, x * 2]); // [1, 2, 2, 4, 3, 6]

// Practical: split sentences into words
const sentences = ["Hello world", "How are you"];
sentences.flatMap((s) => s.split(" "));
// ["Hello", "world", "How", "are", "you"]

// Filter + map in one pass
arr.flatMap((x) => (x % 2 === 0 ? [x * 2] : [])); // [4] (only even, doubled)
```

---

## 4. Array Destructuring

### Basic Destructuring

```javascript
const arr = [1, 2, 3, 4, 5];

const [first, second] = arr;
console.log(first); // 1
console.log(second); // 2

// Skip elements
const [first, , third] = arr;
console.log(third); // 3
```

### Rest in Destructuring

```javascript
const arr = [1, 2, 3, 4, 5];

const [first, ...rest] = arr;
console.log(first); // 1
console.log(rest); // [2, 3, 4, 5]

const [first, second, ...remaining] = arr;
console.log(remaining); // [3, 4, 5]
```

### Default Values

```javascript
const arr = [1];

const [first, second = 2, third = 3] = arr;
console.log(first); // 1
console.log(second); // 2 (default)
console.log(third); // 3 (default)
```

### Swapping Variables

```javascript
let a = 1;
let b = 2;

[a, b] = [b, a]; // Swap
console.log(a); // 2
console.log(b); // 1
```

### Nested Destructuring

```javascript
const arr = [1, [2, 3], 4];

const [first, [second, third], fourth] = arr;
console.log(second); // 2
console.log(third); // 3
```

### Function Return Values

```javascript
function getCoordinates() {
  return [10, 20];
}

const [x, y] = getCoordinates();
console.log(x); // 10
console.log(y); // 20
```

---

## 5. Spread & Rest with Arrays

### Spread Operator (...)

Expands array into individual elements.

#### Copying Arrays

```javascript
const arr = [1, 2, 3];
const copy = [...arr]; // Shallow copy

copy.push(4);
console.log(arr); // [1, 2, 3] (original unchanged)
console.log(copy); // [1, 2, 3, 4]
```

#### Merging Arrays

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const arr3 = [5, 6];

const merged = [...arr1, ...arr2, ...arr3]; // [1, 2, 3, 4, 5, 6]

// Insert in middle
const result = [...arr1, "X", ...arr2]; // [1, 2, "X", 3, 4]
```

#### Function Arguments

```javascript
const numbers = [1, 2, 3, 4, 5];

Math.max(...numbers); // 5 (instead of Math.max(numbers))
console.log(...numbers); // 1 2 3 4 5

// Spread multiple arrays
const min = Math.min(...arr1, ...arr2, ...arr3);
```

#### Converting to Array

```javascript
// String to array
const str = "hello";
[...str]; // ["h", "e", "l", "l", "o"]

// Set to array
const set = new Set([1, 2, 3]);
[...set]; // [1, 2, 3]

// NodeList to array
const divs = document.querySelectorAll("div");
[...divs]; // Array of div elements
```

### Rest Parameter (...)

Collects remaining elements into array.

```javascript
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

sum(1, 2, 3); // 6
sum(1, 2, 3, 4, 5); // 15

// With other parameters
function multiply(multiplier, ...numbers) {
  return numbers.map((num) => num * multiplier);
}

multiply(2, 1, 2, 3); // [2, 4, 6]
```

### Spread vs Rest

```javascript
// Spread: expands array
const arr = [1, 2, 3];
console.log(...arr); // 1 2 3

// Rest: collects into array
function fn(...args) {
  console.log(args); // [1, 2, 3]
}
fn(1, 2, 3);
```

---

## Quick Reference

### Mutating Methods

```javascript
arr.push(item)         // Add to end
arr.pop()              // Remove from end
arr.unshift(item)      // Add to beginning
arr.shift()            // Remove from beginning
arr.splice(i, n, ...)  // Add/remove at position
arr.reverse()          // Reverse order
arr.sort(compareFn)    // Sort elements
arr.fill(value)        // Fill with value
```

### Non-Mutating Methods

```javascript
arr.slice(start, end); // Extract portion
arr.concat(arr2); // Merge arrays
arr.join(separator); // Array to string
arr.indexOf(item); // Find index
arr.includes(item); // Check existence
arr.flat(depth); // Flatten nested
```

### Iteration Methods (Return New Array/Value)

```javascript
arr.forEach(fn); // Execute for each (no return)
arr.map(fn); // Transform each element
arr.filter(fn); // Keep elements that pass test
arr.reduce(fn, init); // Reduce to single value
arr.find(fn); // First element that passes
arr.findIndex(fn); // Index of first match
arr.some(fn); // True if any pass
arr.every(fn); // True if all pass
arr.flatMap(fn); // Map then flatten
```

### Destructuring & Spread

```javascript
const [a, b] = arr; // Destructure
const [a, ...rest] = arr; // Rest
const [a = 1] = arr; // Default
const copy = [...arr]; // Copy (spread)
const merged = [...a, ...b]; // Merge (spread)
```

### Common Patterns

```javascript
// Remove duplicates
[...new Set(arr)]

// Flatten array
arr.flat(Infinity)
arr.reduce((a, b) => a.concat(b), [])

// Remove falsy values
arr.filter(Boolean)

// Sum array
arr.reduce((sum, n) => sum + n, 0)

// Last element
arr[arr.length - 1]
arr.at(-1)

// Shallow copy
[...arr]
arr.slice()
```

---

