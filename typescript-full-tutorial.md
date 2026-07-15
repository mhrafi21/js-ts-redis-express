# TypeScript Full Tutorial — Basic to Advanced (with Explanations)
# টাইপস্ক্রিপ্ট সম্পূর্ণ টিউটোরিয়াল — বেসিক থেকে অ্যাডভান্সড (ব্যাখ্যাসহ)

> **বাংলায়:** টাইপস্ক্রিপ্ট হলো জাভাস্ক্রিপ্টের ওপর একটি টাইপ সিস্টেম যোগ করা ভাষা। এটি প্লেইন জাভাস্ক্রিপ্টে কম্পাইল (ট্রান্সপাইল) হয়, তাই রানটাইমে ক্র্যাশ হওয়ার আগেই কোড লেখার সময় ভুল ধরে দেয়।

---

## 1. Setup — সেটআপ (টাইপস্ক্রিপ্ট ইনস্টল ও কম্পাইল)

> **বাংলায়:** `npm` দিয়ে টাইপস্ক্রিপ্ট ইনস্টল করে `tsc` কমান্ডে `.ts` ফাইল কম্পাইল করা হয়। `tsconfig.json` দিয়ে কম্পাইলার কতটা স্ট্রিক্ট হবে তা ঠিক করা হয়।

```bash
npm install -g typescript
tsc --init          # creates tsconfig.json
tsc file.ts          # compiles file.ts -> file.js
tsc --watch          # auto-recompile on save
```

`tsconfig.json` controls how strict and how modern the compiler behaves. Turning on `"strict": true` is highly recommended — it enables a bundle of safety checks (no implicit `any`, strict null checks, etc.).

> **বাংলায়:** `tsconfig.json` কম্পাইলারের স্ট্রিক্টনেস ও মডার্নতা নিয়ন্ত্রণ করে। `"strict": true` চালু করার পরামর্শ দেওয়া হয়—এতে ইম্প্লিসিট `any` বন্ধ ও নাল-চেক সক্রিয় হয়।

---

## 2. Basic Types — মৌলিক টাইপ (number, string, boolean, tuple)

> **বাংলায়:** টাইপস্ক্রিপ্টে ভেরিয়েবলের টাইপ আগেই বলে দেওয়া যায়—নাম্বার, স্ট্রিং, বুলিয়ান, অ্যারে, টাপল, `any`, `unknown`, `void`, `never` ইত্যাদি।

```ts
let age: number = 25;
let name: string = "Alice";
let isActive: boolean = true;
let big: bigint = 100n;
let sym: symbol = Symbol("id");

// Arrays
let nums: number[] = [1, 2, 3];
let names: Array<string> = ["a", "b"]; // generic syntax, same thing

// Tuples — fixed-length array with known types per position
let point: [number, number] = [10, 20];
let entry: [string, number] = ["age", 25];

// Any — disables type checking (avoid when possible)
let anything: any = "could be anything";

// Unknown — like `any` but forces you to check the type before using it
let value: unknown = fetchValue();
if (typeof value === "string") {
  console.log(value.toUpperCase()); // safe, narrowed to string here
}

// Void — function returns nothing meaningful
function log(msg: string): void { console.log(msg); }

// Never — function never returns (throws or infinite loop)
function fail(msg: string): never { throw new Error(msg); }

// Null & undefined
let u: undefined = undefined;
let n: null = null;
```

**Why `unknown` over `any`:** `any` turns off type checking entirely — TypeScript stops helping you. `unknown` still forces a type check before you can use the value, so you keep safety while still allowing flexibility.

> **বাংলায়:** `any` টাইপ-চেকিং সম্পূর্ণ বন্ধ করে দেয়, কিন্তু `unknown` ব্যবহারের আগে টাইপ চেক করতে বাধ্য করে—তাই নমনীয়তা রেখেও সেফটি বজায় থাকে।

---

## 3. Type Inference & Annotations — টাইপ অনুমান ও টাইপ দেওয়া

> **বাংলায়:** টাইপস্ক্রিপ্ট অনেক সময় ভেরিয়েবলের টাইপ নিজেই বুঝে নেয় (ইনফারেন্স)। তবে ফাংশনের প্যারামিটারে স্পষ্ট টাইপ দেওয়া ভালো।

