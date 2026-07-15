# JavaScript Full Tutorial — Basic to Advanced (Revision Guide)

---

## 1. Basics — মৌলিক বিষয় (Variables, Data Types, Operators)

> **বাংলায়:** এই অধ্যায়ে জাভাস্ক্রিপ্টের মৌলিক ধারণা—ভেরিয়েবল, ডেটা টাইপ ও অপারেটর—দেখানো হয়েছে।

### Variables — ভেরিয়েবল (var, let, const)
```js
var a = 10;    // function-scoped, hoisted, avoid using
let b = 20;    // block-scoped, can be reassigned
const c = 30;  // block-scoped, cannot be reassigned
```

### Data Types — ডেটা টাইপ (String, Number, Boolean ইত্যাদি)
```js
// Primitive types
let str = "hello";        // string
let num = 42;              // number
let big = 123n;            // bigint
let bool = true;           // boolean
let u = undefined;         // undefined
let n = null;              // null
let sym = Symbol("id");    // symbol

// Reference type
let obj = { name: "Alice" }; // object (includes arrays, functions)

typeof str;  // "string"
typeof obj;  // "object"
```

### Operators — অপারেটর (গাণিতিক, তুলনামূলক, লজিক্যাল)
```js
// Arithmetic
5 + 2, 5 - 2, 5 * 2, 5 / 2, 5 % 2, 5 ** 2

// Comparison
5 == "5"   // true  (loose, type coercion)
5 === "5"  // false (strict, no coercion)
5 != "5"   // false
5 !== "5"  // true

// Logical
true && false, true || false, !true

// Nullish coalescing & optional chaining
let x = null ?? "default";     // "default"
let y = obj?.address?.city;    // safe access, undefined if missing
```

### Type Coercion — টাইপ কোয়ার্সন (এক টাইপ থেকে অন্য টাইপে রূপান্তর)
```js
"5" + 3;    // "53" (string concatenation)
"5" - 3;    // 2 (numeric coercion)
Boolean(0), Boolean(""), Boolean(null), Boolean(undefined), Boolean(NaN); // all false
```

---

## 2. Control Flow — নিয়ন্ত্রণ প্রবাহ (if/else, switch, loops)

> **বাংলায়:** কোড কীভাবে শর্ত অনুযায়ী বা পুনরাবৃত্তিতে চলে তা এখানে দেখানো হয়েছে।

```js
// if / else
if (age >= 18) {
  console.log("Adult");
} else if (age >= 13) {
  console.log("Teen");
} else {
  console.log("Child");
}

// switch
switch (day) {
  case "Mon":
    console.log("Start");
    break;
  default:
    console.log("Other");
}

// Loops
for (let i = 0; i < 5; i++) { }
for (const item of [1, 2, 3]) { }       // iterates values
for (const key in { a: 1, b: 2 }) { }   // iterates keys
let i = 0;
while (i < 5) { i++; }
do { i++; } while (i < 5);

// break / continue
for (let i = 0; i < 10; i++) {
  if (i === 3) continue;
  if (i === 6) break;
}
```

---

## 3. Functions — ফাংশন (Declaration, Expression, Arrow)

> **বাংলায়:** ফাংশন ডিক্লারেশন, এক্সপ্রেশন, অ্যারো ফাংশন, ডিফল্ট ও রেস্ট প্যারামিটার এখানে আলোচনা করা হয়েছে।

```js
// Declaration (hoisted)
function add(a, b) { return a + b; }

// Expression (not hoisted)
const sub = function (a, b) { return a - b; };

// Arrow functions (no own `this`, `arguments`, or `super`)
const mul = (a, b) => a * b;
const square = x => x * x;
const greet = () => console.log("hi");

// Default parameters
function greetUser(name = "Guest") { console.log(`Hi ${name}`); }

// Rest parameters
function sumAll(...nums) { return nums.reduce((a, b) => a + b, 0); }

// Immediately Invoked Function Expression (IIFE)
(function () { console.log("runs immediately"); })();

// Higher-order functions
function multiplyBy(factor) {
  return function (num) { return num * factor; };
}
const double = multiplyBy(2);
```

---

## 4. Arrays — অ্যারে (push, map, filter, reduce)

> **বাংলায়:** অ্যারের সাধারণ ও ফাংশনাল মেথড (map, filter, reduce ইত্যাদি) এখানে দেখানো হয়েছে।

