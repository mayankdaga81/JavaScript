# Section 10: DOM & Browser APIs

## Key Definitions

### What is the DOM?

**DOM (Document Object Model)** is a programming interface for HTML documents. It represents the page structure as a tree of objects that JavaScript can manipulate to change document structure, style, and content.

### What is an Event?

**Event** is an action or occurrence that happens in the browser (like clicks, key presses, page load) that JavaScript can respond to.

### What are Browser APIs?

**Browser APIs** are built-in interfaces provided by browsers that allow JavaScript to interact with browser features like storage, geolocation, notifications, etc.

---

## 1. DOM Selection

### Selecting Single Elements

**getElementById:**

```javascript
// Get element by ID (returns single element or null)
const header = document.getElementById("header");
console.log(header);

// Note: Only works on document, not on other elements
```

**querySelector:**

```javascript
// Get first matching element using CSS selector
const button = document.querySelector(".btn");
const firstParagraph = document.querySelector("p");
const specificDiv = document.querySelector("#container .content");

// Returns null if not found
if (button) {
  console.log(button.textContent);
}
```

---

### Selecting Multiple Elements

**getElementsByClassName:**

```javascript
// Returns HTMLCollection (live - auto-updates)
const buttons = document.getElementsByClassName("btn");
console.log(buttons.length);

// HTMLCollection is array-like but NOT an array
// Convert to array for array methods
const buttonsArray = Array.from(buttons);
buttonsArray.forEach((btn) => console.log(btn));
```

**getElementsByTagName:**

```javascript
// Returns HTMLCollection (live)
const paragraphs = document.getElementsByTagName("p");
const allDivs = document.getElementsByTagName("div");

// Get all elements
const allElements = document.getElementsByTagName("*");
```

**querySelectorAll:**

```javascript
// Returns NodeList (static - doesn't auto-update)
const buttons = document.querySelectorAll(".btn");
const items = document.querySelectorAll("ul li");

// NodeList has forEach (unlike HTMLCollection)
buttons.forEach((btn) => {
  console.log(btn.textContent);
});

// Convert to array for other methods
const buttonsArray = [...buttons];
const mapped = buttonsArray.map((btn) => btn.textContent);
```

**Key Differences:**

```javascript
/*
querySelector/querySelectorAll:
✓ Use CSS selectors (more flexible)
✓ querySelectorAll has forEach
✗ Static NodeList (doesn't update)

getElementById/getElementsByClassName/getElementsByTagName:
✓ Slightly faster
✓ Live HTMLCollection (auto-updates)
✗ No forEach on HTMLCollection
✗ Less flexible selection
*/
```

---

## 2. DOM Traversal

### Parent, Child, Sibling Navigation

**Parent Elements:**

```javascript
const child = document.querySelector(".child");

// Parent element
const parent = child.parentElement;
const parentNode = child.parentNode; // Similar but includes non-element nodes

// Closest ancestor matching selector
const container = child.closest(".container");
const form = child.closest("form");
```

**Child Elements:**

```javascript
const parent = document.querySelector(".parent");

// All children
const children = parent.children; // HTMLCollection (elements only)
const childNodes = parent.childNodes; // NodeList (includes text nodes)

// First/Last child
const firstChild = parent.firstElementChild;
const lastChild = parent.lastElementChild;

// Check if has children
if (parent.hasChildNodes()) {
  console.log("Has children");
}
```

**Sibling Elements:**

```javascript
const element = document.querySelector(".middle");

// Next/Previous sibling
const nextSibling = element.nextElementSibling;
const prevSibling = element.previousElementSibling;
```

**Practical Traversal Example:**

```javascript
// Navigate to find specific element
const button = document.querySelector(".submit-btn");
const form = button.closest("form");
const inputs = form.querySelectorAll("input");

inputs.forEach((input) => {
  console.log(input.value);
});
```

---

## 3. DOM Manipulation

### Creating Elements

```javascript
// Create new element
const div = document.createElement("div");
const paragraph = document.createElement("p");
const button = document.createElement("button");

// Set content
div.textContent = "Hello World";
div.innerHTML = "<strong>Bold Text</strong>";

// Set attributes
div.id = "myDiv";
div.className = "container";
button.setAttribute("type", "submit");
button.setAttribute("data-id", "123");

// Create text node
const textNode = document.createTextNode("Plain text");
```

---

