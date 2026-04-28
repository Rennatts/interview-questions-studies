# TypeScript — Core interview topics (types, generics, narrowing, utilities)

TypeScript interviews focus on whether you can model data safely, narrow unknown values correctly, and build maintainable types for APIs and React components. This topic covers the highest-signal areas: **types vs interfaces**, **generics**, **narrowing**, **utility types**, **`unknown`/`never`**, and **typing React hooks**.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Prefer answers that include a small example and a common pitfall.
- Aim for “safe by default” types that prevent invalid states.

---

## Questions & answers

### 1) `type` vs `interface`: what’s the difference and when do you use each?

**Short answer**:
- `interface` is best for object shapes you expect to **extend/merge** (public APIs, library types).
- `type` is best for **unions**, **mapped/conditional types**, and more complex compositions.

**Notes / variations**:
- Interfaces support declaration merging; types do not.
- Both can model objects; use the one that fits your team’s conventions.

---

### 2) What is a generic, and why is it useful?

**Short answer**: A generic lets you write a function/type once while preserving type information for many input types (e.g., `Array<T>`, `Promise<T>`). It improves reusability without losing safety.

**Example**:

```ts
function first<T>(xs: T[]): T | undefined {
  return xs[0];
}
```

---

### 3) What is type narrowing and why do you need it?

**Short answer**: Narrowing is how TypeScript refines a union type based on runtime checks so you can safely access properties/methods.

**Common narrowing tools**:
- `typeof` for primitives
- `instanceof` for classes
- `in` operator for property presence
- discriminated unions (`kind: "a" | "b"`)

---

### 4) What is a discriminated union?

**Short answer**: A union of object types that share a common literal field (the discriminant). It enables safe, exhaustive branching.

**Example**:

```ts
type Result =
  | { status: "ok"; data: string }
  | { status: "error"; message: string };

function render(r: Result) {
  if (r.status === "ok") return r.data;
  return r.message;
}
```

---

### 5) What are `unknown` and `any`, and when should you use `unknown`?

**Short answer**:
- `any` disables type checking (unsafe).
- `unknown` forces you to narrow before using the value (safe).

**Rule of thumb**: Use `unknown` for external/untrusted data (API responses, `JSON.parse`) and validate/narrow it.

---

### 6) What is `never` used for?

**Short answer**: `never` represents values that can’t happen. It’s used for:
- exhaustive checks (switch over a discriminated union)
- functions that throw or never return

**Example (exhaustiveness)**:

```ts
function assertNever(x: never): never {
  throw new Error("Unexpected value: " + x);
}
```

---

### 7) Utility types: which ones should you know?

**Short answer** (high-signal):
- `Partial<T>`: make properties optional
- `Pick<T, K>` / `Omit<T, K>`: select/remove keys
- `Record<K, V>`: map keys to values
- `ReturnType<F>` / `Parameters<F>`: derive from functions
- `NonNullable<T>`: remove `null | undefined`

**Pitfall**: `Partial<T>` can hide invalid states if used too broadly; prefer targeted types when correctness matters.

---

### 8) How do you type common React patterns?

**Short answer**:
- Props: `type Props = { ... }`
- Events: `React.ChangeEvent<HTMLInputElement>` etc.
- Children: `React.ReactNode`
- Refs: `useRef<HTMLDivElement | null>(null)`

---

### 9) Typing React hooks: what are common pitfalls?

**Short answer**:
- `useState([])` infers `never[]` sometimes; provide a generic: `useState<Item[]>([])`.
- `useRef(null)` loses element type; use `useRef<HTMLDivElement | null>(null)`.
- Be careful with optional values and non-null assertions (`!`)—prefer narrowing.

---

### 10) How do you type an async data-fetching hook?

**Short answer**: Use a discriminated union for loading/error/success states so the UI narrows safely.

**Example**:

```ts
type Loadable<T> =
  | { status: "idle" | "loading" }
  | { status: "error"; error: Error }
  | { status: "success"; data: T };
```

---

## Suggested practice exercises

- Model an API response with a discriminated union and make the UI branch exhaustively.
- Write a generic `groupBy<T, K extends string | number>(items, keyFn)` helper.
- Replace `any` in a module with `unknown` + type guards.
- Type a custom React hook that returns `{ data, error, isLoading }` with safe narrowing.

## Links / references

- TypeScript Handbook: https://www.typescriptlang.org/docs/handbook/intro.html
- TS narrowing: https://www.typescriptlang.org/docs/handbook/2/narrowing.html
- React + TypeScript (React docs): https://react.dev/learn/typescript