```ts
// TS infers types automatically — you don't always need to annotate
let city = "Delhi";        // inferred as string
let count = 10;             // inferred as number

// But function parameters need explicit types (TS can't guess these)
function add(a: number, b: number): number {
  return a + b;
}
```

**Rule of thumb:** let TypeScript infer variable types where obvious; annotate function parameters and public API boundaries explicitly for clarity and safety.

> **বাংলায়:** নিয়ম হলো—সহজ বোঝা গেলে টাইপ অনুমান করতে দিন, কিন্তু ফাংশন প্যারামিটার ও পাবলিক API-এর সীমানায় স্পষ্ট টাইপ লিখুন স্পষ্টতা ও সেফটির জন্য।

---

## 4. Interfaces & Type Aliases — ইন্টারফেস ও টাইপ অ্যালায়াস

> **বাংলায়:** ইন্টারফেস ও টাইপ অ্যালায়াস দিয়ে অবজেক্টের গঠন (শেপ) বোঝানো যায়। ইন্টারফেস `extends` করা যায়, আর `type` দিয়ে ইউনিয়ন বা ইন্টারসেকশনও লেখা যায়।

Both describe the *shape* of an object. They're similar but have some differences.

```ts
// Interface
interface User {
  id: number;
  name: string;
  email?: string;       // optional property
  readonly createdAt: Date; // can't be reassigned after creation
}

const user: User = { id: 1, name: "Alice", createdAt: new Date() };

// Type alias — can describe objects, unions, primitives, tuples, etc.
type ID = string | number;
type Point = { x: number; y: number };

// Extending
interface Admin extends User {
  role: string;
}

type AdminType = Point & { z: number }; // intersection instead of extends
```

**Interface vs type:** interfaces can be re-opened and merged (declaration merging) and are generally preferred for object shapes; `type` is more flexible for unions, tuples, and mapped types. In modern practice, use `interface` for objects/classes and `type` for everything else (unions, function signatures, utility types).

> **বাংলায়:** ইন্টারফেস পরে খোলা ও মার্জ করা যায় এবং অবজেক্ট শেপের জন্য এটি বেশি ব্যবহৃত হয়; `type` ইউনিয়ন, টাপল ও ম্যাপড টাইপের জন্য বেশি নমনীয়। অবজেক্ট/ক্লাসের জন্য `interface` এবং বাকি ক্ষেত্রে `type` ব্যবহার করুন।

---

## 5. Functions — ফাংশন (টাইপড প্যারামিটার ও রিটার্ন)

> **বাংলায়:** ফাংশনের প্যারামিটার ও রিটার্ন টাইপ দেওয়া যায়, সাথে অপশনাল, ডিফল্ট, রেস্ট প্যারামিটার এবং ওভারলোডিং।

```ts
// Typed parameters & return type
function multiply(a: number, b: number): number {
  return a * b;
}

// Optional & default parameters
function greet(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}`;
}
function log(msg: string, tag?: string) { }

// Rest parameters
function sum(...nums: number[]): number {
  return nums.reduce((a, b) => a + b, 0);
}

// Function type
let calc: (a: number, b: number) => number;
calc = (a, b) => a + b;

// Overloads — multiple call signatures for one function
function process(x: number): number;
function process(x: string): string;
function process(x: any): any {
  return typeof x === "number" ? x * 2 : x.toUpperCase();
}
```

---

## 6. Union & Intersection Types — ইউনিয়ন ও ইন্টারসেকশন টাইপ

> **বাংলায়:** ইউনিয়ন (`A | B`) মানে "এটি A অথবা B"; ইন্টারসেকশন (`A & B`) মানে "দুটোই থাকতে হবে"। API রেস্পন্স বা বড় শেপ তৈরিতে এরা কাজে লাগে।

```ts
// Union: value can be one of several types
let id: string | number;
id = "abc";
id = 123;

function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // narrowed to string
  } else {
    console.log(id.toFixed(2));    // narrowed to number
  }
}