### Adding Elements to DOM

```javascript
const container = document.querySelector(".container");
const newDiv = document.createElement("div");
newDiv.textContent = "New content";

// Append to end
container.appendChild(newDiv);

// Prepend to beginning
container.prepend(newDiv);

// Insert at specific position
const reference = container.children[2];
container.insertBefore(newDiv, reference);

// Modern methods
container.append("text", newDiv, "more text"); // Can add multiple nodes/strings
container.before(newDiv); // Insert before container
container.after(newDiv); // Insert after container
```

---

### Modifying Elements

**Text Content:**

```javascript
const element = document.querySelector(".content");

// Get/Set text content (safe - escapes HTML)
element.textContent = "New text";
console.log(element.textContent);

// Get/Set HTML content (unsafe - can execute scripts)
element.innerHTML = "<strong>Bold</strong> text";

// innerText vs textContent
// innerText: returns visible text, respects CSS styling
// textContent: returns all text, including hidden elements
```

**Attributes:**

```javascript
const img = document.querySelector("img");

// Get attribute
const src = img.getAttribute("src");
const alt = img.getAttribute("alt");

// Set attribute
img.setAttribute("src", "new-image.jpg");
img.setAttribute("alt", "New image");

// Remove attribute
img.removeAttribute("alt");

// Check if has attribute
if (img.hasAttribute("src")) {
  console.log("Has src");
}

// Direct property access (recommended for standard attributes)
img.src = "image.jpg";
img.alt = "Description";
```

**Classes:**

```javascript
const element = document.querySelector(".box");

// Add class
element.classList.add("active");
element.classList.add("highlight", "selected"); // Multiple

// Remove class
element.classList.remove("active");

// Toggle class
element.classList.toggle("visible"); // Add if absent, remove if present

// Check if has class
if (element.classList.contains("active")) {
  console.log("Is active");
}

// Replace class
element.classList.replace("old-class", "new-class");

// Old way (don't use)
element.className = "new-class"; // Overwrites all classes
element.className += " another-class"; // Awkward
```

**Styles:**

```javascript
const element = document.querySelector(".box");

// Set inline styles
element.style.color = "red";
element.style.backgroundColor = "blue";
element.style.fontSize = "20px";

// Get computed style (includes CSS from stylesheets)
const styles = window.getComputedStyle(element);
console.log(styles.color);
console.log(styles.fontSize);

// Set multiple styles
Object.assign(element.style, {
  color: "red",
  backgroundColor: "blue",
  fontSize: "20px",
  padding: "10px",
});

// Remove style
element.style.color = "";
element.style.removeProperty("background-color");
```

**Data Attributes:**

```javascript
// HTML: <div data-user-id="123" data-role="admin"></div>
const element = document.querySelector("div");

// Access via dataset
console.log(element.dataset.userId); // "123"
console.log(element.dataset.role); // "admin"

// Set data attribute
element.dataset.status = "active";

// Delete data attribute
delete element.dataset.role;

// Or use getAttribute/setAttribute
element.getAttribute("data-user-id");
element.setAttribute("data-user-id", "456");
```

---

### Removing Elements

```javascript
const element = document.querySelector(".remove-me");

// Modern way
element.remove();

// Old way (don't use)
element.parentElement.removeChild(element);

// Remove all children
const container = document.querySelector(".container");
container.innerHTML = ""; // Quick but loses event listeners

// Better: remove children properly
while (container.firstChild) {
  container.removeChild(container.firstChild);
}
```

---

### Cloning Elements

```javascript
const original = document.querySelector(".original");

// Shallow clone (without children)
const shallowClone = original.cloneNode(false);

// Deep clone (with all children)
const deepClone = original.cloneNode(true);

// Cloned elements lose event listeners
// Need to re-attach them
```

---

## 4. Events

### Adding Event Listeners

**addEventListener (Recommended):**

```javascript
const button = document.querySelector("button");

// Add event listener
button.addEventListener("click", function (event) {
  console.log("Button clicked!");
  console.log(event); // Event object
});

// Arrow function
button.addEventListener("click", (event) => {
  console.log("Clicked!");
});

// Named function (can be removed later)
function handleClick(event) {
  console.log("Clicked!");
}

button.addEventListener("click", handleClick);

// Multiple listeners on same event
button.addEventListener("click", handler1);
button.addEventListener("click", handler2); // Both will execute
```