```js
const arr = [1, 2, 3, 4, 5];

// Common methods
arr.push(6);         arr.pop();
arr.shift();          arr.unshift(0);
arr.slice(1, 3);      // copy portion, non-mutating
arr.splice(1, 2);     // remove/insert, mutating
arr.concat([6, 7]);
arr.includes(3);
arr.indexOf(3);
arr.reverse();
arr.join("-");
arr.sort((a, b) => a - b); // numeric sort

// Iteration / functional methods
arr.forEach(x => console.log(x));
arr.map(x => x * 2);
arr.filter(x => x % 2 === 0);
arr.reduce((acc, x) => acc + x, 0);
arr.find(x => x > 3);
arr.findIndex(x => x > 3);
arr.some(x => x > 4);
arr.every(x => x > 0);
arr.flat();       // flatten nested arrays
arr.flatMap(x => [x, x * 2]);

// Destructuring
const [first, second, ...rest] = arr;

// Spread
const combined = [...arr, ...[6, 7]];
```

---

## 5. Objects — অবজেক্ট (Properties, Destructuring, Spread)

> **বাংলায়:** অবজেক্ট তৈরি, প্রপার্টি অ্যাক্সেস, ডেস্ট্রাকচারিং ও স্প্রেড অপারেটর নিয়ে এই অধ্যায়।

```js
const person = {
  name: "Alice",
  age: 25,
  greet() { console.log(`Hi, I'm ${this.name}`); }
};

// Access
person.name;
person["age"];

// Destructuring
const { name, age, city = "Unknown" } = person;

// Spread / merging
const updated = { ...person, age: 26 };

// Object methods
Object.keys(person);
Object.values(person);
Object.entries(person);
Object.assign({}, person, { age: 30 });
Object.freeze(person);   // immutable
Object.seal(person);     // no new/removed props, existing editable

// Computed property names
const key = "score";
const obj2 = { [key]: 100 };

// Shorthand
const x = 5, y = 10;
const point = { x, y };
```

---

## 6. Strings — স্ট্রিং (টেক্সট মেথড ও টেমপ্লেট লিটারেল)

> **বাংলায়:** স্ট্রিংয়ের বিভিন্ন মেথড এবং টেমপ্লেট লিটারেল (ব্যাকটিক দিয়ে মাল্টি-লাইন টেক্সট) এখানে দেখানো হয়েছে।

```js
const s = "Hello World";
s.length;
s.toUpperCase(); s.toLowerCase();
s.trim();
s.includes("World");
s.slice(0, 5);
s.split(" ");
s.replace("World", "JS");
s.padStart(15, "*");
s.repeat(2);

// Template literals
const name = "Bob";
const msg = `Hello, ${name}! Total: ${2 + 3}`;

// Multi-line
const multi = `Line 1
Line 2`;
```

---

## 7. Scope, Hoisting & Closures — স্কোপ, হোইস্টিং ও ক্লোজার

> **বাংলায়:** স্কোপ (দৃশ্যমানতা), হোইস্টিং (ভেরিয়েবল ওপরে তোলা) এবং ক্লোজার (ফাংশন তার আশেপাশের স্কোপ মনে রাখে) বুঝতে এই অধ্যায়টি গুরুত্বপূর্ণ।

```js
// Scope
function outer() {
  let x = 10;
  function inner() {
    console.log(x); // closure: inner "remembers" outer's scope
  }
  inner();
}

// Hoisting
console.log(a); // undefined (var is hoisted, not its value)
var a = 5;

console.log(b); // ReferenceError (temporal dead zone)
let b = 5;

// Closure example: counter factory
function createCounter() {
  let count = 0;
  return {
    increment: () => ++count,
    reset: () => (count = 0),
  };
}
const counter = createCounter();
counter.increment(); // 1
counter.increment(); // 2
```

---

## 8. `this`, call/apply/bind — this, call/apply/bind (ফাংশনের this নিয়ন্ত্রণ)

> **বাংলায়:** `this` কীভাবে কাজ করে এবং `call`, `apply`, `bind` দিয়ে তা কীভাবে বদলাতে হয় তা এখানে বোঝানো হয়েছে।

```js
const obj = {
  name: "Alice",
  regular: function () { console.log(this.name); },   // `this` = obj
  arrow: () => console.log(this.name),                 // `this` = outer scope
};

function show() { console.log(this.name); }
show.call({ name: "Bob" });     // Bob
show.apply({ name: "Carl" });   // Carl
const bound = show.bind({ name: "Dan" });
bound();                         // Dan
```

---

## 9. Prototypes & Classes — প্রোটোটাইপ ও ক্লাস

> **বাংলায়:** জাভাস্ক্রিপ্টে প্রোটোটাইপ-ভিত্তিক ইনহেরিটেন্স এবং ES6 ক্লাস (constructor, extends, static, getter) নিয়ে আলোচনা।

```js
// Prototype-based inheritance
function Animal(name) { this.name = name; }
Animal.prototype.speak = function () {
  console.log(`${this.name} makes a sound`);
};
const dog = new Animal("Rex");
dog.speak();

