# Section 6: Asynchronous JavaScript

## 1. Callbacks

Functions passed as arguments to be executed later.

### Basic Callbacks

```javascript
function fetchData(callback) {
  setTimeout(() => {
    const data = { name: "John", age: 30 };
    callback(data);
  }, 1000);
}

fetchData((data) => {
  console.log(data); // { name: "John", age: 30 }
});

// We call fetchData and pass it a function directly.
// Inside fetchData, setTimeout waits one second.
// After one second, it creates the data object and calls the callback function with this data.
// The callback function is the one we passed in, so it runs and logs the data.
// This shows how a function can be passed and executed later when data is ready.
```

### setTimeout & clearTimeout

**Simple Explanation**: `setTimeout` lets you delay running a function. Think of it like setting a timer - "run this function after X milliseconds."

#### Basic setTimeout

**What this demonstrates:** Basic setTimeout usage showing three ways to use it - with arrow function, named function, and capturing the timer ID for cancellation.

```javascript
// Syntax: setTimeout(function, delay_in_milliseconds)

// Execute after 2 seconds
setTimeout(() => {
  console.log("This runs after 2 seconds");
}, 2000);

// With named function
function greet() {
  console.log("Hello!");
}
setTimeout(greet, 1000); // Runs greet after 1 second

// Returns a timer ID (used to cancel)
const timerId = setTimeout(() => {
  console.log("You won't see this if cancelled");
}, 3000);
```

#### clearTimeout

**What this demonstrates:** How to cancel a scheduled timer before it executes using the timer ID returned by setTimeout.

```javascript
const timerId = setTimeout(() => {
  console.log("This won't run");
}, 5000);

// Cancel the timer
clearTimeout(timerId);
console.log("Timer cancelled");
```

#### Practical Example: HTML Page

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>setTimeout Example</title>
  </head>
  <body>
    <h1>Chai aur code</h1>
    <button id="stop">Stop</button>
  </body>
  <script>
    const sayHitesh = function () {
      console.log("Hitesh");
    };

    const changeText = function () {
      document.querySelector("h1").innerHTML = "best JS series";
    };

    // Schedule text change after 2 seconds
    const changeMe = setTimeout(changeText, 2000);

    // Cancel the scheduled change if button clicked
    document.querySelector("#stop").addEventListener("click", function () {
      clearTimeout(changeMe);
      console.log("STOPPED");
    });
  </script>
</html>
```

**What happens:**

1. Page loads with "Chai aur code" heading
2. `setTimeout` schedules `changeText` to run after 2000ms (2 seconds)
3. If you wait 2 seconds: heading changes to "best JS series"
4. If you click "Stop" button before 2 seconds: timer is cancelled, heading stays unchanged

**Real-World Use Cases:**

- Showing notifications that auto-dismiss
- Debouncing user input (wait before searching)
- Auto-saving drafts
- Delayed redirects after actions
- Animation delays

#### setInterval (Bonus)

**What this demonstrates:** Running a function repeatedly at fixed intervals. Use clearInterval() to stop it. Includes a countdown timer example.

**Key difference:** setInterval runs repeatedly, setTimeout runs once.

```javascript
// Runs every 1 second
const intervalId = setInterval(() => {
  console.log("Tick");
}, 1000);

// Stop after 5 seconds
setTimeout(() => {
  clearInterval(intervalId);
  console.log("Stopped");
}, 5000);

// Example: Countdown timer
let count = 10;
const countdown = setInterval(() => {
  console.log(count);
  count--;

  if (count < 0) {
    clearInterval(countdown);
    console.log("Done!");
  }
}, 1000);
```

**setTimeout vs setInterval:**

- `setTimeout`: Runs **once** after delay
- `setInterval`: Runs **repeatedly** at intervals

---

### Error-First Callbacks (Node.js Convention)

**What this demonstrates:** A common Node.js pattern where the first parameter of a callback is always an error (if any), and the second is the result. This standardizes error handling.

```javascript
function fetchUser(id, callback) {
  setTimeout(() => {
    if (id < 1) {
      callback(new Error("Invalid ID"), null);
    } else {
      callback(null, { id, name: "John" });
    }
  }, 1000);
}