**Removing Event Listeners:**

```javascript
const button = document.querySelector("button");

function handleClick() {
  console.log("Clicked!");
}

button.addEventListener("click", handleClick);

// Remove listener (must use same function reference)
button.removeEventListener("click", handleClick);

// Anonymous functions can't be removed
button.addEventListener("click", () => {}); // Can't remove this!
```

**Old Ways (Don't Use):**

```javascript
// Inline HTML (bad - mixes JS with HTML)
// <button onclick="handleClick()">Click</button>

// Property assignment (only one handler per event)
button.onclick = function () {
  console.log("Clicked!");
};

button.onclick = function () {
  console.log("Different handler"); // Overwrites previous!
};
```

---

### Common Events

```javascript
const input = document.querySelector("input");
const form = document.querySelector("form");

// Mouse events
element.addEventListener("click", handler);
element.addEventListener("dblclick", handler);
element.addEventListener("mousedown", handler);
element.addEventListener("mouseup", handler);
element.addEventListener("mousemove", handler);
element.addEventListener("mouseenter", handler); // Doesn't bubble
element.addEventListener("mouseleave", handler); // Doesn't bubble
element.addEventListener("mouseover", handler); // Bubbles
element.addEventListener("mouseout", handler); // Bubbles

// Keyboard events
input.addEventListener("keydown", handler); // Key pressed
input.addEventListener("keyup", handler); // Key released
input.addEventListener("keypress", handler); // Deprecated

// Form events
input.addEventListener("input", handler); // Value changed (immediate)
input.addEventListener("change", handler); // Value changed (on blur)
input.addEventListener("focus", handler);
input.addEventListener("blur", handler);
form.addEventListener("submit", handler);

// Window events
window.addEventListener("load", handler); // All resources loaded
window.addEventListener("DOMContentLoaded", handler); // DOM ready
window.addEventListener("resize", handler);
window.addEventListener("scroll", handler);

// Document events
document.addEventListener("DOMContentLoaded", handler);
```

---

### Event Object

```javascript
button.addEventListener("click", (event) => {
  // Event properties
  console.log(event.type); // "click"
  console.log(event.target); // Element that triggered event
  console.log(event.currentTarget); // Element listener is attached to
  console.log(event.timeStamp); // When event occurred

  // Mouse position
  console.log(event.clientX, event.clientY); // Relative to viewport
  console.log(event.pageX, event.pageY); // Relative to page
  console.log(event.screenX, event.screenY); // Relative to screen

  // Modifier keys
  console.log(event.ctrlKey); // Ctrl pressed?
  console.log(event.shiftKey); // Shift pressed?
  console.log(event.altKey); // Alt pressed?
  console.log(event.metaKey); // Meta/Cmd pressed?

  // Prevent default behavior
  event.preventDefault(); // e.g., prevent form submission

  // Stop propagation
  event.stopPropagation(); // Stop bubbling to parents
  event.stopImmediatePropagation(); // Stop other listeners on same element
});
```

**Keyboard Event Object:**

```javascript
input.addEventListener("keydown", (event) => {
  console.log(event.key); // "a", "Enter", "ArrowUp"
  console.log(event.code); // "KeyA", "Enter", "ArrowUp"
  console.log(event.keyCode); // Deprecated

  // Check specific keys
  if (event.key === "Enter") {
    console.log("Enter pressed");
  }

  if (event.ctrlKey && event.key === "s") {
    event.preventDefault(); // Prevent browser save
    console.log("Custom save");
  }
});
```

---

### Event Propagation (Bubbling & Capturing)

**What this demonstrates:** Events propagate in two phases: capturing (top-down) and bubbling (bottom-up).

```javascript
/*
Event Propagation Order:
1. CAPTURING PHASE: window → document → html → body → parent → child
2. TARGET PHASE: Event reaches target element
3. BUBBLING PHASE: child → parent → body → html → document → window
*/

const parent = document.querySelector(".parent");
const child = document.querySelector(".child");

// Default: bubbling phase (3rd parameter = false or omitted)
parent.addEventListener("click", () => {
  console.log("Parent clicked (bubbling)");
});

child.addEventListener("click", () => {
  console.log("Child clicked");
});

// Clicking child logs:
// "Child clicked"
// "Parent clicked (bubbling)"  (event bubbles up)

// Capturing phase (3rd parameter = true)
parent.addEventListener(
  "click",
  () => {
    console.log("Parent clicked (capturing)");
  },
  true,
);

child.addEventListener(
  "click",
  () => {
    console.log("Child clicked");
  },
  true,
);

// Clicking child logs:
// "Parent clicked (capturing)"  (captures on way down)
// "Child clicked"
```

**Stop Propagation:**

```javascript
child.addEventListener("click", (event) => {
  event.stopPropagation(); // Prevents bubbling to parent
  console.log("Child clicked");
});

parent.addEventListener("click", () => {
  console.log("Parent clicked"); // Won't execute
});
```

---

### Event Delegation

**What this demonstrates:** Attach one listener to a parent instead of many listeners to children (efficient and works for dynamic elements).

```javascript
// ❌ BAD: Adding listener to each item
const items = document.querySelectorAll("li");
items.forEach((item) => {
  item.addEventListener("click", () => {
    console.log(item.textContent);
  });
});
// Problems: inefficient, doesn't work for dynamically added items

// ✓ GOOD: Event delegation
const list = document.querySelector("ul");
list.addEventListener("click", (event) => {
  // Check if clicked element is an LI
  if (event.target.tagName === "LI") {
    console.log(event.target.textContent);
  }
});
// Benefits: one listener, works for future items

// Practical example with delegation
const todoList = document.querySelector(".todo-list");

todoList.addEventListener("click", (event) => {
  // Delete button clicked
  if (event.target.classList.contains("delete-btn")) {
    const todoItem = event.target.closest(".todo-item");
    todoItem.remove();
  }

  // Checkbox clicked
  if (event.target.type === "checkbox") {
    const todoItem = event.target.closest(".todo-item");
    todoItem.classList.toggle("completed");
  }
});
```

---

## 5. Forms

### Form Handling

**Submit Event:**

```javascript
const form = document.querySelector("form");

form.addEventListener("submit", (event) => {
  event.preventDefault(); // Prevent page reload

  // Get form data
  const formData = new FormData(form);

  // Access individual fields
  const username = formData.get("username");
  const email = formData.get("email");

  // Or access inputs directly
  const usernameInput = form.querySelector('[name="username"]');
  console.log(usernameInput.value);

  // Convert FormData to object
  const data = Object.fromEntries(formData);
  console.log(data);
});
```

**Input Events:**

```javascript
const input = document.querySelector("input");

// Input event (fires on every change)
input.addEventListener("input", (event) => {
  console.log(event.target.value); // Current value
});

// Change event (fires on blur after change)
input.addEventListener("change", (event) => {
  console.log("Changed to:", event.target.value);
});

// Focus/Blur
input.addEventListener("focus", () => {
  console.log("Input focused");
});

input.addEventListener("blur", () => {
  console.log("Input lost focus");
});
```

---

### Form Validation

**HTML5 Validation:**

```javascript
// HTML: <input type="email" required minlength="3" maxlength="50">

const input = document.querySelector("input");

// Check validity
console.log(input.validity.valid); // Overall validity
console.log(input.validity.valueMissing); // Required but empty
console.log(input.validity.typeMismatch); // Wrong type (e.g., invalid email)
console.log(input.validity.tooShort);
console.log(input.validity.tooLong);

// Get validation message
console.log(input.validationMessage);

// Custom validation
input.setCustomValidity("Username must be unique");
console.log(input.validationMessage); // "Username must be unique"

// Clear custom validation
input.setCustomValidity("");
```

**Custom Validation:**

```javascript
const form = document.querySelector("form");
const passwordInput = form.querySelector('[name="password"]');
const confirmInput = form.querySelector('[name="confirm"]');

form.addEventListener("submit", (event) => {
  // Clear previous errors
  passwordInput.setCustomValidity("");
  confirmInput.setCustomValidity("");

  // Validate passwords match
  if (passwordInput.value !== confirmInput.value) {
    confirmInput.setCustomValidity("Passwords must match");
    event.preventDefault();
    return;
  }

  // Validate password strength
  if (passwordInput.value.length < 8) {
    passwordInput.setCustomValidity("Password must be at least 8 characters");
    event.preventDefault();
    return;
  }

  // Form is valid
  console.log("Form submitted!");
});
```

**Display Errors:**

```javascript
const form = document.querySelector("form");

form.addEventListener("submit", (event) => {
  event.preventDefault();

  // Clear previous errors
  document.querySelectorAll(".error").forEach((el) => el.remove());

  // Validate each input
  const inputs = form.querySelectorAll("input[required]");
  let isValid = true;

  inputs.forEach((input) => {
    if (!input.checkValidity()) {
      isValid = false;

      // Create error message
      const error = document.createElement("div");
      error.className = "error";
      error.textContent = input.validationMessage;
      input.parentElement.appendChild(error);
    }
  });

  if (isValid) {
    console.log("Form is valid!");
  }
});
```

---

## 6. Browser APIs

### Local Storage & Session Storage

**localStorage (Persistent):**

```javascript
// Store data (survives browser restart)
localStorage.setItem("username", "John");
localStorage.setItem("theme", "dark");

// Retrieve data
const username = localStorage.getItem("username");
console.log(username); // "John"

// Remove item
localStorage.removeItem("username");

// Clear all
localStorage.clear();

// Check if key exists
if (localStorage.getItem("theme")) {
  console.log("Theme preference saved");
}

// Store objects (must stringify)
const user = { name: "John", age: 30 };
localStorage.setItem("user", JSON.stringify(user));

// Retrieve objects (must parse)
const storedUser = JSON.parse(localStorage.getItem("user"));
console.log(storedUser.name); // "John"
```

**sessionStorage (Temporary):**

```javascript
// Same API as localStorage but data cleared when tab closes
sessionStorage.setItem("tempData", "value");
const tempData = sessionStorage.getItem("tempData");
sessionStorage.removeItem("tempData");
sessionStorage.clear();
```

**Practical Example:**

```javascript
// Save form data
const form = document.querySelector("form");

form.addEventListener("input", () => {
  const formData = new FormData(form);
  const data = Object.fromEntries(formData);
  localStorage.setItem("formData", JSON.stringify(data));
});

// Restore form data on page load
window.addEventListener("load", () => {
  const saved = localStorage.getItem("formData");
  if (saved) {
    const data = JSON.parse(saved);
    Object.entries(data).forEach(([name, value]) => {
      const input = form.querySelector(`[name="${name}"]`);
      if (input) input.value = value;
    });
  }
});
```

---

### Fetch API

**Basic GET Request:**

```javascript
fetch("https://api.example.com/users")
  .then((response) => {
    if (!response.ok) {
      throw new Error("Network response was not ok");
    }
    return response.json();
  })
  .then((data) => {
    console.log(data);
  })
  .catch((error) => {
    console.error("Error:", error);
  });

// With async/await
async function getUsers() {
  try {
    const response = await fetch("https://api.example.com/users");
    if (!response.ok) {
      throw new Error("Network response was not ok");
    }
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error("Error:", error);
  }
}
```

**POST Request:**

```javascript
async function createUser(userData) {
  try {
    const response = await fetch("https://api.example.com/users", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(userData),
    });

    if (!response.ok) {
      throw new Error("Failed to create user");
    }

    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Error:", error);
  }
}

// Usage
createUser({ name: "John", email: "john@example.com" });
```

**Other HTTP Methods:**

```javascript
// PUT (update)
fetch("https://api.example.com/users/1", {
  method: "PUT",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ name: "Updated Name" }),
});

// PATCH (partial update)
fetch("https://api.example.com/users/1", {
  method: "PATCH",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ email: "new@example.com" }),
});

// DELETE
fetch("https://api.example.com/users/1", {
  method: "DELETE",
});
```

---

### Window Object

```javascript
// Window dimensions
console.log(window.innerWidth); // Viewport width
console.log(window.innerHeight); // Viewport height
console.log(window.outerWidth); // Browser window width
console.log(window.outerHeight); // Browser window height

// Scroll position
console.log(window.scrollX); // Horizontal scroll
console.log(window.scrollY); // Vertical scroll
console.log(window.pageYOffset); // Same as scrollY

// Scroll to position
window.scrollTo(0, 0); // Top of page
window.scrollTo({ top: 500, behavior: "smooth" });

// Scroll by amount
window.scrollBy(0, 100); // Scroll down 100px

// Location (URL)
console.log(window.location.href); // Full URL
console.log(window.location.protocol); // "https:"
console.log(window.location.host); // "example.com:443"
console.log(window.location.hostname); // "example.com"
console.log(window.location.port); // "443"
console.log(window.location.pathname); // "/path/to/page"
console.log(window.location.search); // "?query=value"
console.log(window.location.hash); // "#section"

// Navigate
window.location.href = "https://example.com";
window.location.replace("https://example.com"); // No history entry
window.location.reload(); // Refresh page

// History
window.history.back(); // Go back
window.history.forward(); // Go forward
window.history.go(-2); // Go back 2 pages

// Open new window
const newWindow = window.open("https://example.com", "_blank");
newWindow.close(); // Close window

// Dialog boxes
alert("Message");
const result = confirm("Are you sure?"); // Returns true/false
const input = prompt("Enter your name:", "Default"); // Returns string or null

// Timers (covered in async section)
const timeoutId = setTimeout(() => {}, 1000);
const intervalId = setInterval(() => {}, 1000);
clearTimeout(timeoutId);
clearInterval(intervalId);
```

---

### Other Browser APIs

**Geolocation:**

```javascript
if ("geolocation" in navigator) {
  navigator.geolocation.getCurrentPosition(
    (position) => {
      console.log("Latitude:", position.coords.latitude);
      console.log("Longitude:", position.coords.longitude);
      console.log("Accuracy:", position.coords.accuracy);
    },
    (error) => {
      console.error("Error:", error.message);
    },
    {
      enableHighAccuracy: true,
      timeout: 5000,
      maximumAge: 0,
    },
  );
}
```

**Clipboard API:**

```javascript
// Copy to clipboard
async function copyToClipboard(text) {
  try {
    await navigator.clipboard.writeText(text);
    console.log("Copied to clipboard");
  } catch (error) {
    console.error("Failed to copy:", error);
  }
}

// Read from clipboard
async function readClipboard() {
  try {
    const text = await navigator.clipboard.readText();
    console.log("Clipboard content:", text);
  } catch (error) {
    console.error("Failed to read:", error);
  }
}
```

**Notifications:**

```javascript
// Request permission
async function requestNotificationPermission() {
  const permission = await Notification.requestPermission();
  return permission === "granted";
}

// Show notification
function showNotification(title, options) {
  if (Notification.permission === "granted") {
    new Notification(title, {
      body: "Notification message",
      icon: "icon.png",
      ...options,
    });
  }
}

// Usage
requestNotificationPermission().then((granted) => {
  if (granted) {
    showNotification("Hello!", { body: "This is a notification" });
  }
});
```

---

## Quick Reference

### Selection Methods

```javascript
// Single element
document.getElementById("id");
document.querySelector(".class");

// Multiple elements
document.getElementsByClassName("class"); // HTMLCollection (live)
document.getElementsByTagName("div"); // HTMLCollection (live)
document.querySelectorAll(".class"); // NodeList (static)
```

### Element Manipulation

```javascript
// Create
const el = document.createElement("div");

// Add to DOM
parent.appendChild(el);
parent.prepend(el);
parent.insertBefore(el, reference);

// Modify
el.textContent = "Text";
el.innerHTML = "<strong>HTML</strong>";
el.setAttribute("id", "myId");
el.classList.add("class");
el.style.color = "red";

// Remove
el.remove();
```

### Event Handling

```javascript
// Add listener
element.addEventListener("click", handler);

// Remove listener
element.removeEventListener("click", handler);

// Common events
(click, dblclick, mouseenter, mouseleave);
(keydown, keyup);
(input, change, focus, blur);
submit;
(load, DOMContentLoaded, resize, scroll);
```

### Storage

```javascript
// localStorage (persistent)
localStorage.setItem("key", "value");
localStorage.getItem("key");
localStorage.removeItem("key");
localStorage.clear();

// sessionStorage (tab lifetime)
sessionStorage.setItem("key", "value");
sessionStorage.getItem("key");

// Store objects
localStorage.setItem("obj", JSON.stringify(obj));
const obj = JSON.parse(localStorage.getItem("obj"));
```

### Fetch API

```javascript
// GET
const response = await fetch(url);
const data = await response.json();

// POST
fetch(url, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(data)
});

// PUT, PATCH, DELETE
fetch(url, { method: 'PUT', ... });
fetch(url, { method: 'PATCH', ... });
fetch(url, { method: 'DELETE' });
```

---

**That's Section 10!** You now understand how to interact with the DOM, handle events, and use browser APIs. Combined with Sections 1-9, you have a complete JavaScript foundation for TypeScript! 🎯🚀