// Intersection: combine multiple types into one
type Named = { name: string };
type Aged = { age: number };
type Person = Named & Aged; // must have both name AND age
```

**Why this matters:** unions model "this OR that" (like an API response that's either success or error), while intersections model "this AND that" (combining smaller shapes into a richer one).

---

**Why this matters:** unions model "this OR that" (like an API response that's either success or error), while intersections model "this AND that" (combining smaller shapes into a richer one).

> **বাংলায়:** ইউনিয়ন "এটি অথবা ওটি" (যেমন সাফল্য বা এরর রেস্পন্স) মডেল করে, আর ইন্টারসেকশন "এটি এবং ওটি"—ছোট শেপ মিলিয়ে বড় শেপ বানাতে সাহায্য করে।

---

## 7. Literal Types & Enums — লিটারেল টাইপ ও এনাম

> **বাংলায়:** লিটারেল টাইপ নির্দিষ্ট কয়েকটি মানেই সীমাবদ্ধ করে; এনাম (enum) হলো নামকরণ করা কনস্ট্যান্টের সেট। `const enum` কম্পাইল টাইমে ইনলাইন হয়।

```ts
// Literal types — restrict a value to specific exact values
let direction: "up" | "down" | "left" | "right";
direction = "up";     // ok
// direction = "north"; // Error

// Enums — named set of constants
enum Status {
  Active,
  Inactive,
  Pending,
}
let s: Status = Status.Active; // 0

enum Color {
  Red = "RED",
  Green = "GREEN",
}

// const enum — inlined at compile time, no runtime object generated (more efficient)
const enum Direction { Up, Down }
```

**Enums vs union literals:** modern TS style often prefers union literal types (`"up" | "down"`) over enums because they're simpler and don't generate extra JS code, but enums are useful when you want a named, iterable set of constants.

---

**Enums vs union literals:** modern TS style often prefers union literal types (`"up" | "down"`) over enums because they're simpler and don't generate extra JS code, but enums are useful when you want a named, iterable set of constants.

> **বাংলায়:** আধুনিক টিএস-এ সাধারণত এনামের বদলে ইউনিয়ন লিটারেল (`"up" | "down"`) ব্যবহার করা হয় কারণ এটি সহজ ও অতিরিক্ত JS তৈরি করে না; তবে নামকরণ করা ইটারেবল কনস্ট্যান্ট দরকার হলে এনাম কাজে লাগে।

---

## 8. Arrays, Objects & Destructuring — অ্যারে, অবজেক্ট ও ডেস্ট্রাকচারিং

> **বাংলায়:** টাইপস্ক্রিপ্টে অ্যারে ও অবজেক্টের টাইপ দেওয়া এবং ডেস্ট্রাকচারিংয়ের সময় টাইপ বলে দেওয়া যায়।

```ts
const numbers: number[] = [1, 2, 3];
const mixed: (string | number)[] = [1, "two", 3];

interface Config {
  host: string;
  port: number;
}
const config: Config = { host: "localhost", port: 8080 };

// Destructuring with types
const { host, port }: Config = config;
const [first, second]: [number, number] = [10, 20];
```

---

## 9. Classes — ক্লাস (Access Modifiers, Abstract)

> **বাংলায়:** টিএস ক্লাসে `public`, `private`, `protected`, `readonly` মডিফায়ার, শর্টহ্যান্ড কনস্ট্রাক্টর, অ্যাবস্ট্রাক্ট ক্লাস ও `implements` ব্যবহার করা যায়।

```ts
class Animal {
  // property with access modifier
  protected name: string;
  private age: number;
  public readonly species: string;

  constructor(name: string, age: number, species: string) {
    this.name = name;
    this.age = age;
    this.species = species;
  }

  speak(): void {
    console.log(`${this.name} makes a sound.`);
  }
}

class Dog extends Animal {
  constructor(name: string, age: number) {
    super(name, age, "Canine");
  }

  speak(): void { // override
    console.log(`${this.name} barks.`);
  }
}

// Shorthand constructor properties (very common in real code)
class Point {
  constructor(public x: number, public y: number) {}
}

