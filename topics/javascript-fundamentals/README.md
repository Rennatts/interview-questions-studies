# JavaScript (Core) — Questions & Answers

JavaScript core knowledge is a frequent interview focus because it affects **correctness**, **performance**, and **debugging** in real front-end work. This section prioritizes questions that come up in phone screens and onsite loops.

## How to use this file

- Treat each question as a **flashcard**: read the question, try answering out loud, then compare.
- Add your own notes under **Notes / variations**.
- For anything you miss, add a small practice snippet in `exercises/`.

---

## Questions & answers

### 1) What are the JavaScript primitive types?

**Short answer**: `string`, `number`, `bigint`, `boolean`, `undefined`, `symbol`, and `null` (plus `object` for non-primitives like arrays/functions).

**Details**:
- Primitives are **immutable** values (though variables holding them can be reassigned).
- `typeof null` is `"object"` for historical reasons.

**Example**:

```js
typeof "hi";      // "string"
typeof 1;         // "number"
typeof 1n;        // "bigint"
typeof true;      // "boolean"
typeof undefined; // "undefined"
typeof Symbol();  // "symbol"
typeof null;      // "object" (quirk)
```

**Notes / variations**:
- `function` is not a type; it’s an `object` with callable behavior (`typeof fn === "function"` is a special case).

---

### 2) Explain `var`, `let`, and `const`.

**Short answer**: `var` is function-scoped and hoisted (initialized to `undefined`), while `let`/`const` are block-scoped and hoisted but in the TDZ; `const` prevents reassignment, not mutation.

**Details**:
- **Scope**: `var` → function scope; `let`/`const` → block scope.
- **Hoisting**: all declarations are hoisted, but:
  - `var` is usable before declaration (value `undefined`)
  - `let`/`const` throw if accessed before initialization (**Temporal Dead Zone**)
- **`const`**: the binding is constant; object contents may still change.

**Example**:

```js
console.log(x); // undefined
var x = 1;

// console.log(y); // ReferenceError (TDZ)
let y = 2;

const obj = { a: 1 };
obj.a = 2; // ok (mutation)
// obj = {}; // TypeError (reassignment)
```

---

### 3) What is hoisting?

**Short answer**: JavaScript moves declarations to the top of their scope at compile time; the behavior differs by declaration kind.

**Details**:
- **Function declarations** are fully hoisted (callable before they appear).
- **`var`** declarations are hoisted and initialized to `undefined`.
- **`let`/`const`** are hoisted but uninitialized until their line executes (TDZ).

**Example**:

```js
sayHi(); // "hi"
function sayHi() { console.log("hi"); }

console.log(a); // undefined
var a = 1;
```

---

### 4) What is a closure, and why is it useful?

**Short answer**: A closure is when a function “remembers” variables from its lexical scope even after that outer function has finished.

**Details**:
- Useful for **encapsulation** (private state), callbacks, memoization, function factories.
- Common in UI event handlers and async code.

**Example**:

```js
function makeCounter() {
  let count = 0;
  return () => ++count;
}
const next = makeCounter();
next(); // 1
next(); // 2
```

---

### 5) Explain lexical scope.

**Short answer**: Variable access is determined by where code is written (the nested function structure), not by where it’s called.

**Example**:

```js
const x = "global";
function outer() {
  const x = "outer";
  function inner() {
    return x; // "outer"
  }
  return inner();
}
outer();
```

---

### 6) What is `this` in JavaScript?

**Short answer**: `this` depends on how a function is called; arrow functions don’t have their own `this` and capture it lexically.

**Common rules**:
- **Method call**: `obj.fn()` → `this` is `obj`
- **Plain call**: `fn()` → `this` is `undefined` in strict mode (or global object in sloppy mode)
- **Constructor call**: `new Fn()` → `this` is the new instance
- **`call/apply/bind`**: explicitly set `this`
- **Arrow function**: `this` is captured from the surrounding scope

**Example**:

```js
const o = {
  x: 1,
  f() { return this.x; },
  g: () => this?.x, // lexical this (often undefined in modules)
};
o.f(); // 1
o.g(); // usually undefined
```

---

### 7) What’s the difference between `==` and `===`?

**Short answer**: `===` is strict equality (no coercion). `==` allows type coercion and has tricky rules; prefer `===` in most code.

**Examples**:

```js
0 == false;   // true (coercion)
0 === false;  // false
"1" == 1;     // true
"1" === 1;    // false
null == undefined;  // true (special case)
null === undefined; // false
```

---

### 8) What is type coercion? Give an example where it surprises people.

**Short answer**: Coercion is JavaScript converting values between types (often implicitly) for operators like `+`, comparisons, and `==`.

**Common surprises**:

```js
[] + [];      // ""   (string concatenation after coercion)
[] + {};      // "[object Object]"
{} + [];      // 0 or "[object Object]" depending on parsing context
"5" - 2;      // 3 (numeric coercion)
"5" + 2;      // "52" (string concatenation)
```

**Note**: Interviewers care that you can reason about coercion, not memorize every edge case.

---

### 9) Explain pass-by-value vs pass-by-reference in JavaScript.

**Short answer**: JavaScript is **pass-by-value**. For objects, the value being copied is the **reference** (pointer) to the object.

**Example**:

```js
function inc(n) { n++; }
let x = 1;
inc(x);
x; // 1

function addProp(o) { o.a = 1; }
const obj = {};
addProp(obj);
obj.a; // 1
```

---

### 10) What is the prototype chain?

**Short answer**: Objects can inherit properties from another object via `[[Prototype]]` (exposed by `Object.getPrototypeOf`). Property lookup walks up the chain until it finds a match or reaches `null`.