// ES6 Classes (syntax sugar over prototypes)
class Shape {
  constructor(name) { this.name = name; }
  area() { return 0; }
  static describe() { return "A shape"; } // static method
  get info() { return `Shape: ${this.name}`; } // getter
}

class Circle extends Shape {
  constructor(radius) {
    super("Circle");
    this.radius = radius;
  }
  area() { return Math.PI * this.radius ** 2; } // override
}

const c = new Circle(5);
c.area();
c instanceof Shape; // true

// Private fields (ES2022)
class Counter {
  #count = 0; // private
  increment() { return ++this.#count; }
}
```

---

## 10. Error Handling — এরর হ্যান্ডলিং (try/catch, Custom Errors)

> **বাংলায়:** `try/catch/finally` দিয়ে এরর সামলানো এবং নিজের কাস্টম এরর ক্লাস তৈরি করা এখানে দেখানো হয়েছে।

```js
try {
  JSON.parse("invalid json");
} catch (err) {
  console.error("Error:", err.message);
} finally {
  console.log("Always runs");
}

// Custom errors
class ValidationError extends Error {
  constructor(msg) {
    super(msg);
    this.name = "ValidationError";
  }
}
function validate(age) {
  if (age < 0) throw new ValidationError("Age can't be negative");
}
```

---

## 11. Asynchronous JavaScript — অ্যাসিনক্রোনাস জাভাস্ক্রিপ্ট (Callback, Promise, async/await)

> **বাংলায়:** অ্যাসিনক্রোনাস বা অপেক্ষামূলক কোড লেখার তিনটি পদ্ধতি—কলব্যাক, প্রমিস ও async/await—এখানে ব্যাখ্যা করা হয়েছে।

### Callbacks — কলব্যাক (ফাংশনকে আর্গুমেন্ট হিসেবে পাঠানো)
```js
function fetchData(callback) {
  setTimeout(() => callback("data"), 1000);
}
fetchData(data => console.log(data));
```

### Promises — প্রমিস (ভবিষ্যতের ফলাফল)
```js
const promise = new Promise((resolve, reject) => {
  const success = true;
  success ? resolve("Done!") : reject("Failed!");
});

promise
  .then(result => console.log(result))
  .catch(err => console.error(err))
  .finally(() => console.log("Complete"));

// Combinators
Promise.all([p1, p2, p3]);        // fails fast, waits for all
Promise.allSettled([p1, p2, p3]); // waits for all, never rejects
Promise.race([p1, p2]);           // first to settle
Promise.any([p1, p2]);            // first to fulfill
```

### Async/Await — async/await (প্রমিসের ওপর সুন্দর সিনট্যাক্স)
```js
async function getData() {
  try {
    const res = await fetch("https://api.example.com/data");
    const json = await res.json();
    console.log(json);
  } catch (err) {
    console.error(err);
  }
}

// Parallel async calls
async function getBoth() {
  const [a, b] = await Promise.all([getA(), getB()]);
}
```

### Event Loop (concept) — ইভেন্ট লুপ (ধারণা)

> **বাংলায়:** জাভাস্ক্রিপ্ট একক থ্রেডে চলে; ইভেন্ট লুপ কল স্ট্যাক, ওয়েব API ও কুইউগুলো সামলায়। মাইক্রোটাস্ক (প্রমিস) ম্যাক্রোটাস্ক (setTimeout)-এর আগে চলে।
```
Call Stack → Web APIs → Callback Queue / Microtask Queue → Event Loop
Microtasks (Promises) run BEFORE macrotasks (setTimeout).
```
```js
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
console.log("4");
// Output: 1, 4, 3, 2
```

---

## 12. DOM Manipulation — DOM ম্যানিপুলেশন (এলিমেন্ট ও ইভেন্ট)

> **বাংলায়:** ব্রাউজারের HTML এলিমেন্ট নির্বাচন, পরিবর্তন ও ইভেন্ট (ক্লিক ইত্যাদি) হ্যান্ডেল করার পদ্ধতি এখানে দেখানো হয়েছে।

```js
// Selecting
document.getElementById("id");
document.querySelector(".class");
document.querySelectorAll("div");

// Modifying
el.textContent = "New text";
el.innerHTML = "<b>Bold</b>";
el.classList.add("active");
el.classList.remove("hidden");
el.classList.toggle("open");
el.setAttribute("data-id", "5");
el.style.color = "red";

// Creating / inserting
const div = document.createElement("div");
parent.appendChild(div);
parent.insertBefore(div, referenceNode);
el.remove();

// Events
el.addEventListener("click", (e) => {
  console.log("Clicked", e.target);
});

// Event delegation
document.body.addEventListener("click", (e) => {
  if (e.target.matches(".btn")) console.log("Button clicked");
});
```

---

## 13. ES6+ Modules — ES6+ মডিউল (import/export)

> **বাংলায়:** কোডকে ছোট ছোট ফাইলে ভাগ করে `import`/`export` দিয়ে পুনর্ব্যবহার করার নিয়ম এখানে দেখানো হয়েছে।

```js
// math.js
export const PI = 3.14;
export function add(a, b) { return a + b; }
export default function multiply(a, b) { return a * b; }

// main.js
import multiply, { PI, add } from "./math.js";
import * as MathUtils from "./math.js";
```

---

## 14. Iterators & Generators — ইটারেটর ও জেনারেটর

> **বাংলায়:** `for...of` লুপের পেছনের ইটারেটর এবং `function*` জেনারেটর (যা `yield` দিয়ে মাঝপথে থামে) নিয়ে এই অধ্যায়।

```js
// Generator function
function* idGenerator() {
  let id = 1;
  while (true) {
    yield id++;
  }
}
const gen = idGenerator();
gen.next().value; // 1
gen.next().value; // 2

// Custom iterator
const range = {
  [Symbol.iterator]() {
    let current = 0, end = 5;
    return {
      next() {
        return current < end
          ? { value: current++, done: false }
          : { value: undefined, done: true };
      }
    };
  }
};
[...range]; // [0, 1, 2, 3, 4]
```

---

## 15. Advanced Object Features — অ্যাডভান্সড অবজেক্ট ফিচার (Symbol, Proxy, WeakMap)

> **বাংলায়:** `Symbol` (ইউনিক কী), `Proxy` (অপারেশন ইন্টারসেপ্ট), `WeakMap`/`Map`/`Set` ইত্যাদি উন্নত অবজেক্ট ফিচার নিয়ে আলোচনা।

```js
// Symbols — unique identifiers
const id = Symbol("id");
const user = { [id]: 123, name: "Alice" };

// Proxy — intercept operations
const handler = {
  get(target, prop) { return prop in target ? target[prop] : `${prop} not found`; }
};
const p = new Proxy({ name: "Alice" }, handler);
p.age; // "age not found"

// Reflect — utility methods mirroring proxy traps
Reflect.has(user, "name");
Reflect.ownKeys(user);

// WeakMap / WeakSet — garbage-collectable keyed collections
const wm = new WeakMap();
let obj = {};
wm.set(obj, "metadata");

// Map / Set
const map = new Map([["a", 1], ["b", 2]]);
map.get("a"); map.set("c", 3); map.has("b"); map.delete("a");

const set = new Set([1, 2, 2, 3]); // {1, 2, 3}
```

---

## 16. Functional Programming Concepts — ফাংশনাল প্রোগ্রামিং ধারণা

> **বাংলায়:** পিওর ফাংশন, ইমিউটেবিলিটি, কারিং, কম্পোজিশন ও মেমোাইজেশনের মতো ফাংশনাল প্রোগ্রামিং ধারণা এখানে দেখানো হয়েছে।

```js
// Pure functions — no side effects, same input → same output
const pureAdd = (a, b) => a + b;

// Immutability
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4]; // don't mutate arr1

