# TypeScript Full Tutorial — Basic to Advanced (with Explanations)

TypeScript is JavaScript with a **type system** bolted on top. It compiles ("transpiles") down to plain JavaScript, so anything that runs in JS can run TS after compilation. The whole point is to catch mistakes *while you write code* instead of when it crashes at runtime.

---

## 1. Setup

```bash
npm install -g typescript
tsc --init          # creates tsconfig.json
tsc file.ts          # compiles file.ts -> file.js
tsc --watch          # auto-recompile on save
```

`tsconfig.json` controls how strict and how modern the compiler behaves. Turning on `"strict": true` is highly recommended — it enables a bundle of safety checks (no implicit `any`, strict null checks, etc.).

---

## 2. Basic Types

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

---

## 3. Type Inference & Annotations

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

---

## 4. Interfaces & Type Aliases

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

---

## 5. Functions

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

## 6. Union & Intersection Types

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

## 7. Literal Types & Enums

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

## 8. Arrays, Objects & Destructuring

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

## 9. Classes

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

---

## 10. Generics

Generics let you write reusable code that works with multiple types while still preserving type safety — instead of using `any` (which loses type info) or writing duplicate functions per type.

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

## 11. Type Narrowing & Guards

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

## 12. Utility Types

TypeScript ships built-in helper types that transform other types — extremely common in real-world code.

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

## 13. Type Assertions

Sometimes you know more about a value's type than TypeScript does. Assertions tell the compiler "trust me" — but they don't perform any runtime conversion or checking, so use them carefully.

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

## 14. Mapped & Conditional Types (Advanced)

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

## 15. Modules

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

## 16. Working with the DOM

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

## 17. Async/Await with Types

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

## 18. Declaration Files (`.d.ts`)

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

## 19. Advanced Generics Patterns

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

## 20. `keyof`, `typeof`, and Indexed Access

```ts
interface User { id: number; name: string; }

type UserKeys = keyof User;        // "id" | "name"
type IdType = User["id"];          // number

const config = { host: "localhost", port: 8080 };
type Config = typeof config;       // { host: string; port: number }
```

**Why useful:** these let you derive types *from existing values or types* instead of manually re-typing them — reducing duplication and keeping types in sync with actual code.

---

## 21. Strict Mode Flags Worth Knowing

| Flag | Effect |
|---|---|
| `strict` | enables all strict checks below |
| `strictNullChecks` | `null`/`undefined` aren't assignable to other types unless explicitly allowed |
| `noImplicitAny` | errors if a type can't be inferred and defaults to `any` |
| `strictFunctionTypes` | stricter checking of function parameter types |
| `noUnusedLocals` / `noUnusedParameters` | flags unused variables/params |
| `noImplicitReturns` | every code path in a function must return a value if one path does |

---

## 22. Quick Reference: Interview Concepts

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

## 23. Practice Tips
- Convert an existing JS project to TS file-by-file, fixing type errors as they surface.
- Practice writing generic utility functions (`pick`, `groupBy`, `debounce`) with proper types.
- Try implementing `Partial`, `Pick`, and `Omit` yourself using mapped types, before checking the built-in versions.
- Enable `"strict": true` early — it's much harder to retrofit onto a large codebase later.
- Read the official handbook for anything unclear: https://www.typescriptlang.org/docs/handbook/intro.html

---

*End of tutorial — happy revising!*