fetchUser(1, (error, user) => {
  if (error) {
    console.error(error.message);
    return;
  }
  console.log(user); // { id: 1, name: "John" }
});
```

### Callback Hell (Pyramid of Doom)

**What this demonstrates:** The problem with deeply nested callbacks - code becomes hard to read, maintain, and debug. Each async operation depends on the previous one, creating a "pyramid" shape.

**Issue:** Multiple nested callbacks become unreadable and error-prone.

```javascript
// ✗ Callback hell
fetchUser(1, (error, user) => {
  if (error) return console.error(error);

  fetchPosts(user.id, (error, posts) => {
    if (error) return console.error(error);

    fetchComments(posts[0].id, (error, comments) => {
      if (error) return console.error(error);

      fetchAuthor(comments[0].authorId, (error, author) => {
        if (error) return console.error(error);

        console.log(author);
        // 4 levels deep!
      });
    });
  });
});
```

**Problems with Callbacks**:

- Hard to read (nested structure)
- Hard to handle errors (repeat error checking)
- Hard to sequence operations
- Hard to run in parallel

**Solution**: Promises and async/await

---

## 2. Promises

Object representing the eventual completion or failure of an async operation.

### Promise States

- **Pending**: Initial state, operation not completed
- **Fulfilled**: Operation completed successfully
- **Rejected**: Operation failed

### Creating Promises

**What this demonstrates:** How to create a Promise that wraps an asynchronous operation. The executor function runs immediately when the Promise is created.

**Key Points:**

- Executor function runs immediately when Promise is created
- Use `resolve()` to mark success, `reject()` to mark failure
- Any async operation (setTimeout, fetch, etc.) inside makes it truly async
- If you call resolve/reject immediately without async code, it runs synchronously
- Promise handlers (.then/.catch) run after current synchronous code finishes

```javascript
const promise = new Promise((resolve, reject) => {
  // Async operation
  setTimeout(() => {
    const success = true;

    if (success) {
      resolve("Operation successful!"); // Fulfilled
    } else {
      reject(new Error("Operation failed")); // Rejected
    }
  }, 1000);
});
```

### Consuming Promises: then/catch/finally

#### then()

**What this demonstrates:** How to handle resolved (successful) promises. Each `.then()` returns a new promise, allowing chaining.

```javascript
promise
  .then((result) => {
    console.log(result); // "Operation successful!"
    return result.toUpperCase();
  })
  .then((uppercased) => {
    console.log(uppercased); // "OPERATION SUCCESSFUL!"
  });
```

#### catch()

**What this demonstrates:** How to handle rejected (failed) promises. Catches any error in the promise chain.

```javascript
promise
  .then((result) => {
    console.log(result);
  })
  .catch((error) => {
    console.error(error.message); // Handle error
  });
```

#### finally()

**What this demonstrates:** Code that runs regardless of promise success or failure. Perfect for cleanup operations like hiding loading spinners.

```javascript
promise
  .then((result) => console.log(result))
  .catch((error) => console.error(error))
  .finally(() => {
    console.log("Cleanup or loading complete");
  });
```

### Chaining Promises

**What this demonstrates:** How to sequence multiple async operations cleanly without nesting. Each `.then()` waits for the previous one to complete. Much cleaner than callback hell.

```javascript
fetchUser(1)
  .then((user) => {
    console.log(user);
    return fetchPosts(user.id); // Return new promise
  })
  .then((posts) => {
    console.log(posts);
    return fetchComments(posts[0].id);
  })
  .then((comments) => {
    console.log(comments);
  })
  .catch((error) => {
    console.error("Error:", error); // Catches any error in chain
  })
  .finally(() => {
    console.log("All done");
  });
```

### Practical Example

**What this demonstrates:** A real-world promise wrapper around the fetch API with proper error handling for HTTP errors.

```javascript
function fetchData(url) {
  return new Promise((resolve, reject) => {
    fetch(url)
      .then((response) => {
        if (!response.ok) {
          reject(new Error(`HTTP error: ${response.status}`));
        }
        return response.json();
      })
      .then((data) => resolve(data))
      .catch((error) => reject(error));
  });
}

// Using the wrapper function to fetch data with error handling
fetchData("/api/users")
  .then((users) => console.log(users))
  .catch((error) => console.error(error));