// Abstract classes — can't be instantiated directly, must be extended
abstract class Shape {
  abstract area(): number; // must be implemented by subclasses
  describe(): string {
    return `Area: ${this.area()}`;
  }
}
class Circle extends Shape {
  constructor(private radius: number) { super(); }
  area(): number { return Math.PI * this.radius ** 2; }
}

// Interfaces implemented by classes
interface Flyable {
  fly(): void;
}
class Bird implements Flyable {
  fly() { console.log("Flying!"); }
}
```

**Access modifiers:**
- `public` — accessible anywhere (default)
- `private` — only within the same class
- `protected` — within the class and subclasses
- `readonly` — can be set once (usually in constructor), then immutable

> **বাংলায় অ্যাক্সেস মডিফায়ার:**
> - `public` — যেকোনো জায়গা থেকে অ্যাক্সেসযোগ্য (ডিফল্ট)
> - `private` — শুধু সেই ক্লাসের ভেতরে
> - `protected` — ক্লাস ও এর সাবক্লাসের ভেতরে
> - `readonly` — একবার সেট করা যায় (সাধারণত কনস্ট্রাক্টরে), তারপর অপরিবর্তনীয়

---

## 10. Generics — জেনেরিক (পুনর্ব্যবহারযোগ্য টাইপ-সেফ কোড)

> **বাংলায়:** জেনেরিক দিয়ে একই কোড একাধিক টাইপের জন্য ব্যবহার করা যায়, তবু টাইপ ইনফরমেশন নষ্ট হয় না। `T` দিয়ে টাইপ প্যারামিটার বোঝানো হয়।

Generics let you write reusable code that works with multiple types while still preserving type safety — instead of using `any` (which loses type info) or writing duplicate functions per type.

> **বাংলায়:** জেনেরিক ব্যবহার করে একাধিক টাইপের সাথে কাজ করে এমন পুনর্ব্যবহারযোগ্য কোড লেখা যায়, তবু টাইপ সেফটি থাকে—`any` ব্যবহার করলে যা হারিয়ে যায়।

```ts
// Generic function
function identity<T>(value: T): T {
  return value;
}
identity<string>("hello");
identity(42); // T inferred as number

// Generic interface
interface Box<T> {
  contents: T;
}
const stringBox: Box<string> = { contents: "gift" };

// Generic class
class Stack<T> {
  private items: T[] = [];
  push(item: T): void { this.items.push(item); }
  pop(): T | undefined { return this.items.pop(); }
}
const numberStack = new Stack<number>();

// Generic constraints — restrict what T can be
function getLength<T extends { length: number }>(item: T): number {
  return item.length;
}
getLength("hello"); // ok, strings have .length
getLength([1, 2, 3]); // ok, arrays have .length
// getLength(42); // Error, numbers don't have .length

// Multiple type parameters
function merge<T, U>(a: T, b: U): T & U {
  return { ...a, ...b };
}
```

---

## 11. Type Narrowing & Guards — টাইপ ন্যারোইং ও গার্ড

> **বাংলায়:** `typeof`, `instanceof`, `in` বা কাস্টম প্রেডিকেট গার্ড দিয়ে ইউনিয়ন টাইপকে ছোট নির্দিষ্ট টাইপে সীমাবদ্ধ (ন্যারো) করা যায়।

```ts
function printValue(value: string | number) {
  // typeof guard
  if (typeof value === "string") {
    console.log(value.toUpperCase());
  }
}

// instanceof guard
class Cat { meow() {} }
class Dog { bark() {} }
function speak(animal: Cat | Dog) {
  if (animal instanceof Cat) {
    animal.meow();
  } else {
    animal.bark();
  }
}

// in operator guard
interface Fish { swim(): void; }
interface Bird { fly(): void; }
function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    animal.swim();
  } else {
    animal.fly();
  }
}

