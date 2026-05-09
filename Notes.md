# JavaScript ES6+ Notes (Hinglish) — React ke liye Complete Prep

> Bhai, yeh notes specifically tujhe React seekhne se pehle JavaScript ke modern features pakke karne ke liye banaye gaye hain. Har topic mein **definition + kyun zaroori hai + multiple examples + common galtiyaan + React mein kahan use hoga** — sab cover kiya hai.

## Table of Contents

1. [`let`, `const`, `var` — Variable Declaration](#1-let-const-var)
2. [Arrow Functions](#2-arrow-functions)
3. [`this` Keyword — Deep Dive](#3-this-keyword)
4. [Template Literals](#4-template-literals)
5. [Default Parameters](#5-default-parameters)
6. [Destructuring (Array + Object)](#6-destructuring)
7. [Object Shorthand](#7-object-shorthand)
8. [Spread & Rest Operators (`...`)](#8-spread--rest)
9. [Import / Export — Modules](#9-import--export)
10. [Promises](#10-promises)
11. [`async` / `await`](#11-async--await)
12. [Array Methods (map, filter, reduce, find, forEach)](#12-array-methods)
13. [Optional Chaining (`?.`) & Nullish Coalescing (`??`)](#13-optional-chaining--nullish-coalescing)
14. [Short-Circuit Evaluation (`&&`, `||`)](#14-short-circuit-evaluation)
15. [Ternary Operator](#15-ternary-operator)
16. [Classes](#16-classes)
17. [Quick React Translation Table](#quick-react-translation-table)

---

## 1. `let`, `const`, `var`

### Definition

Ye teeno keywords variable declare karne ke liye use hote hain. ES5 mein sirf `var` tha. ES6 ne `let` aur `const` introduce kiye kyunki `var` mein kuch design problems thi (scope leak, hoisting confusion, accidental reassignment, etc.).

### Kyun zaroori hai (problem with `var`)

`var` ka scope **function-level** hai, **block-level** nahi. Iska matlab agar tu `if`, `for`, ya `{ }` ke andar `var` declare karega, woh bahar bhi accessible hoga. Yahi `let` aur `const` fix karte hain.

### Comparison Table

| Keyword | Scope | Reassign? | Redeclare? | Hoisting | Use kab kare? |
|---|---|---|---|---|---|
| `var` | Function/Global | ✅ Yes | ✅ Yes | Hoisted, `undefined` se initialize | Use mat kar (legacy) |
| `let` | Block `{ }` | ✅ Yes | ❌ No (same scope mein) | Hoisted but TDZ | Jab reassignment chahiye |
| `const` | Block `{ }` | ❌ No | ❌ No | Hoisted but TDZ | **Default — yahi use kar** |

> **TDZ (Temporal Dead Zone)** = Variable declare hone se pehle agar tu use karega, error milega. `var` mein `undefined` milta tha, jo bug chhupa deta tha.

### Examples

```js
// var — hoisted hota hai, isliye declaration se pehle access karne par error nahi aata
console.log(name);            // undefined (kyunki hoisting hua, value abhi nahi mili)
var name = "Yash Agarwal";
console.log(name);            // "Yash Agarwal"

// let — TDZ ki wajah se ReferenceError aayega
console.log(course);          // ❌ ReferenceError: Cannot access 'course' before initialization
let course = "M.Tech";
console.log(course);

// const — declare karte hi value deni padti hai, nahi to error
const PI;                     // ❌ SyntaxError: Missing initializer
const PI = 3.14;              // ✅
PI = 3.14159;                 // ❌ TypeError: Assignment to constant variable
```

### Const ka Twist (yeh point bohot important hai)

`const` ka matlab hai **binding constant**, value nahi. Iska matlab agar tu `const` se array ya object banaya hai, uske *contents* change kar sakta hai, but reassign nahi kar sakta.

```js
const arr = [1, 2, 3];
arr.push(4);                  // ✅ chal jayega — arr ab [1,2,3,4]
arr[0] = 100;                 // ✅ yeh bhi chalega
arr = [5, 6];                 // ❌ TypeError — reassignment ban hai

const user = { name: "Yash" };
user.name = "Dad";            // ✅ property change kar sakte hain
user.age = 25;                // ✅ new property add bhi
user = { name: "New" };       // ❌ reassign nahi kar sakte
```

> **Yaad rakh:** `const` matlab "tu apni jagah se hil nahi sakta", but "tu jo cheez pakde hai uski insides change ho sakti hain".

### Block Scope ka Drama

```js
if (true) {
  var a = 10;                 // function-scoped — bahar leak ho jayega
  let b = 20;                 // block-scoped — sirf is { } ke andar
  const c = 30;               // block-scoped — same as let
}
console.log(a);               // 10 ✅
console.log(b);               // ❌ ReferenceError
console.log(c);               // ❌ ReferenceError
```

### Classic `var` Bug — Loop Mein

```js
// var ke saath — sab loops me same i share karte hain
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 3, 3, 3 (kyunki loop khatam hone tak i = 3 ho gaya)

// let ke saath — har iteration ka apna i
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 0, 1, 2 ✅
```

### React Relevance

React mein **default `const` use kar**. Components, hooks, props — sab `const`. `let` sirf jab tujhe sach me reassign karna ho. `var` ko bhool ja.

```jsx
const App = () => {
  const [count, setCount] = useState(0);  // hooks always const
  const name = "Yash";                     // values always const
  return <div>{name}: {count}</div>;
};
```

---

## 2. Arrow Functions

### Definition

Arrow function ek **chhota syntax** hai function likhne ka, jo ES6 me aaya. Yeh sirf syntax sugar nahi hai — iska behavior bhi alag hai (especially `this` ke saath, jo next topic mein dekhenge).

### Kyun zaroori hai

1. **Concise** — ek line mein function likh sakte ho
2. **Lexical `this`** — apna khud ka `this` nahi hota (yeh feature React mein bohot kaam aata hai)
3. **Implicit return** — single expression ke liye `return` keyword skip kar sakte ho

### Syntax Progression (asaani se mushkil tak)

```js
// ES5 — purana style
var greet = function(name, age) {
  return "Hello " + name + " " + age;
};

// ES6 — full arrow form (sab parens, sab braces)
let greet = (name, age) => {
  return "Hello " + name + " " + age;
};

// Single parameter — parens optional hain
let greet = name => {
  return "Hello " + name;
};

// Implicit return — agar body sirf ek expression hai, braces aur return hata sakte ho
let sqr = num => num * num;
console.log(sqr(5));          // 25

// Zero parameters — empty parens chahiye hi
let sayHi = () => "Hi!";

// Multiple statements — braces aur explicit return zaroori
let process = (a, b) => {
  const sum = a + b;
  const product = a * b;
  return { sum, product };
};
```

### Object Return Karne Ka Trap

```js
// ❌ Yeh kaam nahi karega — { } ko function body samjhega
let makeUser = (name, age) => { name, age };
makeUser("Yash", 25);         // undefined milega

// ✅ Object literal ko parens mein wrap kar
let makeUser = (name, age) => ({ name, age });
makeUser("Yash", 25);         // { name: "Yash", age: 25 }
```

> **Reason:** `{` arrow function ke baad function body samjha jata hai. Agar tujhe object return karna hai, parens lagana padta hai taaki JS samajh jaye "yeh expression hai, body nahi".

### Examples

```js
// Single line, single expression
const double = n => n * 2;
const isEven = n => n % 2 === 0;

// Inline use — array methods ke saath bohot common
[1, 2, 3].map(n => n * 10);   // [10, 20, 30]

// Nested arrow (currying pattern)
const add = a => b => a + b;
add(2)(3);                    // 5
```

### React Relevance

Arrow functions React ka *roti-paani* hain.

```jsx
// Functional component
const Welcome = () => <h1>Hello!</h1>;

// Event handler
<button onClick={() => setCount(count + 1)}>+1</button>

// Array rendering
{users.map(user => <li key={user.id}>{user.name}</li>)}

// Custom hook
const useCounter = (initial) => {
  const [count, setCount] = useState(initial);
  return { count, increment: () => setCount(c => c + 1) };
};
```

---

## 3. `this` Keyword

### Definition

`this` ka matlab hai — "**function ko kaun call kar raha hai?**" `this` ki value depend karti hai *kaise* function call hua, *kahaan* defined hai uspe nahi.

> **Bhai, yeh JavaScript ka sabse confusing concept hai.** Ek baar samajh gaya, sab clear ho jayega.

### Regular Function vs Arrow Function — THE Big Difference

```js
const obj = {
  name: "Yash",

  // Regular function — apna khud ka `this` hota hai
  // Jab obj.regular() call karte hain, this = obj
  regular: function() {
    console.log(this.name);   // "Yash" ✅
  },

  // Arrow function — apna khud ka `this` NAHI hota
  // Yeh outer scope se uthata hai (lexical this)
  arrow: () => {
    console.log(this.name);   // undefined ❌
  }
};

obj.regular();                // "Yash"
obj.arrow();                  // undefined
```

### Regular Function ke 4 Binding Rules

`this` regular function mein kaisi set hoti hai, yeh 4 rules decide karte hain:

#### Rule 1 — Default Binding (akele call karna)

```js
function show() {
  console.log(this);
}
show();
// Browser non-strict: window
// Strict mode / ES module: undefined
```

#### Rule 2 — Implicit Binding (object ke through call)

```js
const user = {
  name: "Yash",
  greet: function() {
    console.log(this.name);   // `this` = user
  }
};
user.greet();                 // "Yash"

// ⚠️ Trap — agar function reference nikal liya
const greetFn = user.greet;
greetFn();                    // undefined (default binding lag gaya)
```

#### Rule 3 — Explicit Binding (call/apply/bind)

```js
function intro() {
  console.log(this.name);
}

const person = { name: "Dad" };

intro.call(person);           // "Dad" — pehla arg = this
intro.apply(person);          // "Dad" — same as call (args array mein)
const bound = intro.bind(person);
bound();                      // "Dad" — naya function jiska this lock hai
```

> `call` aur `apply` mein farak sirf args pass karne ka style hai:
> - `fn.call(thisArg, arg1, arg2)`
> - `fn.apply(thisArg, [arg1, arg2])`
> - `bind` naya function return karta hai, immediately call nahi karta.

#### Rule 4 — `new` Binding (constructor)

```js
function Person(name) {
  this.name = name;           // `this` = naya empty object
}
const p = new Person("Yash");
console.log(p.name);          // "Yash"
```

### Arrow Function — Lexical `this`

Arrow function `this` ko **enclosing scope se inherit karta hai**. Iska binding **fixed** hota hai — `call`, `apply`, `bind` se bhi change nahi kar sakte.

```js
const counter = {
  count: 0,
  start: function() {           // regular function — this = counter
    setInterval(() => {         // arrow — this = enclosing (counter) ✅
      this.count++;
      console.log(this.count);
    }, 1000);
  }
};
counter.start();                // 1, 2, 3, 4...
```

Agar tu yahan `setInterval` ke andar regular function use karta:

```js
setInterval(function() {
  this.count++;                 // ❌ this = window/undefined
}, 1000);
```

…toh `this.count` undefined hota aur `NaN` show hota.

### Common Galti — Method ke andar callback

```js
const user = {
  name: "Yash",
  hobbies: ["coding", "gym"],

  printHobbies: function() {
    // ❌ Regular function as callback — this = undefined
    this.hobbies.forEach(function(h) {
      console.log(this.name + " loves " + h);  // this.name undefined
    });

    // ✅ Arrow function — this = user (lexical)
    this.hobbies.forEach(h => {
      console.log(this.name + " loves " + h);  // works!
    });
  }
};
```

### React Relevance

Yahi reason hai jo class components mein `this.handleClick = this.handleClick.bind(this)` likhna padta tha — kyunki event handler call hote waqt `this` lose ho jata tha. Functional components + hooks mein `this` ka jhanjhat hi nahi hai.

```jsx
// Class component (purana way) — this ka drama
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    this.increment = this.increment.bind(this);  // 😩 boilerplate
  }
  increment() {
    this.setState({ count: this.state.count + 1 });
  }
  render() {
    return <button onClick={this.increment}>+1</button>;
  }
}

// Functional component (modern) — koi this nahi
const Counter = () => {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>+1</button>;
};
```

---

## 4. Template Literals

### Definition

Backtick `` ` `` se banaye gaye strings, jisme tu **expressions embed kar sakta hai** (`${ }` ke andar) aur **multi-line strings** seedha likh sakta hai.

### Kyun zaroori hai

ES5 mein string concatenation `+` se hoti thi — long strings mein dard ho jata tha. Multi-line strings ke liye `\n` lagana padta tha. Template literals ne sab clean kar diya.

### Examples

```js
// ES5 — concatenation
var name = "Yash";
var age = 25;
console.log("Welcome " + name + "! Tumhari age " + age + " hai.");

// ES6 — clean template literal
const name = "Yash";
const age = 25;
console.log(`Welcome ${name}! Tumhari age ${age} hai.`);
```

### Multi-line Strings

```js
// ES5 — terrible
var html = "<div>\n" +
           "  <h1>" + name + "</h1>\n" +
           "  <p>Hello</p>\n" +
           "</div>";

// ES6 — natural
const html = `
  <div>
    <h1>${name}</h1>
    <p>Hello</p>
  </div>
`;
```

### Expressions Inside `${ }`

`${ }` mein **kuch bhi valid JavaScript expression** likh sakta hai — math, function calls, ternary, sab.

```js
const a = 5, b = 10;
console.log(`Sum is ${a + b}`);                            // "Sum is 15"
console.log(`${a} is ${a > b ? "bada" : "chhota"} than ${b}`);
console.log(`Square of ${a} is ${Math.pow(a, 2)}`);
console.log(`Hello ${name.toUpperCase()}`);                // function call
```

### Tagged Templates (Advanced — bonus)

Tu function ka name template literal ke aage laga sakta hai. Function ko strings aur values alag-alag milte hain.

```js
function highlight(strings, ...values) {
  return strings.reduce((acc, str, i) => {
    return acc + str + (values[i] ? `<b>${values[i]}</b>` : '');
  }, '');
}

const name = "Yash";
const age = 25;
console.log(highlight`My name is ${name} and age is ${age}.`);
// "My name is <b>Yash</b> and age is <b>25</b>."
```

> Yeh advanced hai — React mein styled-components library exactly yahi pattern use karti hai.

### React Relevance

```jsx
// Dynamic className
<div className={`card card-${theme} ${isActive ? 'active' : ''}`}>...</div>

// Dynamic URL
const userUrl = `/api/users/${userId}`;

// Inline style strings
const styleString = `transform: translateX(${x}px)`;
```

---

## 5. Default Parameters

### Definition

Function ke parameters ko **default value** dena, jo tab use hoti hai jab caller ne argument nahi diya (ya `undefined` diya).

### Kyun zaroori hai

ES5 mein har function ke top par manual fallback likhna padta tha — boilerplate code badh jata tha. ES6 mein syntax-level support aa gaya.

### Examples

```js
// ES5 — manual fallback
function greet(name) {
  name = name || "Guest";       // ⚠️ ye kuch falsy values pe galat behave karega
  console.log("Hello " + name);
}

// ES6 — clean syntax
function greet(name = "Guest") {
  console.log(`Hello ${name}`);
}

greet();                        // "Hello Guest"
greet("Yash");                  // "Hello Yash"
greet(undefined);               // "Hello Guest" (undefined = default trigger)
greet(null);                    // "Hello null"  (null counts as a real value!)
greet("");                      // "Hello "      (empty string = real value)
greet(0);                       // "Hello 0"     (0 = real value)
```

> **Important distinction:** Default parameter sirf `undefined` ke liye trigger hota hai. `null`, `0`, `""`, `false` — yeh sab "real values" hain, default nahi lagega.

### Defaults Use Earlier Params Too

```js
function createUser(name, role = "user", id = Date.now()) {
  return { name, role, id };
}

createUser("Yash");
// { name: "Yash", role: "user", id: 1715337600000 }

// Earlier params ko reference kar sakta hai
function rectangle(width, height = width) {
  return width * height;
}
rectangle(5);                   // 25 (square)
rectangle(5, 10);               // 50
```

### With Destructuring

```js
// Default param + destructuring + nested defaults — full power
function fetchData({ url, method = "GET", headers = {} } = {}) {
  console.log(url, method, headers);
}

fetchData({ url: "/api/user" });   // /api/user GET {}
fetchData();                        // undefined GET {} (poora arg missing bhi handle)
```

### React Relevance

React mein default props lagane ka modern way yahi hai.

```jsx
const Button = ({ label = "Click me", onClick = () => {}, type = "button" }) => (
  <button type={type} onClick={onClick}>{label}</button>
);

// Sab defaults use ho jayenge
<Button />

// Sirf label override
<Button label="Submit" />
```

---

## 6. Destructuring

### Definition

Array ya object ke values ko **ek hi line mein** alag-alag variables mein nikalna. Code clean aur readable hota hai.

### Kyun zaroori hai

ES5 mein har property ke liye alag line likhni padti thi. React mein props handle karte waqt yeh feature blessing hai.

---

### Object Destructuring

#### Basic

```js
// ES5
var details = { name: "Yash", contact: 9876543210, city: "Jammu" };
var name = details.name;
var contact = details.contact;
var city = details.city;

// ES6 — ek line, kaam khatam
const { name, contact, city } = details;
console.log(name, contact, city);
```

#### Rename (Alias)

Variable ka naam alag rakhna hai? Colon `:` use kar.

```js
const details = { name: "Yash", contact: 9876543210 };
const { name: person_name, contact: phone } = details;
console.log(person_name);       // "Yash"
console.log(phone);             // 9876543210
console.log(name);              // ❌ ReferenceError (rename ho gaya)
```

#### Default Values

Property nahi mili to fallback.

```js
const details = { name: "Yash" };
const { name, age = 25, city = "Jammu" } = details;
console.log(name, age, city);   // "Yash" 25 "Jammu"
```

#### Rename + Default Combo

```js
const { role: userRole = "guest" } = details;
// "agar details.role hai to userRole me dal, warna 'guest'"
```

#### Nested Destructuring

```js
const user = {
  name: "Yash",
  profile: {
    city: "Jammu",
    pin: 180001,
    education: { degree: "M.Tech", college: "IIT Jammu" }
  }
};

const { profile: { city, education: { degree } } } = user;
console.log(city, degree);      // "Jammu" "M.Tech"
```

> **Note:** Nested destructuring mein sirf inner values mil rahi hain, `profile` aur `education` xvariables nahi bante. Agar dono chahiye, dobara likhna padega.

#### Rest Pattern (object mein)

```js
const { name, ...rest } = { name: "Yash", age: 25, city: "Jammu" };
console.log(name);              // "Yash"
console.log(rest);              // { age: 25, city: "Jammu" }
```

---

### Array Destructuring

#### Basic — Position Matter Karti Hai

```js
const numbers = [10, 20, 30, 40];
const [a, b, c] = numbers;
console.log(a, b, c);           // 10 20 30
```

#### Skip Karna

Comma chhod ke skip kar sakta hai.

```js
const [first, , third] = [10, 20, 30];
console.log(first, third);      // 10 30
```

#### Rest Pattern (array mein)

```js
const [head, ...tail] = [10, 20, 30, 40];
console.log(head);              // 10
console.log(tail);              // [20, 30, 40]
```

#### Default Values

```js
const [x = 1, y = 2, z = 3] = [100];
console.log(x, y, z);           // 100 2 3
```

#### Swap Variables — Ek Line Mein!

```js
let p = 1, q = 2;
[p, q] = [q, p];
console.log(p, q);              // 2 1
```

> **Pehle ke time:** swap ke liye temporary variable lagana padta tha. Ab destructuring se ek line!

### Mixed Destructuring (Object + Array)

```js
const data = {
  name: "Yash",
  scores: [90, 85, 78]
};

const { name, scores: [first, second] } = data;
console.log(name, first, second); // "Yash" 90 85
```

### React Relevance

React mein destructuring **bina iske kaam hi nahi chalta**.

```jsx
// 1. Props destructuring (component definition mein)
const Card = ({ title, description, onClick }) => (
  <div onClick={onClick}>
    <h2>{title}</h2>
    <p>{description}</p>
  </div>
);

// 2. Hook return values — array destructuring
const [count, setCount] = useState(0);
const [user, setUser] = useState(null);
const [isLoading, setIsLoading] = useState(true);

// 3. Event objects
const handleChange = ({ target: { name, value } }) => {
  setForm({ ...form, [name]: value });
};
```

---

## 7. Object Shorthand

### Definition

Jab variable ka naam aur object key ka naam **same** ho, tu repeat nahi karna. Aur method definition ka shorter syntax bhi hai.

### Kyun zaroori hai

Code DRY (Don't Repeat Yourself) banta hai. React state updates aur object literals mein har jagah use hota hai.

### Property Shorthand

```js
const name = "Yash";
const age = 25;
const city = "Jammu";

// ES5 — repetitive
var user = { name: name, age: age, city: city };

// ES6 — shorthand
const user = { name, age, city };
// Same as: { name: name, age: age, city: city }

console.log(user); // { name: "Yash", age: 25, city: "Jammu" }
```

### Method Shorthand

```js
// ES5
var obj = {
  greet: function() {
    console.log("hi");
  },
  add: function(a, b) {
    return a + b;
  }
};

// ES6
const obj = {
  greet() {
    console.log("hi");
  },
  add(a, b) {
    return a + b;
  }
};

obj.greet();                    // "hi"
obj.add(2, 3);                  // 5
```

### Computed Property Names — Dynamic Keys

Square brackets `[ ]` use kar key ko **runtime pe** decide karne ke liye.

```js
const key = "dynamicKey";
const obj = {
  [key]: "value1",
  [`${key}_2`]: "value2",
  [key.toUpperCase()]: "value3"
};
console.log(obj);
// { dynamicKey: "value1", dynamicKey_2: "value2", DYNAMICKEY: "value3" }
```

### Combine — Real-World Example

```js
function createUser(name, age, role) {
  return {
    name,                       // shorthand
    age,                        // shorthand
    role,                       // shorthand
    createdAt: Date.now(),      // normal
    [`is${role}`]: true,        // computed key
    greet() {                   // method shorthand
      return `Hi, I'm ${this.name}`;
    }
  };
}

const u = createUser("Yash", 25, "Admin");
// {
//   name: "Yash",
//   age: 25,
//   role: "Admin",
//   createdAt: 1715337600000,
//   isAdmin: true,
//   greet: function...
// }
```

### React Relevance

State updates mein **constantly** use hoga.

```jsx
// Computed key — generic form handling
const [form, setForm] = useState({ name: "", email: "", phone: "" });

const handleChange = (e) => {
  const { name, value } = e.target;
  setForm({ ...form, [name]: value });   // computed key + spread + shorthand
};

// Object shorthand in API calls
const saveUser = (name, age) => {
  fetch('/api/users', {
    method: 'POST',
    body: JSON.stringify({ name, age })  // shorthand
  });
};
```

---

## 8. Spread & Rest

### Definition

Dono `...` syntax use karte hain, but **opposite kaam** karte hain. Context se decide hota hai kaun sa hai.

- **Spread** — kisi cheez ko *kholna* (expand)
- **Rest** — kayi cheezein *ek mein band* karna (collect)

### Yaad Rakhne Ka Trick

| Position | Type | Kaam |
|---|---|---|
| `=` ke **right side**, function call mein, array `[ ]` ya object `{ }` literal mein | **Spread** | Expand karta hai |
| `=` ke **left side**, function parameters mein | **Rest** | Collect karta hai |

---

### Spread — Expand / Split

#### Arrays

```js
const old = [1, 2, 3];

// Add karte waqt expand
const newArr = ['a', 'b', ...old, 4, 5];
console.log(newArr);            // ['a', 'b', 1, 2, 3, 4, 5]

// Shallow copy banane ke liye
const copy = [...old];
copy.push(99);
console.log(old);               // [1, 2, 3] — original safe
console.log(copy);              // [1, 2, 3, 99]

// Multiple arrays merge
const a = [1, 2];
const b = [3, 4];
const c = [5, 6];
const merged = [...a, ...b, ...c];     // [1, 2, 3, 4, 5, 6]

// Function ko array as separate args pass karna
const nums = [1, 5, 3, 9, 2];
Math.max(...nums);              // 9 — same as Math.max(1,5,3,9,2)
```

#### Objects (ES2018, but tu use karega React mein)

```js
const user = { name: "Yash", age: 25 };

// Copy
const userCopy = { ...user };

// Add karte waqt
const userWithCity = { ...user, city: "Jammu" };

// Override karna — baad wala win karta hai
const updated = { ...user, age: 26 };
console.log(updated);           // { name: "Yash", age: 26 }

// Multiple objects merge
const defaults = { theme: "light", lang: "en" };
const userPrefs = { theme: "dark" };
const final = { ...defaults, ...userPrefs };
console.log(final);             // { theme: "dark", lang: "en" }
```

> **Order matters!** Bad wala spread pehle wale ko override kar deta hai.

#### Strings (bonus)

```js
const word = "yash";
const chars = [...word];        // ['y', 'a', 's', 'h']
```

---

### Rest — Collect / Merge

#### Function Parameters

Variable number of arguments handle karne ke liye.

```js
// Without rest — only fixed args
const sumFixed = (a, b, c, d) => a + b + c + d;
sumFixed(1, 2, 3, 4);           // 10
sumFixed(1, 2, 3, 4, 5, 6);     // 10 (extra args ignore!)

// With rest — kitne bhi args
const sum = (...numbers) => {
  console.log(numbers);         // [1, 2, 3, 4, 5, 6]
  return numbers.reduce((total, n) => total + n, 0);
};
sum(1, 2, 3);                   // 6
sum(1, 2, 3, 4, 5, 6);          // 21

// Mixed — fixed first, rest baad mein
const greet = (greeting, ...names) => {
  return names.map(n => `${greeting}, ${n}`).join('\n');
};
greet("Hi", "Yash", "Dad", "Bhai");
// "Hi, Yash\nHi, Dad\nHi, Bhai"
```

> **Rule:** Rest parameter **last** mein hi aana chahiye. `(...args, last)` — yeh syntax error hai.

#### Destructuring Mein Rest

```js
// Array
const [first, ...others] = [10, 20, 30, 40];
console.log(first);             // 10
console.log(others);            // [20, 30, 40]

// Object
const { name, ...rest } = { name: "Yash", age: 25, city: "Jammu", phone: "xxx" };
console.log(name);              // "Yash"
console.log(rest);              // { age: 25, city: "Jammu", phone: "xxx" }
```

### Spread vs Rest — Side-by-Side

```js
// SPREAD (expanding)
const a = [1, 2, 3];
const b = [...a, 4];            // [1, 2, 3, 4]

// REST (collecting)
const [first, ...rest] = [1, 2, 3, 4];   // first=1, rest=[2,3,4]

// Same syntax, different position!
```

### React Relevance

Yeh **most important ES6 feature** hai React ke liye, kyunki React state ko **immutably** update karna padta hai (directly mutate nahi kar sakte).

```jsx
// ❌ NEVER do this in React — direct mutation
state.name = "New";
state.items.push(newItem);

// ✅ Spread se naya object/array banao
setUser({ ...user, name: "New" });
setItems([...items, newItem]);

// Update specific item in array
setItems(items.map(item =>
  item.id === targetId ? { ...item, done: true } : item
));

// Remove item
setItems(items.filter(item => item.id !== targetId));

// Forward extra props to child component
const Wrapper = ({ children, className, ...rest }) => (
  <div className={`wrapper ${className}`} {...rest}>{children}</div>
);
// Now <Wrapper id="x" data-test="y"> sab id, data-test pass ho jayenge
```

---

## 9. Import / Export

### Definition

ES6 ka **native module system**. Code ko alag-alag files mein todna aur connect karna. Pehle `<script>` tags ya CommonJS (`require`) se kaam chalta tha — ab clean syntax hai.

### Kyun zaroori hai

- **Reusability** — ek file mein function likho, anywhere import karo
- **Encapsulation** — sirf jo `export` kiya hai woh hi bahar dikhta hai
- **Dependencies clear** — har file mein top par dikh jata hai kya import ho raha hai

---

### Named Exports — Multiple Per File

```js
// math.js — har export ka apna naam
export const PI = 3.14;

export function add(a, b) {
  return a + b;
}

export function sub(a, b) {
  return a - b;
}

// OR end mein grouped export
const MULTIPLIER = 2;
function multiply(a, b) { return a * b; }
function divide(a, b) { return a / b; }
export { MULTIPLIER, multiply, divide };
```

```js
// app.js
import { PI, add } from './math.js';
console.log(PI, add(2, 3));     // 3.14 5

// Rename on import (alias)
import { add as sum, sub as minus } from './math.js';
sum(2, 3);                      // 5

// Sab kuch ek namespace ke andar
import * as math from './math.js';
math.add(2, 3);                 // 5
math.PI;                        // 3.14
```

### Default Exports — Sirf Ek Per File

```js
// User.js
export default class User {
  constructor(name) { this.name = name; }
}

// OR
export default function greet() { console.log("hi"); }

// OR (variable)
const config = { apiUrl: "/api", timeout: 5000 };
export default config;
```

```js
// app.js — naam tu kuch bhi rakh sakta hai (no curly braces)
import User from './User.js';
import Person from './User.js';      // same export, different name
```

> **Default vs Named — kab kya use kare?**
> - File ka **main thing** ek hi hai? → `export default`
> - Multiple utilities? → Named exports

### Mixing Both

```js
// utils.js
export const VERSION = "1.0";
export const MAX_RETRIES = 3;
export default function main() { console.log("main fn"); }
```

```js
// app.js — default + named in ek line
import main, { VERSION, MAX_RETRIES } from './utils.js';
```

### Re-exports (Bonus — barrel files)

```js
// index.js — sab cheezein ek jagah re-export
export { Button } from './Button';
export { Card } from './Card';
export { Modal } from './Modal';
```

```js
// app.js — ek hi import path
import { Button, Card, Modal } from './components';
```

### HTML mein Module Use Karna

```html
<!-- type="module" zaroori hai, warna import/export work nahi karega -->
<script type="module" src="./app.js"></script>
```

> **Important:** `type="module"` ke saath:
> - File automatically `defer` hoti hai (DOM load ke baad chalti hai)
> - `import`/`export` syntax kaam karta hai
> - `this` top-level pe `undefined` hota hai (window nahi)
> - Strict mode automatically on

### React Relevance

**Har React file mein** import/export hota hai.

```jsx
// Button.jsx — default export (component ka main thing)
import React from 'react';

const Button = ({ label, onClick }) => (
  <button onClick={onClick}>{label}</button>
);

export default Button;

// constants.js — named exports (multiple values)
export const API_URL = "https://api.example.com";
export const MAX_FILE_SIZE = 5 * 1024 * 1024;

// App.jsx — sab milake use
import React, { useState, useEffect } from 'react';
import Button from './Button';
import { API_URL } from './constants';

function App() {
  const [count, setCount] = useState(0);
  return <Button label="Click" onClick={() => setCount(count + 1)} />;
}

export default App;
```

---

## 10. Promises

### Definition

Promise ek **object** hai jo bata raha hai ki "abhi yeh kaam pending hai, future mein result milega — ya success ya failure". Real-life promise jaisa hi:

> "Bhai main tujhe kal paise dunga" — yeh promise hai. Kal aane par, ya tu paise milenge (resolved) ya bahana milega (rejected).

### Kyun zaroori hai

Pehle async kaam (jaise API calls, file reading) callbacks se hota tha. Nested callbacks ki situation **callback hell** kehlati thi:

```js
// Callback hell — ye bohot ugly hai
getUser(userId, function(user) {
  getOrders(user.id, function(orders) {
    getOrderDetails(orders[0].id, function(details) {
      // ... aur deep
    });
  });
});
```

Promises ne yeh flat aur readable kar diya.

### Three States

| State | Matlab |
|---|---|
| **Pending** | Initial state, abhi result aaya nahi |
| **Fulfilled (Resolved)** | Kaam successfully complete |
| **Rejected** | Kaam fail ho gaya |

Ek baar resolve ya reject ho gaya, state lock — phir change nahi hoti.

### Promise Banana

```js
const myPromise = new Promise((resolve, reject) => {
  // async kaam yahan
  const a = 5, b = 6;
  const c = a + b;

  if (c === 11) {
    resolve(`Yes! ${a}+${b}=${c}`);    // success path
  } else {
    reject(`No! ${a}+${b}!=${c}`);     // failure path
  }
});
```

### Promise Consume Karna — `.then` / `.catch` / `.finally`

```js
myPromise
  .then((message) => {
    console.log("Success:", message);  // resolve hua to yeh chalega
  })
  .catch((err) => {
    console.log("Error:", err);        // reject hua to yeh
  })
  .finally(() => {
    console.log("Done — kuch bhi ho, yeh chalega");
  });
```

### Chaining — har `.then` ek naya promise return karta hai

```js
fetch("/api/user")
  .then(res => res.json())            // res.json() bhi promise return karta hai
  .then(data => {
    console.log(data);
    return data.id;                    // next .then ko milega
  })
  .then(id => fetch(`/api/orders/${id}`))
  .then(res => res.json())
  .then(orders => console.log(orders))
  .catch(err => console.error(err));   // chain mein kahin bhi error → yahan
```

> **Chaining magic:** Agar tu `.then` mein **value return** karega, woh next `.then` ko milti hai. Agar **promise return** karega, next `.then` uske resolve hone ka wait karega.

### Real Example

```js
function makeOrder(amount) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (amount > 0) {
        resolve(`Order placed for ₹${amount}`);
      } else {
        reject("Invalid amount");
      }
    }, 1000);
  });
}

makeOrder(500)
  .then(msg => console.log(msg))      // 1 sec baad: "Order placed for ₹500"
  .catch(err => console.log(err));

makeOrder(-100)
  .then(msg => console.log(msg))
  .catch(err => console.log(err));    // 1 sec baad: "Invalid amount"
```

### Promise Utility Methods

#### `Promise.all` — Sab parallel mein, ek bhi fail to sab fail

```js
const p1 = fetch("/api/users");
const p2 = fetch("/api/products");
const p3 = fetch("/api/orders");

Promise.all([p1, p2, p3])
  .then(([users, products, orders]) => {
    // sab ke result destructure
  })
  .catch(err => {
    // koi ek bhi reject hua to yahan
  });
```

#### `Promise.race` — Sabse pehle jo settle ho

```js
Promise.race([fetchAPI(), timeout(5000)])
  .then(result => console.log(result));
// Agar API 5sec mein nahi aaya, timeout jeet jayega
```

#### `Promise.allSettled` — Sab ka result chahiye, success/fail dono

```js
Promise.allSettled([p1, p2, p3])
  .then(results => {
    results.forEach(r => {
      if (r.status === "fulfilled") console.log("OK:", r.value);
      else console.log("Fail:", r.reason);
    });
  });
```

### React Relevance

API calls in `useEffect` mostly promises hi return karte hain.

```jsx
useEffect(() => {
  fetch("/api/user")
    .then(res => res.json())
    .then(data => setUser(data))
    .catch(err => setError(err));
}, []);
```

---

## 11. async / await

### Definition

`async/await` promises ke upar **syntax sugar** hai. Async code ko **synchronous jaisa likhne** ka tarika. Readability bohot improve ho jaati hai.

### Kyun zaroori hai

Promises better hai callbacks se, but lambi `.then().then().then()` chain phir bhi messy ho jaati hai. `async/await` se code linear lagta hai — top-to-bottom padh sakte ho.

### Side-by-Side Comparison

```js
// Promise version
function getUser() {
  return fetch("/api/user")
    .then(res => res.json())
    .then(data => {
      console.log(data);
      return data;
    })
    .catch(err => console.error(err));
}

// async/await version — same kaam, cleaner
async function getUser() {
  try {
    const res = await fetch("/api/user");
    const data = await res.json();
    console.log(data);
    return data;
  } catch (err) {
    console.error(err);
  }
}
```

### Rules — Yaad Rakh

1. `await` **sirf** `async` function ke andar use kar sakta hai (ya top-level module mein)
2. `async` function **hamesha promise return karta hai** — tu plain value return kare to bhi
3. `await` ek promise ka wait karta hai — pause hota hai, but JS thread block nahi karta
4. Error handling ke liye `try/catch` use kar (ya `.catch` chain bhi laga sakte ho)

### Examples

```js
// Plain value bhi promise mein wrap ho jaata hai
async function getNumber() {
  return 42;
}
getNumber().then(v => console.log(v));    // 42

// await pause karta hai
async function demo() {
  console.log("Start");
  const result = await fetchData();        // yahan ruk jata hai
  console.log("Got:", result);
  console.log("End");
}
```

### Error Handling

```js
async function loadUser(id) {
  try {
    const res = await fetch(`/api/users/${id}`);
    if (!res.ok) throw new Error("User not found");

    const user = await res.json();
    return user;
  } catch (err) {
    console.error("Failed:", err.message);
    return null;
  }
}
```

### Sequential vs Parallel — Important Performance Tip

```js
// ❌ Sequential — slow
async function loadAll() {
  const users = await fetch("/api/users").then(r => r.json());      // wait
  const products = await fetch("/api/products").then(r => r.json()); // wait
  // Total time: users_time + products_time
}

// ✅ Parallel — fast (jab dono independent ho)
async function loadAll() {
  const [users, products] = await Promise.all([
    fetch("/api/users").then(r => r.json()),
    fetch("/api/products").then(r => r.json())
  ]);
  // Total time: max(users_time, products_time)
}
```

### Loop ke Andar `await`

```js
// Sequential — har iteration wait karta hai
async function processOne() {
  const ids = [1, 2, 3, 4];
  for (const id of ids) {
    const result = await fetch(`/api/items/${id}`);
    console.log(result);
  }
}

// Parallel — saari requests ek saath
async function processAll() {
  const ids = [1, 2, 3, 4];
  const promises = ids.map(id => fetch(`/api/items/${id}`));
  const results = await Promise.all(promises);
  console.log(results);
}
```

### React Relevance

```jsx
// useEffect mein async function — common pattern
useEffect(() => {
  // useEffect khud async nahi ho sakta, isliye andar fn banao
  const loadData = async () => {
    try {
      setLoading(true);
      const res = await fetch("/api/data");
      const json = await res.json();
      setData(json);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  loadData();
}, []);

// Event handler bhi async ho sakta hai
const handleSubmit = async (e) => {
  e.preventDefault();
  const res = await fetch("/api/save", { method: "POST", body: ... });
  // ...
};
```

---

## 12. Array Methods

Yeh methods **non-mutating** hain — original array ko change nahi karte, naya array (ya value) return karte hain. React mein iski wajah se yeh perfect hai (kyunki React immutability prefer karta hai).

---

### `map()` — Har Element Transform Kar

Har element pe function chala, naye values ka array bana.

```js
const arr = [1, 4, 9, 16];
const doubled = arr.map(x => x * 2);
console.log(doubled);           // [2, 8, 18, 32]
console.log(arr);               // [1, 4, 9, 16] — original safe

// Object array
const users = [
  { name: "Yash", age: 25 },
  { name: "Dad", age: 55 }
];
const names = users.map(u => u.name);          // ["Yash", "Dad"]
const labels = users.map(u => `${u.name} (${u.age})`); // ["Yash (25)", "Dad (55)"]

// Index ka access bhi mil sakta hai (second arg)
const indexed = arr.map((x, i) => `${i}: ${x}`);
// ["0: 1", "1: 4", "2: 9", "3: 16"]
```

> **Output array ki length hamesha original ke barabar hoti hai.** Map drop ya add nahi karta.

---

### `filter()` — Sirf Match Karne Wale Rakh

Predicate function `true` return kare to element rakhta hai.

```js
const numbers = [1, 2, 3, 4, 5, 6];
const evens = numbers.filter(n => n % 2 === 0);
console.log(evens);             // [2, 4, 6]

// Object array
const users = [
  { name: "A", age: 30 },
  { name: "B", age: 18 },
  { name: "C", age: 25 }
];
const adults = users.filter(u => u.age >= 21);
// [{name:"A",age:30}, {name:"C",age:25}]

// Falsy values hatana
const arr = [1, null, 2, undefined, 3, "", 4];
const truthy = arr.filter(Boolean);            // [1, 2, 3, 4]
```

---

### `reduce()` — Ek Single Value Mein Convert Kar

Sabse powerful but thoda samajhne mein time leta hai.

```js
const nums = [1, 2, 3, 4];
const total = nums.reduce((acc, curr) => acc + curr, 0);
console.log(total);             // 10

// Step-by-step (initial = 0):
// Step 1: acc=0, curr=1 → returns 0+1 = 1
// Step 2: acc=1, curr=2 → returns 1+2 = 3
// Step 3: acc=3, curr=3 → returns 3+3 = 6
// Step 4: acc=6, curr=4 → returns 6+4 = 10
```

#### Reduce ke Powerful Use Cases

**1. Sum/Max/Min**
```js
const max = nums.reduce((a, b) => a > b ? a : b);
const sum = nums.reduce((a, b) => a + b, 0);
```

**2. Group By**
```js
const items = [
  { cat: "fruit", v: 1 },
  { cat: "veg",   v: 2 },
  { cat: "fruit", v: 3 }
];
const grouped = items.reduce((acc, item) => {
  acc[item.cat] = (acc[item.cat] || 0) + item.v;
  return acc;
}, {});
// { fruit: 4, veg: 2 }
```

**3. Array → Object**
```js
const users = [{ id: 1, name: "A" }, { id: 2, name: "B" }];
const byId = users.reduce((acc, u) => {
  acc[u.id] = u;
  return acc;
}, {});
// { 1: {id:1,name:"A"}, 2: {id:2,name:"B"} }
```

**4. Flatten**
```js
const nested = [[1, 2], [3, 4], [5, 6]];
const flat = nested.reduce((acc, curr) => [...acc, ...curr], []);
// [1, 2, 3, 4, 5, 6]
```

> **Initial value (second arg)** zaroori hai jab return type alag ho ya array empty ho sakta hai. Best practice: hamesha do.

---

### `find()` — Pehla Match (ya `undefined`)

```js
const users = [
  { id: 1, name: "A" },
  { id: 2, name: "B" },
  { id: 3, name: "C" }
];
const u = users.find(u => u.id === 2);
console.log(u);                 // { id: 2, name: "B" }

const notFound = users.find(u => u.id === 99);
console.log(notFound);          // undefined

// Difference from filter — filter array deta hai, find ek item
users.filter(u => u.id === 2);  // [{ id: 2, name: "B" }]
users.find(u => u.id === 2);    // { id: 2, name: "B" }
```

---

### `forEach()` — Sirf Side Effects

Iterate karta hai, **kuch return nahi karta**. Bas function chalata hai har element pe.

```js
[1, 2, 3].forEach(n => console.log(n));   // 1, 2, 3 console pe

// ❌ forEach ka return undefined hota hai — chain nahi kar sakte
const x = [1, 2, 3].forEach(n => n * 2);
console.log(x);                 // undefined

// Agar transform chahiye, map use kar
const x = [1, 2, 3].map(n => n * 2);     // [2, 4, 6]
```

> **Rule of thumb:** Naya array chahiye → `map`. Sirf kuch karna hai (e.g., DOM update, console log) → `forEach`.

---

### Bonus Methods

```js
// some() — koi ek bhi match karta hai? (returns boolean)
[1, 2, 3].some(n => n > 2);     // true

// every() — saare match karte hain? (returns boolean)
[1, 2, 3].every(n => n > 0);    // true
[1, 2, 3].every(n => n > 1);    // false

// includes() — value hai? (simple equality)
[1, 2, 3].includes(2);          // true

// indexOf() / findIndex()
[1, 2, 3].indexOf(2);                     // 1
[1, 2, 3].findIndex(n => n > 1);          // 1
```

### Comparison Table

| Method | Returns | Mutates? | Use Case |
|---|---|---|---|
| `map` | New array (same length) | ❌ | Transform every item |
| `filter` | New array (≤ length) | ❌ | Keep matching items |
| `reduce` | Any value | ❌ | Combine into one thing |
| `find` | One item or undefined | ❌ | Get first match |
| `forEach` | undefined | ❌ | Side effects |
| `some` | boolean | ❌ | Any match? |
| `every` | boolean | ❌ | All match? |

### React Relevance

Lists rendering ka **bread-and-butter**.

```jsx
// Filter + map combo — React mein har jagah dikhega
<ul>
  {users
    .filter(u => u.active)
    .map(u => <li key={u.id}>{u.name}</li>)}
</ul>

// reduce — totals calculate karne ke liye
const total = cart.reduce((sum, item) => sum + item.price, 0);

// find — selected item nikalne ke liye
const current = items.find(i => i.id === selectedId);

// some — kuch check karne ke liye
const hasError = fields.some(f => f.error);
```

---

## 13. Optional Chaining & Nullish Coalescing

> Note: Yeh ES2020 mein aaye, ES6 ke baad. But React mein constantly use hote hain, isliye yahan cover kiye.

---

### Optional Chaining `?.`

**Definition:** Nested properties safely access karne ka tarika. Agar bich mein koi link `null` ya `undefined` hai, error throw karne ke jagah `undefined` return kar deta hai.

#### Without `?.` — Crash Hota Hai

```js
const user = { profile: { name: "Yash" } };

console.log(user.profile.name);      // "Yash" ✅
console.log(user.address.city);      // ❌ TypeError: Cannot read properties of undefined
```

#### With `?.` — Safe

```js
console.log(user?.profile?.name);    // "Yash"
console.log(user?.address?.city);    // undefined (no error)
console.log(user?.x?.y?.z?.w);       // undefined (no error)
```

#### Functions Pe Bhi Kaam Karta Hai

```js
const obj = {
  greet: () => "Hi!"
};

obj.greet?.();                  // "Hi!"
obj.notExist?.();               // undefined (no error)
obj.notExist();                 // ❌ TypeError
```

#### Array Index Pe Bhi

```js
const data = { items: null };

console.log(data.items[0]);     // ❌ TypeError
console.log(data.items?.[0]);   // undefined ✅
```

> **Important:** `?.` sirf `null` aur `undefined` pe rukta hai. `0`, `""`, `false` pe nahi.

---

### Nullish Coalescing `??`

**Definition:** Left side `null` ya `undefined` ho, tab right side return karta hai. **`||` se yahi farak hai** — `||` saari falsy values pe trigger hota hai.

#### Examples

```js
// ?? — sirf null/undefined pe
null      ?? "default"          // "default"
undefined ?? "default"          // "default"
0         ?? "default"          // 0   ✅ value safe hai
""        ?? "default"          // ""  ✅ value safe hai
false     ?? "default"          // false ✅
NaN       ?? "default"          // NaN ✅

// || — saari falsy pe trigger
0         || "default"          // "default" ❌ shayad galat
""        || "default"          // "default" ❌ shayad galat
false     || "default"          // "default" ❌
null      || "default"          // "default"
undefined || "default"          // "default"
```

#### `??` vs `||` — Real Example

```js
function getCount(val) {
  return val || 10;             // ❌ agar val=0, 10 return karega (galat)
}
getCount(0);                    // 10 — but actual count 0 tha!

function getCount(val) {
  return val ?? 10;             // ✅ val=0 to 0 return, val=null/undefined to 10
}
getCount(0);                    // 0 ✅
```

### Combine — The React Pattern

```js
const username = user?.profile?.name ?? "Guest";
// "user.profile.name agar exist karta hai to woh, warna 'Guest'"

const count = data?.results?.length ?? 0;
// Loading state mein 0 dikhayega
```

### React Relevance

API se data aate waqt undefined hota hai. `?.` aur `??` se safe access kar sakte ho.

```jsx
function Profile({ user }) {
  return (
    <div>
      <h1>{user?.name ?? "Loading..."}</h1>
      <p>{user?.profile?.bio ?? "No bio yet"}</p>
      <span>{user?.posts?.length ?? 0} posts</span>
    </div>
  );
}
```

---

## 14. Short-Circuit Evaluation

### Definition

Logical operators (`&&`, `||`) hamesha `true`/`false` return nahi karte. Yeh **operands** mein se ek return karte hain, jaise hi result decide ho jaye **rukh jaate hain** (short-circuit).

### `&&` — AND

Pehla **falsy** mile to wahi return, warna **last** wala.

```js
true && "hi";                   // "hi"
false && "hi";                  // false
1 && 2 && 3;                    // 3 (sab truthy, last wala)
1 && 0 && 3;                    // 0 (0 milte hi ruk gaya)
"a" && "" && "c";               // "" (empty string falsy)
```

**Logic:** "Sab truthy hai? Then last wala return kar." "Koi falsy mila? Then wahi return kar (kyunki AND = sab truthy hone chahiye)."

### `||` — OR

Pehla **truthy** mile to wahi return, warna **last** wala.

```js
null || "default";              // "default"
"a" || "b";                     // "a"
0 || "" || "x";                 // "x" (pehla truthy)
0 || "" || null;                // null (sab falsy, last wala)
```

**Logic:** "Koi truthy mila? Wahi return kar (kyunki OR = ek bhi truthy chahiye)."

### Falsy Values — Yaad Rakh

JavaScript mein **6 falsy values** hain (baaki sab truthy):
- `false`
- `0` (zero)
- `""` (empty string)
- `null`
- `undefined`
- `NaN`

### Practical Patterns

```js
// 1. Default value (legacy — modern code mein ?? better hai)
const name = inputName || "Guest";

// 2. Conditional execution
isLoggedIn && doSomething();    // sirf truthy hone par chalega

// 3. Short fallback chain
const value = userInput || localStorage.get('saved') || defaultValue;
```

### React Relevance — Conditional Rendering

JSX mein `if` statement nahi likh sakte (expression nahi hai). Isliye conditional rendering ke liye `&&` aur ternary use hota hai.

```jsx
// Sirf "if" — koi else nahi
{isLoggedIn && <Dashboard />}
{errors.length > 0 && <ErrorBox errors={errors} />}
{user && <Welcome name={user.name} />}

// Multiple conditions
{isReady && data && <Display data={data} />}
```

### ⚠️ Common Gotcha — `0` Render Ho Jata Hai

```jsx
// ❌ Bug — agar count = 0, screen pe "0" dikh jayega
{count && <List />}

// Reason: 0 && X returns 0, aur React 0 ko render karta hai (string mein convert)

// ✅ Fix 1 — explicit boolean check
{count > 0 && <List />}

// ✅ Fix 2 — double negation se boolean
{!!count && <List />}

// ✅ Fix 3 — ternary
{count ? <List /> : null}
```

> **Yaad rakh:** `&&` ka result agar `0`, `""`, ya `NaN` ho aur woh JSX mein hai, render ho jayega. Boolean explicitly chahiye.

---

## 15. Ternary Operator

### Definition

One-line `if/else` expression. Syntax: `condition ? valueIfTrue : valueIfFalse`

### Examples

```js
const age = 20;
const status = age >= 18 ? "adult" : "minor";
console.log(status);            // "adult"

// Function se return
const getDiscount = (isPremium) => isPremium ? 20 : 5;

// Variable assignment
const message = isLoggedIn ? `Welcome ${user.name}` : "Please login";
```

### Nested Ternary — Avoid Going Deep

```js
const grade =
  score >= 90 ? "A" :
  score >= 75 ? "B" :
  score >= 60 ? "C" : "F";
// Yeh padhne mein still OK hai

// Lekin yeh nahi:
const x = a ? b ? c ? "yes" : "no" : "maybe" : "what";
// 🤯 Pagal ho jata hai padhne wala
```

> **Best practice:** Nesting 2 levels se zyada na ho. Phir `if/else` ya `switch` use kar.

### Ternary vs `if/else`

```js
// if/else — statement, value return nahi karta directly
let status;
if (age >= 18) {
  status = "adult";
} else {
  status = "minor";
}

// Ternary — expression, directly assign
const status = age >= 18 ? "adult" : "minor";
```

### React Relevance — Both Branches Wala Conditional Rendering

JSX mein jab if-else dono branches chahiye, ternary use kar.

```jsx
// Loading vs content
{isLoading ? <Spinner /> : <Content data={data} />}

// Multiple states
{user
  ? <p>Hi, {user.name}</p>
  : <button>Login</button>}

// Inline class names
<div className={isActive ? "card active" : "card"}>...</div>

// Complex
{!data ? (
  <Loading />
) : data.length === 0 ? (
  <Empty />
) : (
  <List items={data} />
)}
```

### Quick Decision Guide

| Scenario | Use |
|---|---|
| Sirf `if` (no else) | `&&` |
| `if-else` (dono branches) | Ternary `? :` |
| Multiple branches (3+) | `if/else if/else` ya `switch` |

---

## 16. Classes

### Definition

JavaScript ki classes ek **template** hain objects banane ka. Internally yeh prototype-based inheritance ke upar **syntax sugar** hai — but Java/C++ ki tarah likhne ka chance milta hai.

### Kyun zaroori hai

- Object-oriented patterns ke liye
- React (legacy) class components
- Inheritance, encapsulation
- Modern JS frameworks aur libraries mein common

### Basic Syntax

```js
class Person {
  // Constructor — `new Person()` call karte hi yeh chalta hai
  constructor(name, age) {
    this.name = name;           // properties `this` pe lag jaati hain
    this.age = age;
  }

  // Methods — `function` keyword nahi chahiye
  greet() {
    console.log(`Hi, I'm ${this.name}`);
  }

  birthday() {
    this.age++;
    console.log(`Now I'm ${this.age}`);
  }
}

// Instance banao
const yash = new Person("Yash", 25);
yash.greet();                   // "Hi, I'm Yash"
yash.birthday();                // "Now I'm 26"
console.log(yash.name, yash.age); // "Yash" 26
```

### Getters and Setters

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // Getter — property ki tarah access hota hai (no parens)
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // Setter — value assign karte waqt chalta hai
  set fullName(value) {
    [this.firstName, this.lastName] = value.split(" ");
  }
}

const p = new Person("Yash", "Agarwal");
console.log(p.fullName);        // "Yash Agarwal" — getter call (no parens)
p.fullName = "Dad Agarwal";     // setter call
console.log(p.firstName);       // "Dad"
```

### Static Methods — Class Pe, Instance Pe Nahi

```js
class MathHelper {
  static double(n) {
    return n * 2;
  }

  static square(n) {
    return n * n;
  }
}

MathHelper.double(5);           // 10 ✅
MathHelper.square(4);           // 16 ✅

const m = new MathHelper();
m.double(5);                    // ❌ TypeError — instance pe nahi
```

> **Static methods** factory functions ya utility methods ke liye kaam aate hain.

### Inheritance — `extends` aur `super`

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  greet() {
    console.log(`Hi, I'm ${this.name}`);
  }
}

class Student extends Person {
  constructor(name, age, course) {
    super(name, age);           // ⚠️ super() call FIRST karna padta hai
    this.course = course;
  }

  // Method override
  greet() {
    super.greet();              // parent ka method call
    console.log(`I study ${this.course}`);
  }

  study() {
    console.log(`${this.name} studying ${this.course}`);
  }
}

const s = new Student("Yash", 25, "M.Tech CSE");
s.greet();
// "Hi, I'm Yash"
// "I study M.Tech CSE"
s.study();
// "Yash studying M.Tech CSE"
```

> **Rule:** Child class ke constructor mein `this` use karne se pehle `super()` call karna **mandatory** hai.

### Modern Class Fields

```js
class Counter {
  count = 0;                    // public field, constructor ki zarurat nahi
  #secret = 42;                 // private field (# prefix, ES2022)
  static defaultStep = 1;       // static field

  // Arrow method — auto-bound `this`
  increment = () => {
    this.count++;
  };

  decrement = () => {
    this.count--;
  };

  getSecret() {
    return this.#secret;        // private access only inside class
  }
}

const c = new Counter();
c.increment();
console.log(c.count);           // 1
console.log(c.#secret);         // ❌ SyntaxError — private nahi access kar sakte bahar se
console.log(c.getSecret());     // 42 ✅
```

### Common Galti — Method Loses `this`

```js
class Counter {
  count = 0;

  increment() {
    this.count++;
  }
}

const c = new Counter();
const inc = c.increment;
inc();                          // ❌ TypeError — `this` undefined ho gaya

// Fix 1: bind
const inc = c.increment.bind(c);

// Fix 2: arrow method (class field syntax)
class Counter {
  count = 0;
  increment = () => { this.count++; };  // ✅ this auto-bound
}
```

### React Relevance

Class components React 16.8 (2019) se pehle ka standard tha. Aaj bhi legacy code mein milte hain.

```jsx
class Counter extends React.Component {
  // Constructor — state initialize aur bind
  constructor(props) {
    super(props);
    this.state = { count: 0 };

    // ❗️ Yeh boilerplate the bohot common
    this.increment = this.increment.bind(this);
  }

  // Lifecycle method
  componentDidMount() {
    console.log("Mounted");
  }

  // Custom method
  increment() {
    this.setState({ count: this.state.count + 1 });
  }

  // Render method — JSX yahan return hota hai
  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>+1</button>
      </div>
    );
  }
}
```

Modern way (functional components + hooks):

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("Mounted");
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}
```

> **Aaj bhi classes seekhne ki zaroorat hai?** Haan, kyunki:
> 1. Legacy codebases mein milengi
> 2. Error boundaries abhi bhi class component se hi banti hain
> 3. OOP concepts samajhne ke liye

---

## Quick React Translation Table

| ES6 Feature | React Mein Kahan |
|---|---|
| `const` / `let` | Component definitions, hook calls — `const App = () => ...` |
| Arrow functions | Components, event handlers — `<button onClick={() => ...}>` |
| Template literals | Dynamic className, URLs — `` `card-${theme}` `` |
| Destructuring | Props, hook returns — `const { name } = props`, `const [x, setX] = useState()` |
| Object shorthand | State updates — `setForm({ ...form, [name]: value })` |
| Spread `...` | Immutable state, prop forwarding — `setUser({ ...user, age: 26 })` |
| Rest `...` | Catching extra props — `({ children, ...rest }) => <div {...rest}>` |
| `import` / `export` | **Har file** mein |
| Promises / async-await | `useEffect` mein API calls |
| `map` | Lists rendering — `{items.map(i => <li key={i.id}>...)}` |
| `filter` | Showing subset of list before render |
| `reduce` | Totals, grouping data |
| `?.` / `??` | Loading state mein safe access — `data?.user?.name ?? "..."` |
| `&&` / Ternary | Conditional rendering in JSX |
| Classes | Legacy class components, Error boundaries |

---

## Aage Kya?

In sab features pe pakad aane ke baad React seedha samajh aa jayega. React mein **naya kuch nahi hai** — bas yeh same JS features JSX ke saath organize hain.

### Next Steps for React:

1. **JSX** — HTML jaisa syntax JS ke andar
2. **Components & Props** — function components likhna
3. **`useState` & `useEffect`** — sabse common 2 hooks
4. **Event handling** — `onClick`, `onChange`, etc.
5. **Lists & Keys** — `map` ke saath `key` prop
6. **Conditional rendering** — `&&`, ternary in JSX
7. **Forms** — controlled components
8. **Custom hooks** — apne reusable hooks
9. **Context API** — global state
10. **React Router** — pages/routes

---

## Final Tips, Bhai

1. **Practice karte raho** — sirf padhne se kuch nahi hota. Console mein khol ke try kar har example.
2. **`const` default rakh** — `let` sirf jab zaroori. `var` bhool ja.
3. **Arrow functions for callbacks** — especially React mein.
4. **Spread `...` master kar** — React state ke liye yeh life-saving hai.
5. **`map` + `filter` combo** ko bhool nahi sakta — har list rendering yahi pattern hai.
6. **`?.` aur `??`** modern code mein essential hai — purane tutorials mein nahi milega zyada, but ab use kar.
7. **`&&` ka 0 wala bug** yaad rakh — bohot common mistake hai.

Ab React mein dive maar 🚀