```

### Creating Resolved/Rejected Promises

**What this demonstrates:** How to create promises that are already resolved or rejected. Useful for testing, or when you need to return a promise but the value is already known.

```javascript
// Immediately resolved
const resolved = Promise.resolve(42);
resolved.then((value) => console.log(value)); // 42

// Immediately rejected
const rejected = Promise.reject(new Error("Failed"));
rejected.catch((error) => console.error(error.message)); // "Failed"

// Useful for testing or returning from functions
function getUserData(id) {
  if (id < 1) {
    return Promise.reject(new Error("Invalid ID"));
  }
  return Promise.resolve({ id, name: "John" });
}
```

---

## 3. async/await

Syntactic sugar over promises - makes async code look synchronous.

With Promises and many .then, .catch, and .finally blocks, code can quickly become hard to read and maintain.
To solve this, JavaScript introduced async functions and the await keyword.
You can add async to any function, and then use await inside it.
By default, an async function always returns a Promise, this happens automatically, you don’t need to specify it.
When you use await inside an async function, the code pauses at that line until the awaited Promise resolves, then continues to the next line.
This makes asynchronous code much easier to write and understand.

### Basic Syntax

**What this demonstrates:** How async functions automatically wrap their return value in a Promise. Any value you return is automatically wrapped in `Promise.resolve()`.

```javascript
// async function returns a Promise
async function fetchData() {
  return "Hello"; // Automatically wrapped in Promise.resolve()
}

// You can use .then() with async functions, but it is recommended to use await for cleaner code.
fetchData().then((data) => console.log(data)); // "Hello"

// This is equivalent to the async function above
// Shows that async automatically wraps return values in Promises
function fetchData() {
  return Promise.resolve("Hello");
}
```

### await Keyword

**What this demonstrates:** How `await` pauses function execution until a promise resolves, making async code look synchronous. Only works inside async functions.

```javascript
async function getUser() {
  const response = await fetch("/api/user"); // Wait for fetch
  const user = await response.json(); // Wait for json parsing
  console.log(user);
}

getUser();
```

### Error Handling with try/catch

**What this demonstrates:** Using try/catch for error handling with async/await - much cleaner than .catch() chains. The finally block always runs for cleanup.

```javascript
async function fetchUserData() {
  try {
    const response = await fetch("/api/user");

    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }

    const user = await response.json();
    return user;
  } catch (error) {
    console.error("Error fetching user:", error.message);
    throw error; // Re-throw if needed
  } finally {
    console.log("Fetch attempt completed");
  }
}

// Calling the async function
// Shows that async functions return promises, so you can use .then/.catch
fetchUserData()
  .then((user) => console.log(user))
  .catch((error) => console.error("Caught outside:", error));
```

### Sequential vs Parallel Execution

#### Sequential (one after another)

**What this demonstrates:** Sequential execution where each operation waits for the previous to complete. Total time is sum of all operations.

**Issue:** Slow - operations run one at a time even if they could run in parallel.

```javascript
async function sequential() {
  const user = await fetchUser(1); // Wait 1s
  const posts = await fetchPosts(user.id); // Wait 1s
  const comments = await fetchComments(posts[0].id); // Wait 1s
  // Total: 3s
  return { user, posts, comments };
}
```

#### Parallel (all at once)

**What this demonstrates:** Parallel execution by starting all promises before awaiting them. Total time is the longest operation, not the sum.

**Better approach:** Use `Promise.all()` for cleaner parallel execution.

```javascript
async function parallel() {
  // Start all requests simultaneously
  const userPromise = fetchUser(1);
  const postsPromise = fetchPosts(1);
  const commentsPromise = fetchComments(1);

  // Wait for all to complete
  const user = await userPromise; // Wait up to 1s
  const posts = await postsPromise; // May already be done
  const comments = await commentsPromise; // May already be done
  // Total: ~1s (all run in parallel)

  return { user, posts, comments };
}

