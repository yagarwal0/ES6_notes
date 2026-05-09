# JavaScript ES6+ Notes — React Prep

> Covers all core ES6 features needed before jumping into React. Each topic has a definition, example, and (where applicable) why React cares about it.

## Table of Contents

1. [`let`, `const`, `var`](#1-let-const-var)
2. [Arrow Functions](#2-arrow-functions)
3. [`this` Keyword Deep Dive](#3-this-keyword-deep-dive)
4. [Template Literals](#4-template-literals)
5. [Default Parameters](#5-default-parameters)
6. [Destructuring (Array + Object)](#6-destructuring)
7. [Object Shorthand](#7-object-shorthand)
8. [Spread & Rest Operators](#8-spread--rest)
9. [Import / Export](#9-import--export)
10. [Promises](#10-promises)
11. [async / await](#11-async--await)
12. [Array Methods (map, filter, reduce, find, forEach)](#12-array-methods)
13. [Optional Chaining (`?.`) & Nullish Coalescing (`??`)](#13-optional-chaining--nullish-coalescing)
14. [Short-Circuit Evaluation (`&&`, `||`)](#14-short-circuit-evaluation)
15. [Ternary Operator](#15-ternary-operator)
16. [Classes](#16-classes)
17. [Quick React Translation Table](#quick-react-translation-table)

---

## 1. `let`, `const`, `var`

**Definition:** Variable declaration keywords. ES6 introduced `let` and `const` to fix the scoping and mutability issues with `var`.

| Keyword | Scope | Reassignable? | Hoisting behavior |
|---|---|---|---|
| `var` | Function/Global | Yes | Hoisted, initialized to `undefined` |
| `let` | Block `{ }` | Yes | Hoisted but in *Temporal Dead Zone* (TDZ) |
| `const` | Block `{ }` | No (binding can't change) | Same as `let` (TDZ) |

### Examples

```js
// var — hoisted, no error (just undefined before assignment)
console.log(name);            // undefined
var name = "Yash Agarwal";
console.log(name);            // "Yash Agarwal"

// let — TDZ → ReferenceError if accessed before declaration
console.log(course);          // ❌ ReferenceError
let course = "M.Tech";
console.log(course);

// const — cannot reassign
const PI = 3.14;
PI = 3.14159;                 // ❌ TypeError

// BUT const objects/arrays can be MUTATED — only the binding is constant
const arr = [1, 2, 3];
arr.push(4);                  // ✅ works, arr is now [1,2,3,4]
arr = [5, 6];                 // ❌ TypeError — reassigning the binding
```

### Block scope demo

```js
if (true) {
  var a = 10;   // function-scoped → leaks out
  let b = 20;   // block-scoped → trapped inside { }
}
console.log(a); // 10
console.log(b); // ❌ ReferenceError
```

**React relevance:** Use `const` by default for components, hooks, props. Use `let` only when reassignment is needed. Avoid `var` entirely.

---

## 2. Arrow Functions

**Definition:** A shorter syntax for writing functions. They do NOT have their own `this`, `arguments`, or `prototype`.

### Syntax progression

```js
// ES5
var greet = function(name, age) {
  return "Hello " + name + " " + age;
};

// ES6 — full form
let greet = (name, age) => {
  return "Hello " + name + " " + age;
};

// ES6 — single param, parens optional
let greet = name => {
  return "Hello " + name;
};

// ES6 — implicit return (single expression, no braces, no `return`)
let sqr = num => num * num;
console.log(sqr(5));          // 25

// Returning an object literal — wrap in parentheses to disambiguate from function body
let makeUser = (name, age) => ({ name, age });
console.log(makeUser("Yash", 25)); // { name: "Yash", age: 25 }
```

**React relevance:** Arrow functions are the de-facto standard for React functional components, event handlers, and inline callbacks — because they don't bind their own `this`.

```jsx
<button onClick={() => setCount(count + 1)}>+1</button>
```

---

## 3. `this` Keyword Deep Dive

**Definition:** `this` refers to the *execution context* — what object is "calling" the function. Its value depends on **HOW** the function is called, not where it's defined.

### Key rule: regular function vs arrow function

```js
const obj = {
  name: "Yash",
  // Regular function — has its own `this`, points to obj when called as obj.regular()
  regular: function() {
    console.log(this.name);   // "Yash"
  },
  // Arrow function — NO own `this`, takes it from the enclosing lexical scope
  arrow: () => {
    console.log(this.name);   // undefined (this = module/global)
  }
};
obj.regular(); // "Yash"
obj.arrow();   // undefined
```

### The 4 binding rules (regular functions)

```js
// 1. Default binding — global object (window in browser) / undefined in strict mode
function show() { console.log(this); }
show();

// 2. Implicit binding — the object that owns the call
const user = {
  name: "Yash",
  greet() { console.log(this.name); }
};
user.greet();                 // "Yash" — `this` = user

// 3. Explicit binding — call / apply / bind
function intro() { console.log(this.name); }
intro.call({ name: "Dad" });  // "Dad"

// 4. `new` binding — `this` = the brand new object
function Person(n) { this.name = n; }
const p = new Person("Yash"); // this = p
```

### Arrow functions = lexical `this`

Arrow functions inherit `this` from the *enclosing scope* and **cannot be re-bound** with `call`, `apply`, or `bind`.

```js
const counter = {
  count: 0,
  start: function() {
    setInterval(() => {
      this.count++;            // ✅ arrow inherits `this` from start() → counter
      console.log(this.count);
    }, 1000);
  }
};
counter.start();
```

If you used a regular function inside `setInterval`, `this` would be `window`/`undefined` and `this.count++` would break.

**React relevance:** This is *exactly* why class components had `this.handleClick = this.handleClick.bind(this)` boilerplate — and why functional components + hooks are simpler. With hooks, you avoid `this` entirely.

---

## 4. Template Literals

**Definition:** String literals using backticks `` ` `` that allow embedded expressions and multi-line strings.

```js
// ES5 — concatenation
var name = "Yash";
console.log("Welcome " + name + "! Have a nice day");

// ES6 — template literal
const name = "Yash";
console.log(`Welcome ${name}! Have a nice day`);

// Multi-line — no \n needed
const html = `
  <div>
    <h1>${name}</h1>
    <p>M.Tech, IIT Jammu</p>
  </div>
`;

// Any expression works inside ${ }
const a = 5, b = 10;
console.log(`Sum is ${a + b}`);                  // "Sum is 15"
console.log(`Status: ${a > b ? "big" : "small"}`); // ternary inside
```

**React relevance:** JSX uses `{ }` for embedding (similar idea). You'll use template literals for dynamic class names, URLs, and inline strings.

```jsx
<div className={`card card-${theme}`}>...</div>
```

---

## 5. Default Parameters

**Definition:** Function parameters with default values when no argument (or `undefined`) is passed.

```js
// ES5 — manual fallback
function greet(name) {
  name = name || "Guest";
  console.log("Hello " + name);
}

// ES6 — clean syntax
function greet(name = "Guest") {
  console.log(`Hello ${name}`);
}
greet();         // "Hello Guest"
greet("Yash");   // "Hello Yash"
greet(undefined);// "Hello Guest" (default kicks in)
greet(null);     // "Hello null"  (null is NOT replaced — only undefined is)

// Defaults can use earlier params and any expression
function createUser(name, role = "user", id = Date.now()) {
  return { name, role, id };
}
```

**React relevance:** Default props for components are typically set this way.

```jsx
const Button = ({ label = "Click me", onClick = () => {} }) => (
  <button onClick={onClick}>{label}</button>
);
```

---

## 6. Destructuring

**Definition:** Unpack values from arrays/objects into separate variables in a single statement.

### Object Destructuring

```js
// ES5
var details = { name: "Yash", contact: 9876543210 };
var name = details.name;
var contact = details.contact;

// ES6 — basic
const { name, contact } = details;

// Rename while destructuring (alias)
const { name: person_name, contact } = details;
console.log(person_name);     // "Yash"

// Default values (used if property missing or undefined)
const { age = 25 } = details;

// Nested destructuring
const user = { profile: { city: "Jammu", pin: 180001 } };
const { profile: { city } } = user;
console.log(city);            // "Jammu"

// Combine: rename + default
const { role: userRole = "guest" } = details;
```

### Array Destructuring

```js
const numbers = [10, 20, 30, 40];

// Basic — order matters (positional)
const [a, b, c] = numbers;
console.log(a, b, c);         // 10 20 30

// Skip elements with extra commas
const [first, , third] = numbers;
console.log(first, third);    // 10 30

// Rest pattern — collect remaining items into an array
const [head, ...tail] = numbers;
console.log(head);            // 10
console.log(tail);            // [20, 30, 40]

// Defaults
const [x = 1, y = 2, z = 3] = [100];
console.log(x, y, z);         // 100 2 3

// Swap variables in one line!
let p = 1, q = 2;
[p, q] = [q, p];              // p = 2, q = 1
```

**React relevance:** Destructuring is *everywhere* in React.

```jsx
// Props destructuring
const Card = ({ title, description, onClick }) => (
  <div onClick={onClick}>{title}: {description}</div>
);

// Hook return values use array destructuring
const [count, setCount] = useState(0);
const [user, setUser] = useState(null);
```

---

## 7. Object Shorthand

**Definition:** Shorter syntax when the property key is the same as the variable name, plus shorthand for methods.

```js
const name = "Yash";
const age = 25;

// ES5
var user = { name: name, age: age };

// ES6 — property shorthand
const user = { name, age };   // same as { name: name, age: age }

// Method shorthand
// ES5
var obj = {
  greet: function() { console.log("hi"); }
};

// ES6
const obj = {
  greet() { console.log("hi"); }
};

// Computed property names — dynamic keys
const key = "dynamicKey";
const o = {
  [key]: "value",
  [`${key}_2`]: "value2"
};
console.log(o);               // { dynamicKey: "value", dynamicKey_2: "value2" }
```

**React relevance:** State updates lean heavily on object shorthand and computed keys.

```jsx
const [form, setForm] = useState({ name: "", email: "" });

const handleChange = (e) => {
  const { name, value } = e.target;
  setForm({ ...form, [name]: value });   // computed key updates the right field
};
```

---

## 8. Spread & Rest

Both use `...` but do *opposite* things based on context.

### Spread — expands / splits

```js
// ARRAYS
const old = [1, 2, 3];
const newArr = ['a', 'b', ...old, 4, 5];
console.log(newArr);          // ['a', 'b', 1, 2, 3, 4, 5]

// Copy an array (shallow copy)
const copy = [...old];

// Merge arrays
const merged = [...arr1, ...arr2];

// OBJECTS (ES2018, but you'll use it constantly)
const u = { name: "Yash", age: 25 };
const updated = { ...u, age: 26 };  // later keys override earlier
console.log(updated);         // { name: "Yash", age: 26 }

// Pass array as individual arguments
const nums = [1, 2, 3];
Math.max(...nums);            // same as Math.max(1, 2, 3) → 3
```

### Rest — collects / merges

```js
// In function parameters
function sum(...numbers) {
  console.log(numbers);       // [1, 2, 3, 4, 5]
  return numbers.reduce((total, n) => total + n, 0);
}
sum(1, 2, 3, 4, 5);           // 15

// Without rest — fixed params can't handle variable count
const sumFixed = (a, b, c, d) => a + b + c + d;
sumFixed(1, 2, 3, 4);         // 10 — but only 4 args max

// In destructuring
const [first, ...rest] = [10, 20, 30, 40];
console.log(rest);            // [20, 30, 40]

const { name, ...others } = { name: "Yash", age: 25, city: "Jammu" };
console.log(others);          // { age: 25, city: "Jammu" }
```

### How to remember
- `...` on the **right side** of `=` (or in a function call / array literal / object literal) → **spread**
- `...` on the **left side** of `=` (or in function parameters) → **rest**

**React relevance:** Critical for *immutable* state updates and prop forwarding.

```jsx
// Update state immutably (never mutate directly!)
setUser({ ...user, name: "New Name" });
setItems([...items, newItem]);

// Forward extra props down to a child
const Wrapper = ({ children, ...rest }) => (
  <div {...rest}>{children}</div>
);
```

---

## 9. Import / Export

**Definition:** The native module system in ES6. Lets you split code into reusable files. Replaces older patterns like CommonJS (`require`/`module.exports`) on the frontend.

### Named exports — multiple per file

```js
// math.js
export const PI = 3.14;
export function add(a, b) { return a + b; }
export function sub(a, b) { return a - b; }

// or grouped at the bottom
const PI = 3.14;
function add(a, b) { return a + b; }
export { PI, add };
```

```js
// app.js
import { PI, add } from './math.js';
console.log(add(2, 3));       // 5

// Rename on import
import { add as sum } from './math.js';

// Import everything as a namespace
import * as math from './math.js';
math.add(2, 3);
```

### Default export — only one per file

```js
// User.js
export default class User {
  constructor(name) { this.name = name; }
}

// or for a function/value
export default function greet() { console.log("hi"); }
```

```js
// app.js — name can be anything (no curly braces)
import User from './User.js';
import Greeting from './User.js';   // same import, just different alias
```

### Mixing both

```js
// utils.js
export const VERSION = "1.0";
export default function main() { /* ... */ }

// app.js
import main, { VERSION } from './utils.js';
```

### HTML script tag

To use ES6 modules in a plain browser HTML page:

```html
<script type="module" src="./app.js"></script>
```

Without `type="module"`, `import`/`export` won't work directly in the browser.

**React relevance:** Every React file uses imports/exports.

```jsx
// Button.jsx
import React from 'react';
export default function Button({ label }) {
  return <button>{label}</button>;
}

// App.jsx
import { useState } from 'react';
import Button from './Button';
```

---

## 10. Promises

**Definition:** An object representing the eventual completion (or failure) of an asynchronous operation. Replaces nested callbacks ("callback hell").

> Real-life analogy: a promise is a guarantee about something that *will* happen — it can either be kept (resolved) or broken (rejected).

### Three states
- **Pending** — initial state, neither fulfilled nor rejected
- **Fulfilled (resolved)** — operation completed successfully
- **Rejected** — operation failed

### Creating a promise

```js
const myPromise = new Promise((resolve, reject) => {
  const a = 5, b = 6;
  const c = a + b;
  if (c === 11) {
    resolve(`Yes! ${a}+${b}=${c}`);
  } else {
    reject(`No! ${a}+${b}!=${c}`);
  }
});

// Consuming the promise
myPromise
  .then((message) => console.log(message))   // runs on resolve
  .catch((err) => console.log(err))          // runs on reject
  .finally(() => console.log("done"));       // always runs at the end
```

### Chaining — promises return promises

```js
fetch("/api/user")
  .then(res => res.json())          // each .then returns another promise
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

### Useful Promise utilities

```js
// All in parallel — wait for ALL to resolve, fail if any rejects
Promise.all([promise1, promise2, promise3])
  .then(([r1, r2, r3]) => console.log(r1, r2, r3));

// First one to settle wins (resolve OR reject)
Promise.race([p1, p2]);

// Wait for all — get an array of {status, value/reason} regardless of success
Promise.allSettled([p1, p2]);
```

**React relevance:** API calls in `useEffect` typically return promises. `useEffect` itself can't be `async`, but you call promise-returning functions inside it.

---

## 11. async / await

**Definition:** Syntactic sugar over promises. Lets you write asynchronous code that *looks* synchronous and is much easier to read.

```js
// Promise version
function getUser() {
  return fetch("/api/user")
    .then(res => res.json())
    .then(data => console.log(data))
    .catch(err => console.error(err));
}

// async/await version — same logic, cleaner
async function getUser() {
  try {
    const res = await fetch("/api/user");
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

### Rules to remember
- `await` can only be used inside an `async` function (or top-level in modules).
- An `async` function **always returns a promise**, even if you return a plain value.
- `await` pauses execution of that function until the promise settles — without blocking the thread.

```js
async function demo() {
  return 42;                  // returns Promise<42>
}
demo().then(v => console.log(v)); // 42
```

### Sequential vs Parallel

```js
// Sequential — slow, each waits for the previous
const a = await fetchA();
const b = await fetchB();

// Parallel — much faster when fetches are independent
const [a, b] = await Promise.all([fetchA(), fetchB()]);
```

**React relevance:** Used in event handlers, custom hooks, and inside `useEffect` (via an inner async function).

```jsx
useEffect(() => {
  const loadData = async () => {
    const res = await fetch("/api/data");
    const json = await res.json();
    setData(json);
  };
  loadData();
}, []);
```

---

## 12. Array Methods

All these are **non-mutating** — they return a new array (or value) without changing the original. Perfect for React's immutable state model.

### `map()` — transform every element

```js
const arr = [1, 4, 9, 16];
const doubled = arr.map(x => x * 2);
console.log(doubled);         // [2, 8, 18, 32]

// Transform objects
const users = [{ name: "Yash" }, { name: "Dad" }];
const names = users.map(u => u.name);  // ["Yash", "Dad"]
```

### `filter()` — keep only matching elements

```js
const numbers = [1, 2, 3, 4, 5, 6];
const evens = numbers.filter(n => n % 2 === 0);
console.log(evens);           // [2, 4, 6]

const users = [{ name: "A", age: 30 }, { name: "B", age: 18 }];
const adults = users.filter(u => u.age >= 21);
```

### `reduce()` — combine into a single value

```js
const nums = [1, 2, 3, 4];
const total = nums.reduce((acc, curr) => acc + curr, 0);
// Step-by-step:
// acc = 0 (initial), curr = 1 → returns 1
// acc = 1, curr = 2 → returns 3
// acc = 3, curr = 3 → returns 6
// acc = 6, curr = 4 → returns 10
console.log(total);           // 10

// Group-by example — reduce can build any shape
const items = [{ cat: "a", v: 1 }, { cat: "b", v: 2 }, { cat: "a", v: 3 }];
const grouped = items.reduce((acc, item) => {
  acc[item.cat] = (acc[item.cat] || 0) + item.v;
  return acc;
}, {});
// { a: 4, b: 2 }
```

### `find()` — first matching element (or `undefined`)

```js
const users = [{ id: 1, name: "A" }, { id: 2, name: "B" }];
const u = users.find(u => u.id === 2);
console.log(u);               // { id: 2, name: "B" }
```

### `forEach()` — run a function per element (no return value)

```js
[1, 2, 3].forEach(n => console.log(n));
// Side effects only — does NOT return a new array. Don't use forEach
// when you actually want to build something — use map/filter/reduce instead.
```

### Quick comparison

| Method | Returns | Use when... |
|---|---|---|
| `map` | new array (same length) | transform every item |
| `filter` | new array (≤ length) | keep some items |
| `reduce` | any value | combine into one thing |
| `find` | one item OR `undefined` | get first match |
| `forEach` | `undefined` | side effects only |

**React relevance:** Rendering lists is the #1 use case. Combine `filter` + `map` constantly.

```jsx
<ul>
  {users
    .filter(u => u.active)
    .map(u => <li key={u.id}>{u.name}</li>)}
</ul>
```

---

## 13. Optional Chaining & Nullish Coalescing

> Technically ES2020 (not strictly ES6), but essential for modern React code.

### Optional chaining `?.`

Safely access nested properties — returns `undefined` if any link is `null`/`undefined`, instead of throwing a TypeError.

```js
const user = { profile: { name: "Yash" } };

// Without optional chaining — crashes on missing property
console.log(user.address.city);     // ❌ TypeError: cannot read properties of undefined

// With optional chaining — short-circuits to undefined
console.log(user?.address?.city);   // undefined (no error)

// Works on function calls and array indices too
user.greet?.();                     // calls only if greet exists
user.friends?.[0]?.name;            // safe array access
```

### Nullish coalescing `??`

Returns the right-hand side **only** if the left is `null` or `undefined`. Unlike `||`, it does NOT trigger on other falsy values like `0` or `""`.

```js
const a = null      ?? "default"; // "default"
const b = undefined ?? "default"; // "default"
const c = 0         ?? "default"; // 0   ✅ kept
const d = ""        ?? "default"; // ""  ✅ kept

// Compare with || — returns right side if left is ANY falsy value
const x = 0 || "default";         // "default"  ❌ usually NOT what you want
```

### Combining the two — the React-friendly pattern

```js
const username = user?.profile?.name ?? "Guest";
```

**React relevance:** API data is often deeply nested and may be `undefined` while loading.

```jsx
return <h1>{data?.user?.name ?? "Loading..."}</h1>;
```

---

## 14. Short-Circuit Evaluation

**Definition:** Logical operators don't always return `true`/`false` — they return one of the *operands*, stopping as soon as the result is determined.

```js
// AND (&&) — returns the FIRST falsy operand, or the LAST one if all are truthy
console.log(true && "hi");          // "hi"
console.log(false && "hi");         // false
console.log(1 && 2 && 3);           // 3

// OR (||) — returns the FIRST truthy operand, or the LAST one if all are falsy
console.log(null || "default");     // "default"
console.log("a" || "b");            // "a"
console.log(0 || "" || "x");        // "x"
```

### Common patterns

```js
// Default value (legacy — use ?? if you want strict null/undefined check only)
const name = inputName || "Guest";

// Conditional execution
isLoggedIn && doSomething();        // call only if isLoggedIn is truthy
```

**React relevance:** This is *the* idiomatic pattern for conditional rendering in JSX (since `if` statements don't work inside JSX expressions).

```jsx
{isLoggedIn && <Dashboard />}
{errors.length > 0 && <ErrorBox errors={errors} />}
```

⚠️ **Common gotcha:** `{count && <List />}` will render `0` on screen if `count` is 0 (because `0 && X` returns `0`, and React renders `0` as text). Always coerce to boolean explicitly:

```jsx
{count > 0 && <List />}             // ✅ safer
{!!items.length && <List />}        // ✅ also fine
```

---

## 15. Ternary Operator

**Definition:** A one-line conditional expression. `condition ? valueIfTrue : valueIfFalse`

```js
const age = 20;
const status = age >= 18 ? "adult" : "minor";

// Nested ternary — works but gets messy fast, avoid going deeper than this
const grade = score > 90 ? "A" : score > 75 ? "B" : "C";
```

**React relevance:** The standard way to do inline if/else in JSX, since `if` itself can't be used as an expression.

```jsx
{isLoading ? <Spinner /> : <Content data={data} />}

{user
  ? <p>Hi {user.name}</p>
  : <button>Login</button>}
```

When you only need the "if" branch (no else), use `&&` instead — it's cleaner.

---

## 16. Classes

**Definition:** Syntactic sugar over JavaScript's prototype-based inheritance. A template for creating objects that bundles data with methods that operate on that data.

### Basic syntax

```js
class Person {
  // constructor — runs automatically when `new Person()` is called
  constructor(name, age) {
    this.name = name;             // properties live on `this`
    this.age = age;
  }

  // methods — no `function` keyword needed inside a class body
  greet() {
    console.log(`Hi, I'm ${this.name}`);
  }

  // getters and setters
  get info() {
    return `${this.name}, ${this.age}`;
  }
  set info(value) {
    [this.name, this.age] = value.split(",");
  }

  // static methods — called on the class itself, not on instances
  static create(name) {
    return new Person(name, 0);
  }
}

const yash = new Person("Yash", 25);
yash.greet();                       // "Hi, I'm Yash"
console.log(yash.info);             // "Yash, 25"

const baby = Person.create("Baby"); // static call — no `new` needed for create()
```

### Inheritance with `extends` and `super`

```js
class Student extends Person {
  constructor(name, age, course) {
    super(name, age);             // call parent constructor FIRST
    this.course = course;
  }

  greet() {
    super.greet();                // call parent's version of the method
    console.log(`I study ${this.course}`);
  }
}

const s = new Student("Yash", 25, "M.Tech CSE");
s.greet();
// "Hi, I'm Yash"
// "I study M.Tech CSE"
```

### Modern class fields

```js
class Counter {
  count = 0;                      // public class field
  #secret = 42;                   // private field (# prefix)

  increment = () => {             // arrow method — auto-bound `this`
    this.count++;
  };
}
```

**React relevance:** Class components were the default before hooks landed in React 16.8 (2019).

```jsx
class Welcome extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

Modern React uses **functional components + hooks** — but understanding classes still helps when reading legacy codebases or older Stack Overflow answers.

---

## Quick React Translation Table

| ES6 feature | Where you'll see it in React |
|---|---|
| `const`/`let` | Component definitions, hook calls |
| Arrow functions | Components, event handlers, callbacks |
| Template literals | Dynamic class names, URLs |
| Destructuring | Props, hook return values |
| Object shorthand | State updates |
| Spread `...` | Immutable state, prop forwarding |
| Rest `...` | Catching extra props |
| `import`/`export` | Every single file |
| Promises / async-await | API calls in `useEffect`, event handlers |
| `map` | Rendering lists |
| `filter` | Filtering visible items before render |
| `reduce` | Computing totals, grouping data |
| `?.`, `??` | Safe access to API data while loading |
| `&&` / ternary | Conditional rendering in JSX |
| Classes | Legacy class components |

---

## What's next?

Once these are solid, jumping into React — JSX, components, hooks (`useState`, `useEffect`) — will feel natural. The syntax above *is* most of React's surface complexity. From here:

1. **JSX** — HTML-in-JS templating
2. **Components & Props** — function components
3. **`useState` & `useEffect`** — the two most common hooks
4. **Event handling** — `onClick`, `onChange`, etc.
5. **Lists & Keys** — using `map` with a `key` prop
6. **Conditional rendering** — `&&` and ternary in JSX
7. **Forms** — controlled components

Good luck, Yash 🚀