**Example**:

```js
const parent = { kind: "parent" };
const child = Object.create(parent);
child.kind; // "parent" (inherited)
Object.getPrototypeOf(child) === parent; // true
```

---

### 11) What does `new` do?

**Short answer**: `new Fn()` creates a new object, sets its prototype to `Fn.prototype`, binds `this` inside `Fn` to that object, then returns the object (unless `Fn` returns a different object).

**Example**:

```js
function Person(name) {
  this.name = name;
}
Person.prototype.say = function () { return `hi ${this.name}`; };

const p = new Person("Renata");
p.say(); // "hi Renata"
```

---

### 12) What is the event loop (microtasks vs macrotasks)?

**Short answer**: The event loop runs tasks from queues. Promise reactions (`then/catch/finally`) run as **microtasks**, which generally run before the next **macrotask** (like `setTimeout`).

**Typical ordering**:
- Current call stack
- Microtasks (Promises)
- Next macrotask (timers, I/O callbacks, etc.)

**Example**:

```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
// A D C B
```

---

### 13) What is a Promise, and what states can it be in?

**Short answer**: A Promise represents a future value and can be **pending**, **fulfilled**, or **rejected**. Once settled, it stays settled.

**Notes / variations**:
- `async` functions return Promises.
- A common pitfall is forgetting to return the Promise chain.

---

### 14) `async/await` vs `.then()`: what’s the difference?

**Short answer**: They’re different syntax on top of Promises. `async/await` makes async code read like synchronous code; `.then()` is explicit chaining.

**Key points**:
- `await` only works inside an `async` function (or top-level in some environments).
- `try/catch` works naturally with `await`.
- Parallelism still requires explicit structure (e.g., `Promise.all`).

**Example**:

```js
// sequential
const a = await fetchA();
const b = await fetchB();

// parallel
const [a2, b2] = await Promise.all([fetchA(), fetchB()]);
```

---

### 15) What’s the difference between `null` and `undefined`?

**Short answer**: `undefined` usually means “not provided / not initialized”; `null` is an explicit “no value”.

**Interview-friendly guidance**:
- Use `undefined` for “missing”.
- Use `null` when you intentionally want an explicit empty value (e.g., resetting).

---

### 16) What are the differences between `map`, `forEach`, `filter`, and `reduce`?

**Short answer**:
- `map` transforms each element → new array
- `filter` keeps some elements → new array
- `reduce` folds to a single value (or structure)
- `forEach` is for side effects; returns `undefined`

**Example**:

```js
const xs = [1, 2, 3];
xs.map(x => x * 2);        // [2, 4, 6]
xs.filter(x => x % 2);     // [1, 3]
xs.reduce((sum, x) => sum + x, 0); // 6
```

---

### 17) What are `Map` and `Set`, and when would you use them?

**Short answer**:
- `Map`: key/value store with any key type; predictable iteration order; better for frequent inserts/lookups than plain objects in some cases.
- `Set`: unique values; good for membership tests and deduping.

**Examples**:

```js
const seen = new Set([1, 1, 2]);
seen.has(2); // true

const m = new Map();
m.set({ id: 1 }, "value"); // object keys are ok
```

---

### 18) What is debouncing vs throttling?

**Short answer**:
- **Debounce**: run after events stop firing (e.g., search input)
- **Throttle**: run at most once per interval (e.g., scroll handler)

**Notes / variations**:
- Interviews often ask you to implement one of them.

---

### 19) How do you handle errors in async code?

**Short answer**: Use `try/catch` with `await`, or `.catch()` in Promise chains; ensure errors propagate and are not silently swallowed.

**Example**:

```js
async function load() {
  try {
    const res = await fetch("/api");
    if (!res.ok) throw new Error("Request failed");
    return await res.json();
  } catch (e) {
    // report / show fallback UI
    throw e; // or return a safe value, depending on strategy
  }
}
```

---

### 20) What are common sources of memory leaks in front-end JavaScript?

**Short answer**: Unreleased references that prevent GC, commonly from long-lived event listeners, timers, caches, or closures retaining large objects.

**Common examples**:
- Adding event listeners and never removing them (especially on global objects)
- `setInterval` not cleared
- Caches without eviction
- Detached DOM nodes still referenced in JS

---

## Suggested practice exercises

- Implement **debounce** and **throttle** (with cancel/flush).
- Write a function that deep-freezes an object (discuss limitations).
- Re-implement `Promise.all` (basic behavior + rejection semantics).
- Explain the output of 10 short snippets involving hoisting/closures/`this`.
- Write a `once(fn)` helper that only allows a function to run once.
- Implement a tiny event emitter with `on/off/emit`.

## Links / references

- MDN JavaScript Guide: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide
- MDN `this`: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this
- MDN Promises: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
- You Don’t Know JS (book series): https://github.com/getify/You-Dont-Know-JS

## JavaScript fundamentals

This topic covers the core JavaScript concepts that show up constantly in front-end interviews.

### What you should be able to do

- **Explain** how JavaScript executes code (call stack, event loop, tasks/microtasks)
- **Reason** about scope, closures, and `this`
- **Predict** behavior for coercion, equality, and common edge cases
- **Use** functions fluently (parameters, defaults, rest/spread, callbacks)
- **Work** with arrays/objects immutably and safely
- **Understand** prototypes, classes, and inheritance basics

### How to study this topic

- Read `questions.md` top-to-bottom once.
- Then do exercises in `exercises/` without looking at solutions.
- Run code samples in `code/` and tweak them until you can predict outputs.

### Files in this topic

- `questions.md`
- `exercises/`
- `code/`