// Custom type guard (predicate function)
function isString(value: unknown): value is string {
  return typeof value === "string";
}
function handle(value: unknown) {
  if (isString(value)) {
    console.log(value.toUpperCase()); // narrowed to string
  }
}
```

---

## 12. Utility Types — ইউটিলিটি টাইপ (Partial, Pick, Omit, Record)

> **বাংলায়:** টিএস-এ বিল্ট-ইন হেল্পার টাইপ থাকে যা অন্য টাইপকে রূপান্তর করে—যেমন `Partial`, `Required`, `Readonly`, `Pick`, `Omit`, `Record`।

TypeScript ships built-in helper types that transform other types — extremely common in real-world code.

> **বাংলায়:** টাইপস্ক্রিপ্টে বিল্ট-ইন হেল্পার টাইপ থাকে যা অন্য টাইপকে রূপান্তর করে—বাস্তব প্রজেক্টে খুব বেশি ব্যবহৃত হয়।

```ts
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
}

Partial<User>;       // all properties optional
Required<User>;      // all properties required
Readonly<User>;      // all properties readonly
Pick<User, "id" | "name">;      // only chosen keys
Omit<User, "password">;         // all except chosen keys
Record<string, number>;         // object type with string keys, number values

// Example
function updateUser(id: number, changes: Partial<User>) {
  // caller doesn't need to pass every field
}

type PublicUser = Omit<User, "password">;

// Extract & Exclude — work with union members
type Status = "active" | "inactive" | "banned";
Exclude<Status, "banned">;   // "active" | "inactive"
Extract<Status, "active" | "x">; // "active"

// ReturnType & Parameters — extract types from functions
function createUser(name: string, age: number) {
  return { name, age };
}
type NewUser = ReturnType<typeof createUser>; // { name: string; age: number }
type CreateUserParams = Parameters<typeof createUser>; // [string, number]
```

---

## 13. Type Assertions — টাইপ অ্যাসারশন (কম্পাইলারকে "বিশ্বাস করানো")

> **বাংলায়:** কখনো কখনো আপনি ভ্যালুর টাইপ সম্পর্কে কম্পাইলারের চেয়ে বেশি জানেন—`as` দিয়ে তা বলা যায়। তবে এটি রানটাইম চেক করে না, তাই সাবধানে ব্যবহার করুন।

Sometimes you know more about a value's type than TypeScript does. Assertions tell the compiler "trust me" — but they don't perform any runtime conversion or checking, so use them carefully.

> **বাংলায়:** কখনো কখনো আপনি ভ্যালুর টাইপ সম্পর্কে কম্পাইলারের চেয়ে বেশি জানেন। অ্যাসারশন কম্পাইলারকে "আমাকে বিশ্বাস করো" বলে, কিন্তু রানটাইমে কোনো কনভারশন বা চেক করে না—তাই সাবধানে ব্যবহার করুন।

```ts
let value: unknown = "hello world";
let strLength = (value as string).length;
// alternative syntax (not usable in .tsx files):
let strLength2 = (<string>value).length;

// Non-null assertion — tells TS a value isn't null/undefined
function process(el: HTMLElement | null) {
  el!.classList.add("active"); // asserting el is not null
}
```

---

## 14. Mapped & Conditional Types (Advanced) — ম্যাপড ও কন্ডিশনাল টাইপ (অ্যাডভান্সড)

> **বাংলায়:** ম্যাপড টাইপ দিয়ে অন্য টাইপের প্রতিটি প্রপার্টি রূপান্তর করা যায়; কন্ডিশনাল টাইপ হলো টাইপ-লেভেলের টার্নারি (`T extends X ? A : B`)। `infer` দিয়ে ভেতরের টাইপ বের করা যায়।

```ts
// Mapped types — build a new type by transforming each property of another
type ReadonlyVersion<T> = {
  readonly [K in keyof T]: T[K];
};
type OptionalVersion<T> = {
  [K in keyof T]?: T[K];
};

interface Todo { title: string; done: boolean; }
type ReadonlyTodo = ReadonlyVersion<Todo>;

// Conditional types — types that depend on a check, like a type-level ternary
type IsString<T> = T extends string ? "yes" : "no";
type A = IsString<string>; // "yes"
type B = IsString<number>; // "no"

// infer keyword — extract a type from within another type
type ElementType<T> = T extends (infer U)[] ? U : T;
type Item = ElementType<string[]>; // string