// Cleaner approach using Promise.all()
// Starts all promises simultaneously and waits for all to complete
async function parallelBetter() {
  const [user, posts, comments] = await Promise.all([
    fetchUser(1),
    fetchPosts(1),
    fetchComments(1),
  ]);

  return { user, posts, comments };
}
```

### Async/Await with Loops

#### Sequential Loop

**What this demonstrates:** Processing array items one at a time. Each iteration waits for the previous to complete.

**Use when:** Operations must be done in order or you want to avoid overwhelming a server.

```javascript
async function processUsers(userIds) {
  const results = [];

  for (const id of userIds) {
    const user = await fetchUser(id); // Wait for each
    results.push(user);
  }

  return results;
}
```

#### Parallel Loop

**What this demonstrates:** Processing array items in parallel using `Promise.all()`. Much faster when operations are independent.

**Use when:** Operations can run simultaneously and order doesn't matter.

```javascript
async function processUsersParallel(userIds) {
  // Start all fetches
  const promises = userIds.map((id) => fetchUser(id));

  // Wait for all
  const results = await Promise.all(promises);

  return results;
}

// More concise version - combines map and Promise.all in one line
async function processUsersParallel(userIds) {
  return await Promise.all(userIds.map((id) => fetchUser(id)));
}
```

### Top-Level await (ES2022)

**What this demonstrates:** Using await outside of async functions at the module level. Only works in ES modules.

**Use case:** Loading config or initial data before module exports.

```javascript
// In a module file
const response = await fetch("/api/config");
const config = await response.json();

export default config;
```

### Common Patterns

#### Retry Logic

**What this demonstrates:** Automatically retrying a failed operation up to N times before giving up. Common pattern for unreliable network requests.

```javascript
async function fetchWithRetry(url, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      const response = await fetch(url);
      if (response.ok) return response;
    } catch (error) {
      if (i === retries - 1) throw error;
      await delay(1000); // Wait before retry
    }
  }
}

function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}
```

#### Timeout

**What this demonstrates:** Aborting a fetch request if it takes too long. Uses AbortController to cancel the request.

```javascript
async function fetchWithTimeout(url, timeout = 5000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeout);

  try {
    const response = await fetch(url, { signal: controller.signal });
    return response;
  } finally {
    clearTimeout(timeoutId);
  }
}
```

#### Conditional Awaiting

**What this demonstrates:** Conditionally using await based on logic. If data is already available (cached), return it immediately without awaiting.

```javascript
async function getData(useCache) {
  if (useCache) {
    return getCachedData(); // Synchronous
  }

  return await fetchData(); // Asynchronous
}
```

---

## 4. Error Handling

### try/catch with async/await

**What this demonstrates:** Basic error handling pattern for async/await. Catch block handles any errors, and you can return fallback values.

```javascript
async function fetchData() {
  try {
    const response = await fetch("/api/data");
    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Error:", error.message);
    return null; // Return fallback
  }
}
```

### Multiple try/catch Blocks

**What this demonstrates:** Using separate try/catch blocks for different operations allows you to handle failures independently and provide fallbacks for each.

```javascript
async function complexOperation() {
  let user;

  // Try to fetch user
  try {
    user = await fetchUser(1);
  } catch (error) {
    console.error("Failed to fetch user:", error);
    user = getDefaultUser();
  }

  // Try to fetch posts
  try {
    const posts = await fetchPosts(user.id);
    return { user, posts };
  } catch (error) {
    console.error("Failed to fetch posts:", error);
    return { user, posts: [] };
  }
}
```

### Promise catch() Method

**What this demonstrates:** Alternative to try/catch - using .catch() directly on the promise. Both approaches work, choose based on preference.

```javascript
// Alternative to try/catch
async function fetchData() {
  const data = await fetch("/api/data")
    .then((response) => response.json())
    .catch((error) => {
      console.error("Error:", error);
      return null;
    });

  return data;
}
```

### Error Types

**What this demonstrates:** Handling different types of errors (HTTP status codes, network errors) with specific logic for each case.

```javascript
async function fetchUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`);

    if (!response.ok) {
      if (response.status === 404) {
        throw new Error("User not found");
      } else if (response.status === 401) {
        throw new Error("Unauthorized");
      } else {
        throw new Error(`HTTP error: ${response.status}`);
      }
    }

    return await response.json();
  } catch (error) {
    if (error.name === "TypeError") {
      console.error("Network error:", error.message);
    } else {
      console.error("Error:", error.message);
    }
    throw error;
  }
}
```

### Custom Error Classes

**What this demonstrates:** Creating custom error types with additional properties (like statusCode) for better error handling and debugging.