// Currying
const curriedAdd = a => b => c => a + b + c;
curriedAdd(1)(2)(3); // 6

// Composition
const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);
const process = pipe(x => x + 1, x => x * 2);
process(3); // 8

// Memoization
function memoize(fn) {
  const cache = new Map();
  return (...args) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}
```

---

## 17. Debounce & Throttle (common interview asks) — ডিবাউন্স ও থ্রটল (সাধারণ ইন্টারভিউ প্রশ্ন)

> **বাংলায়:** ডিবাউন্স (বিরতির পর একবার চালায়) ও থ্রটল (নির্দিষ্ট সময়ে সর্বোচ্চ একবার চালায়) ফাংশন ফ্রিকোয়েন্সি কমাতে ব্যবহৃত হয়।

```js
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}

function throttle(fn, limit) {
  let waiting = false;
  return (...args) => {
    if (!waiting) {
      fn(...args);
      waiting = true;
      setTimeout(() => (waiting = false), limit);
    }
  };
}
```

---

## 18. `JSON`, Dates, and Math — JSON, তারিখ ও ম্যাথ

> **বাংলায়:** `JSON` (অবজেক্ট ↔ টেক্সট রূপান্তর), `Date` (তারিখ/সময়) ও `Math` (গাণিতিক ফাংশন) নিয়ে এই অধ্যায়।

```js
JSON.stringify({ a: 1 });
JSON.parse('{"a":1}');