// Template literal types — build string types from patterns
type EventName = `on${Capitalize<"click" | "hover">}`; // "onClick" | "onHover"
```

---

## 15. Modules — মডিউল (import/export ও type-only ইম্পোর্ট)

> **বাংলায়:** টিএস-এ `import`/`export` দিয়ে কোড ভাগ করা যায়। `import type` দিয়ে শুধু টাইপ ইম্পোর্ট করলে কম্পাইল টাইমে তা মুছে যায় (রানটাইম খরচ নেই)।

```ts
// math.ts
export function add(a: number, b: number): number { return a + b; }
export const PI = 3.14;
export default class Calculator { }

// app.ts
import Calculator, { add, PI } from "./math";
import * as MathUtils from "./math";

// Type-only imports (erased at compile time, no runtime cost)
import type { User } from "./types";
```

---

## 16. Working with the DOM — DOM নিয়ে কাজ (সঠিক এলিমেন্ট টাইপ)

> **বাংলায়:** টিএস ব্যবহার করে DOM এলিমেন্টকে সঠিক টাইপ (যেমন `HTMLInputElement`) হিসেবে কুয়েরি করা যায়, ফলে `.value` বা `.clientX`-এর মতো প্রপার্টি টাইপ-সেফ পাওয়া যায়।

```ts
// Query elements with correct types
const input = document.querySelector<HTMLInputElement>("#name");
if (input) {
  console.log(input.value); // .value is only on HTMLInputElement
}

const btn = document.getElementById("submit") as HTMLButtonElement;
btn.addEventListener("click", (e: MouseEvent) => {
  console.log("Clicked", e.clientX);
});
```

---

## 17. Async/Await with Types — অ্যাসিনক/অউট টাইপসহ

> **বাংলায়:** `async` ফাংশনে `Promise<ApiResponse>`-এর মতো রিটার্ন টাইপ দেওয়া যায়, আর জেনেরিক `fetchJson<T>` দিয়ে যেকোনো টাইপের রেস্পন্স টাইপ করা যায়।

```ts
interface ApiResponse {
  id: number;
  data: string;
}

async function fetchData(url: string): Promise<ApiResponse> {
  const res = await fetch(url);
  const json: ApiResponse = await res.json();
  return json;
}

// Generic async function
async function fetchJson<T>(url: string): Promise<T> {
  const res = await fetch(url);
  return res.json() as Promise<T>;
}
const user = await fetchJson<User>("/api/user");
```

---

## 18. Declaration Files (`.d.ts`) — ডিক্লারেশন ফাইল (টাইপ ডেফিনিশন)

> **বাংলায়:** টাইপ নেই এমন JS লাইব্রেরির জন্য `.d.ts` ফাইলে `declare` কিয়ার্ড দিয়ে টাইপ বলে দেওয়া হয়। জনপ্রিয় লাইব্রেরির টাইপ `@types/...` প্যাকেজে পাওয়া যায়।

When using plain JS libraries without built-in types, TypeScript relies on `.d.ts` declaration files to know their shapes.

```ts
// mylib.d.ts
declare module "mylib" {
  export function doSomething(x: number): string;
}

// Global declarations (e.g. augmenting `window`)
declare global {
  interface Window {
    myGlobalVar: string;
  }
}
```

Most popular libraries have community-maintained types via `@types/packagename` (e.g. `npm install --save-dev @types/lodash`).

---

## 19. Advanced Generics Patterns — অ্যাডভান্সড জেনেরিক প্যাটার্ন

> **বাংলায়:** ডিফল্ট জেনেরিক প্যারামিটার, অন্য জেনেরিকের ওপর কনস্ট্রেইন্ট এবং রিকার্সিভ টাইপ (যেমন `Json`)—জেনেরিকের উন্নত ব্যবহার এখানে দেখানো হয়েছে।

```ts
// Default generic parameters
interface ApiResponse<T = unknown> {
  data: T;
  status: number;
}

// Generic constraints referencing another generic
function getProp<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
const user = { name: "Alice", age: 25 };
getProp(user, "name"); // string
getProp(user, "age");  // number
// getProp(user, "email"); // Error, "email" isn't a key of user