```javascript
class APIError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.name = "APIError";
    this.statusCode = statusCode;
  }
}

async function fetchData() {
  const response = await fetch("/api/data");

  if (!response.ok) {
    throw new APIError("Fetch failed", response.status);
  }

  return response.json();
}

// Using the custom error class
// This shows how to check the error type and access custom properties
try {
  const data = await fetchData();
} catch (error) {
  if (error instanceof APIError) {
    console.error(`API Error ${error.statusCode}: ${error.message}`);
  } else {
    console.error("Unknown error:", error);
  }
}
```

### finally Block

**What this demonstrates:** Cleanup code that must run whether the operation succeeds or fails. Perfect for hiding loading spinners or releasing resources.

```javascript
async function fetchWithLoading() {
  showLoadingSpinner();

  try {
    const data = await fetch("/api/data");
    return data;
  } catch (error) {
    showError(error.message);
    throw error;
  } finally {
    hideLoadingSpinner(); // Always runs
  }
}
```

---

## 5. Promise Methods

### Promise.all()

**What this demonstrates:** Running multiple promises in parallel and waiting for ALL to complete. If any fails, the entire operation fails immediately (fail-fast).

**Use when:** You need all results and any failure should stop everything.

```javascript
const promise1 = Promise.resolve(3);
const promise2 = new Promise((resolve) => setTimeout(() => resolve(42), 100));
const promise3 = fetch("/api/data").then((r) => r.json());

Promise.all([promise1, promise2, promise3])
  .then(([result1, result2, result3]) => {
    console.log(result1); // 3
    console.log(result2); // 42
    console.log(result3); // API data
  })
  .catch((error) => {
    // If ANY promise rejects, catch is called immediately
    console.error("One of the promises failed:", error);
  });

// Using Promise.all with async/await for cleaner syntax
// Destructures the array of results into named variables
async function fetchAll() {
  try {
    const [users, posts, comments] = await Promise.all([
      fetchUsers(),
      fetchPosts(),
      fetchComments(),
    ]);

    return { users, posts, comments };
  } catch (error) {
    console.error("Failed:", error);
  }
}
```

**Use Case**: When you need ALL results and any failure should stop everything.

### Promise.allSettled()

**What this demonstrates:** Running multiple promises and waiting for ALL to finish, regardless of success or failure. Never rejects - you handle each result individually.

**Use when:** You want to try multiple operations and handle successes/failures separately.

```javascript
const promises = [
  Promise.resolve(1),
  Promise.reject(new Error("Failed")),
  Promise.resolve(3),
];

Promise.allSettled(promises).then((results) => {
  results.forEach((result) => {
    if (result.status === "fulfilled") {
      console.log("Success:", result.value);
    } else {
      console.error("Failed:", result.reason);
    }
  });
});

// Output:
// Success: 1
// Failed: Error: Failed
// Success: 3

// Using Promise.allSettled with async/await
// Separates successful and failed results for individual handling
async function fetchMultiple() {
  const results = await Promise.allSettled([
    fetchUsers(),
    fetchPosts(),
    fetchComments(),
  ]);

  const successful = results
    .filter((r) => r.status === "fulfilled")
    .map((r) => r.value);

  const failed = results
    .filter((r) => r.status === "rejected")
    .map((r) => r.reason);

  return { successful, failed };
}
```

**Use Case**: When you want to try multiple operations and handle each result independently.

### Promise.race()

**What this demonstrates:** Racing multiple promises - returns as soon as the FIRST one completes (resolve or reject). Useful for implementing timeouts.

**Use when:** You need the fastest response or want to implement timeout logic.

```javascript
const promise1 = new Promise((resolve) =>
  setTimeout(() => resolve("slow"), 500),
);
const promise2 = new Promise((resolve) =>
  setTimeout(() => resolve("fast"), 100),
);

Promise.race([promise1, promise2]).then((winner) => {
  console.log(winner); // "fast" (first to complete)
});

// Common timeout pattern using Promise.race
// Races the actual fetch against a timeout promise
async function fetchWithTimeout(url, timeout = 5000) {
  const fetchPromise = fetch(url);
  const timeoutPromise = new Promise((_, reject) =>
    setTimeout(() => reject(new Error("Timeout")), timeout),
  );

  return Promise.race([fetchPromise, timeoutPromise]);
}

// Using the timeout function
// If the endpoint takes longer than 3 seconds, it throws a timeout error
fetchWithTimeout("/api/slow-endpoint", 3000)
  .then((response) => response.json())
  .catch((error) => console.error(error.message)); // "Timeout" after 3s
```