const date = new Date();
date.getFullYear(); date.getMonth(); date.getDate();
date.toISOString();

Math.max(1, 2, 3); Math.min(1, 2, 3);
Math.round(4.5); Math.floor(4.9); Math.ceil(4.1);
Math.random(); // [0, 1)
```

---

## 19. Modules of Modern Syntax Sugar — আধুনিক সিনট্যাক্স সুগার

> **বাংলায়:** অপশনাল চেইনিং (`?.`), নালিশ কোয়ালেসিং (`??`), লজিক্যাল অ্যাসাইনমেন্ট ও নিউমেরিক সেপারেটরের মতো নতুন সুবিধাজনক সিনট্যাক্স এখানে দেখানো হয়েছে।

```js
// Optional chaining
user?.profile?.email;

// Nullish coalescing
const val = input ?? "default";

// Logical assignment
x ||= y;   // x = x || y
x &&= y;   // x = x && y
x ??= y;   // x = x ?? y

// Numeric separators
const million = 1_000_000;

// Array/Object destructuring with defaults & renaming
const { name: userName = "Anon" } = user;
```

---

## 20. Common Design Patterns — সাধারণ ডিজাইন প্যাটার্ন

> **বাংলায়:** মডিউল, সিঙ্গলটন, অবজারভার (ইভেন্ট এমিটার) ও ফ্যাক্টরি—জাভাস্ক্রিপ্টে ব্যবহৃত পরিচিত ডিজাইন প্যাটার্নগুলো এখানে দেওয়া হয়েছে।

```js
// Module Pattern
const Counter = (function () {
  let count = 0;
  return { increment: () => ++count, get: () => count };
})();

// Singleton
class Singleton {
  static instance;
  constructor() {
    if (Singleton.instance) return Singleton.instance;
    Singleton.instance = this;
  }
}

// Observer Pattern
class EventEmitter {
  constructor() { this.events = {}; }
  on(event, listener) {
    (this.events[event] ||= []).push(listener);
  }
  emit(event, ...args) {
    (this.events[event] || []).forEach(fn => fn(...args));
  }
}

// Factory Pattern
function createUser(type) {
  if (type === "admin") return { role: "admin", access: "all" };
  return { role: "user", access: "limited" };
}
```

---

## 21. Quick Reference: Interview Concepts — দ্রুত রেফারেন্স: ইন্টারভিউ ধারণাসমূহ

> **বাংলায়:** ইন্টারভিউতে সচরাচর জিজ্ঞাসিত গুরুত্বপূর্ণ ধারণাগুলো এক নজরে টেবিল আকারে দেওয়া হয়েছে।

| Concept | Key Point |
|---|---|
| `==` vs `===` | loose vs strict equality |
| `var` vs `let/const` | function-scoped vs block-scoped |
| Hoisting | declarations moved up, `var` initialized as `undefined`, `let/const` in TDZ |
| Closures | function retains access to its lexical scope |
| Event loop | microtasks (Promises) before macrotasks (setTimeout) |
| `this` | determined by how a function is called, not where defined (except arrow fns) |
| Prototype chain | objects inherit via `__proto__` / `Object.getPrototypeOf` |
| Shallow vs deep copy | spread/`Object.assign` copy one level deep only |
| Pure function | no side effects, deterministic output |
| Debounce vs throttle | delay till pause vs limit rate |

---

## 22. Practice Tips — অনুশীলনের টিপস

> **বাংলায়:** নিজে হাতে কোড লিখে ও আউটপুট অনুমান করে দক্ষতা বাড়ানোর কিছু পরামর্শ এখানে দেওয়া হয়েছে।
- Rebuild `map`, `filter`, `reduce` from scratch to test understanding.
- Predict output of async/event-loop snippets before running them.
- Convert callback-based code to Promises, then to async/await.
- Implement debounce, throttle, deep clone, and curry without looking them up.
- Read MDN docs for anything unclear: https://developer.mozilla.org/en-US/docs/Web/JavaScript

---

*End of tutorial — happy revising!*