// Recursive types
type Json = string | number | boolean | null | Json[] | { [key: string]: Json };
```

---

## 20. `keyof`, `typeof`, and Indexed Access — keyof, typeof ও ইন্ডেক্সড অ্যাক্সেস

> **বাংলায়:** `keyof` দিয়ে একটি টাইপের কীগুলোর ইউনিয়ন পাওয়া যায়, `typeof` দিয়ে ভ্যালু থেকে টাইপ বানানো যায়, আর `User["id"]` দিয়ে নির্দিষ্ট প্রপার্টির টাইপ বের করা যায়।

```ts
interface User { id: number; name: string; }

type UserKeys = keyof User;        // "id" | "name"
type IdType = User["id"];          // number

const config = { host: "localhost", port: 8080 };
type Config = typeof config;       // { host: string; port: number }
```

**Why useful:** these let you derive types *from existing values or types* instead of manually re-typing them — reducing duplication and keeping types in sync with actual code.

> **বাংলায়:** এগুলো ব্যবহার করে বিদ্যমান ভ্যালু বা টাইপ থেকে নতুন টাইপ তৈরি করা যায়—হাতে আবার টাইপ লেখার ঝামেলা কমে এবং টাইপ কোডের সাথে সিঙ্ক থাকে।

---

## 21. Strict Mode Flags Worth Knowing — জানা উচিত এমন স্ট্রিক্ট ফ্ল্যাগ

> **বাংলায়:** `tsconfig.json`-এ এই স্ট্রিক্ট ফ্ল্যাগগুলো চালু করলে কম্পাইলার আরও বেশি ভুল ধরতে পারে।

| Flag | Effect |
|---|---|
| `strict` | enables all strict checks below |
| `strictNullChecks` | `null`/`undefined` aren't assignable to other types unless explicitly allowed |
| `noImplicitAny` | errors if a type can't be inferred and defaults to `any` |
| `strictFunctionTypes` | stricter checking of function parameter types |
| `noUnusedLocals` / `noUnusedParameters` | flags unused variables/params |
| `noImplicitReturns` | every code path in a function must return a value if one path does |

---

## 22. Quick Reference: Interview Concepts — দ্রুত রেফারেন্স: ইন্টারভিউ ধারণাসমূহ

> **বাংলায়:** টাইপস্ক্রিপ্ট ইন্টারভিউতে সচরাচর জিজ্ঞাসিত ধারণাগুলো টেবিল আকারে দেওয়া হয়েছে।

| Concept | Key Point |
|---|---|
| `any` vs `unknown` | `any` disables checks; `unknown` forces narrowing first |
| `interface` vs `type` | interfaces merge & extend; types handle unions/intersections better |
| Structural typing | TS compares shape, not names — "if it looks like a duck..." |
| Generics | write reusable, type-safe code without losing type info |
| Type guard | runtime check (`typeof`, `instanceof`, `in`, custom predicate) that narrows a type |
| Utility types | `Partial`, `Pick`, `Omit`, `Record`, etc. — transform existing types |
| Declaration merging | interfaces with the same name combine into one |
| Type erasure | TS types don't exist at runtime — they're fully compiled away to plain JS |

---

## 23. Practice Tips — অনুশীলনের টিপস

> **বাংলায়:** ধাপে ধাপে JS প্রজেক্ট টিএস-এ রূপান্তর, জেনেরিক ইউটিলিটি ফাংশন লেখা এবং `Partial`/`Pick`/`Omit` নিজে বানিয়ে দক্ষতা বাড়ানোর পরামর্শ দেওয়া হয়েছে।
- Convert an existing JS project to TS file-by-file, fixing type errors as they surface.
- Practice writing generic utility functions (`pick`, `groupBy`, `debounce`) with proper types.
- Try implementing `Partial`, `Pick`, and `Omit` yourself using mapped types, before checking the built-in versions.
- Enable `"strict": true` early — it's much harder to retrofit onto a large codebase later.
- Read the official handbook for anything unclear: https://www.typescriptlang.org/docs/handbook/intro.html

---

*End of tutorial — happy revising!*