**Use Case**: Timeouts, fastest response wins, racing multiple data sources.

### Promise.any() (ES2021)

**What this demonstrates:** Returns when FIRST promise succeeds. Ignores rejections until all fail. Perfect for trying multiple fallback sources.

**Use when:** You have multiple data sources and want the first successful response.

```javascript
const promises = [
  Promise.reject(new Error("Failed 1")),
  Promise.resolve("Success"),
  Promise.reject(new Error("Failed 2")),
];

Promise.any(promises)
  .then((result) => {
    console.log(result); // "Success" (first to resolve)
  })
  .catch((error) => {
    // Only if ALL promises reject
    console.error("All failed:", error);
  });

// Real-world use case: Trying multiple backup servers
// Fetches from the first server that responds successfully
async function fetchFromServers(path) {
  const servers = [
    "https://server1.com",
    "https://server2.com",
    "https://server3.com",
  ];

  try {
    const response = await Promise.any(
      servers.map((server) => fetch(`${server}${path}`)),
    );
    return response.json();
  } catch (error) {
    throw new Error("All servers failed");
  }
}
```

**Use Case**: Multiple fallback sources, first successful response wins.

### Comparison Table

| Method                 | Returns When   | Rejects When  | Use Case                        |
| ---------------------- | -------------- | ------------- | ------------------------------- |
| `Promise.all()`        | All resolve    | Any rejects   | Need all results, fail-fast     |
| `Promise.allSettled()` | All settle     | Never         | Try all, handle each separately |
| `Promise.race()`       | First settles  | First rejects | Timeout, fastest wins           |
| `Promise.any()`        | First resolves | All reject    | Fallback sources, first success |

---

## Quick Reference

### Timers (setTimeout/setInterval)

```javascript
// setTimeout - run once after delay
const timerId = setTimeout(fn, delay);
clearTimeout(timerId); // Cancel timer

// setInterval - run repeatedly
const intervalId = setInterval(fn, delay);
clearInterval(intervalId); // Stop repeating

// Examples
setTimeout(() => console.log("Hi"), 1000); // After 1s
setInterval(() => console.log("Tick"), 1000); // Every 1s
```

### Promises

```javascript
// Creating
new Promise((resolve, reject) => {
  if (success) resolve(value);
  else reject(error);
});

// Consuming
promise
  .then((result) => {})
  .catch((error) => {})
  .finally(() => {});

// Static methods
Promise.resolve(value);
Promise.reject(error);
```

### async/await

```javascript
// Declare async function
async function fn() {
  return value; // Wrapped in Promise
}

// Wait for promise
const result = await promise;

// Error handling
try {
  const data = await fetchData();
} catch (error) {
  console.error(error);
} finally {
  cleanup();
}
```

### Sequential vs Parallel

```javascript
// Sequential (slow)
const a = await fetchA();
const b = await fetchB();

// Parallel (fast)
const [a, b] = await Promise.all([fetchA(), fetchB()]);
```

### Promise Combinators

```javascript
Promise.all([p1, p2]); // All must succeed
Promise.allSettled([p1, p2]); // Wait for all, never fails
Promise.race([p1, p2]); // First to settle wins
Promise.any([p1, p2]); // First to resolve wins
```

### Common Patterns

```javascript
// Delay
const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));
await delay(1000);

// Retry
for (let i = 0; i < retries; i++) {
  try {
    return await fetchData();
  } catch (error) {
    if (i === retries - 1) throw error;
  }
}

// Timeout
Promise.race([
  fetchData(),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error("Timeout")), 5000),
  ),
]);

// Parallel mapping
await Promise.all(items.map((item) => processItem(item)));

// Sequential forEach
for (const item of items) {
  await processItem(item);
}
```

### Error Handling

```javascript
// try/catch (async/await)
try {
  const data = await fetch(url);
} catch (error) {
  console.error(error);
}

// .catch() (promises)
fetch(url)
  .then((r) => r.json())
  .catch((error) => console.error(error));

// finally (always runs)
try {
  await doWork();
} finally {
  cleanup();
}
```

---
