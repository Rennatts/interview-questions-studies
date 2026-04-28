# React — Rendering model, hooks, state patterns, performance, forms, context, SSR basics

React interviews typically focus on whether you understand **how React renders**, how to use **hooks** correctly, and how to build maintainable UI with good performance. This topic is a curated set of high-signal questions you can practice as flashcards.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Prefer answers that include **a rule of thumb** and **one common pitfall**.
- When discussing performance, always mention “**measure first**” (profiling) and then optimizations.

---

## Rendering model

### 1) What does it mean for a React component to “render”?

**Short answer**: Rendering means React calls your component function/class to produce a new description of UI (React elements). The browser DOM updates happen during commit after reconciliation.

---

### 2) What triggers a re-render?

**Short answer**:
- State updates (`setState`, `useState` setter)
- Parent re-rendering (child re-renders by default)
- Context value changes (for consumers)

**Pitfall**: “Re-render” doesn’t necessarily mean the DOM changes—React may bail out during reconciliation.

---

### 3) What is reconciliation (high level)?

**Short answer**: React compares the previous and next element trees to determine the minimal set of updates to apply. Keys help React match list items across renders.

---

### 4) Why are keys important in lists?

**Short answer**: Keys help React preserve element identity across changes (insert/reorder/delete). Bad keys (like array index) can cause incorrect state association and UI glitches when items reorder.

---

## Hooks

### 1) What are the Rules of Hooks?

**Short answer**:
- Only call hooks at the top level (no conditions/loops).
- Only call hooks from React function components or custom hooks.

**Why**: React relies on call order to map hook state across renders.

---

### 2) `useEffect`: when does it run and what is it for?

**Short answer**: `useEffect` runs after the render is committed to the screen (post-paint timing can vary). It’s for side effects: subscriptions, timers, logging, network calls, syncing with external systems.

**Pitfalls**:
- Incorrect dependencies causing stale values or infinite loops.
- Doing layout measurement in `useEffect` when you need `useLayoutEffect`.

---

### 3) `useMemo` vs `useCallback`: what’s the difference?

**Short answer**:
- `useMemo` memoizes a **value** (result of a computation).
- `useCallback` memoizes a **function** reference.

**Rule of thumb**: Use them for performance only when you’ve measured and have a reason (prop identity, expensive compute).

---

### 4) What is a custom hook?

**Short answer**: A custom hook is a function that uses hooks to encapsulate reusable stateful logic. It’s primarily an abstraction tool (and sometimes a correctness tool) for reuse and separation of concerns.

---

## State patterns

### 1) Controlled vs uncontrolled components

**Short answer**:
- **Controlled**: parent owns the state (`value` + `onChange`).
- **Uncontrolled**: the DOM/component owns state, read via refs or on submit.

**Trade-off**: Controlled gives predictability and validation; uncontrolled can be simpler and reduce re-renders for large forms.

---

### 2) Lifting state up: when do you do it?

**Short answer**: Lift state when multiple components need to coordinate on the same source of truth. Keep state local when only one subtree needs it.

---

### 3) When do you reach for a reducer (`useReducer`)?

**Short answer**: When state transitions are complex (multiple sub-values, multi-step flows) and you want explicit, testable transitions.

---

## Context

### 1) What is React Context good for?

**Short answer**: Dependency-like values shared across a subtree (theme, locale, auth session, feature flags). It avoids prop drilling for truly shared concerns.

**Pitfall**: Using context for frequently changing state can cause widespread re-renders; keep context values stable or split contexts.

---

### 2) How do you prevent unnecessary re-renders with context?

**Short answer**:
- Split context by concern (don’t bundle everything into one provider value).
- Memoize provider values when needed.
- Keep frequently-changing values local or behind selector patterns where appropriate.

---

## Performance

### 1) What are the most common causes of slow React apps?

**Short answer**:
- Large component trees re-rendering too often
- Expensive renders (heavy computations in render)
- Large lists without virtualization
- Too much JS (bundle size) and third-party scripts

---

### 2) How do you profile React performance?

**Short answer**: Use React DevTools Profiler to identify components with high render time or frequent renders. Use browser Performance panel to see main-thread long tasks.

---

### 3) What are common performance techniques?

**Short answer**:
- Memoization: `React.memo`, `useMemo`, `useCallback` (when useful)
- Component splitting to localize state changes
- Virtualize large lists
- Avoid creating new objects/functions in hot paths when it matters
- Code splitting by route/feature

**Interview-friendly line**: “First measure, then optimize the biggest hotspots.”

---

## Forms

### 1) What are common pitfalls with forms in React?

**Short answer**:
- Validation and error display that isn’t accessible
- Too many re-renders on every keystroke for huge forms
- Mixing controlled and uncontrolled patterns inconsistently

---

### 2) How do you handle async form submission UX?

**Short answer**:
- Disable submit during in-flight
- Show progress/inline errors
- Handle server validation errors distinctly from client errors
- Keep focus management and announcements accessible (error summary / aria-live when needed)

---

## SSR basics

### 1) What is SSR and why do teams use it with React?

**Short answer**: Server-Side Rendering generates HTML on the server so users see content sooner and crawlers can index it more reliably. It can improve perceived performance and SEO, but adds complexity.

---

### 2) What is hydration?

**Short answer**: Hydration is when React attaches event listeners and makes the server-rendered HTML interactive on the client, reusing the existing DOM.

**Common pitfall**: Hydration mismatches when server and client render different markup (timezones, random ids, feature flags).

---

## Modern React concurrency (basics)

### 1) What changed in modern React regarding concurrency (high level)?

**Short answer**: React can render work in a more interruptible way and prioritize user interactions, which helps keep the UI responsive under load. In practice, you design for responsive updates and avoid long main-thread blocks.

**Interview note**: Concurrency doesn’t “make code parallel”; it helps React schedule rendering work more intelligently.

---

### 2) What’s a practical way to talk about priority in React updates?

**Short answer**: Some updates are urgent (typing/click feedback), others are non-urgent (filtering a large list, expensive recalculation). Prioritizing helps keep input responsive.

---

## Suspense & data fetching patterns (high level)

### 1) What is Suspense used for?

**Short answer**: Suspense coordinates showing fallback UI while something is “not ready yet” (commonly code splitting and, in some architectures, data fetching). It’s about managing loading states in a consistent way.

---

### 2) What’s a common mistake with Suspense discussions in interviews?

**Short answer**: Treating Suspense as a universal data fetching solution in all setups. In many codebases, data fetching is handled by a dedicated server-state layer; Suspense may or may not be part of that architecture.

---

## Testing React (RTL best practices)

### 1) What’s RTL’s core philosophy for React testing?

**Short answer**: Test components the way users experience them: query by role/label/text and assert on visible behavior, not implementation details.

---

### 2) What queries should you prefer?

**Short answer**: Prefer accessible queries:
- `getByRole` (with `name`)
- `getByLabelText`
- `getByText` (when appropriate)

**Pitfall**: Overusing `data-testid` when a role/label exists can hide accessibility problems.

---

### 3) How do you handle async UI in RTL without flakes?

**Short answer**:
- Wait for user-visible outcomes (`findBy*`, `waitFor`)
- Don’t use arbitrary sleeps
- Mock at the boundary (network) and keep UI real

---

## State colocation & memoization pitfalls

### 1) What is state colocation and why does it help?

**Short answer**: Keep state as close as possible to where it’s used. It reduces unnecessary re-renders and makes ownership clear.

---

### 2) What are common memoization pitfalls?

**Short answer**:
- Adding `useMemo`/`useCallback` everywhere without measuring (adds complexity and can be slower).
- Incorrect dependency arrays (stale values/bugs).
- Memoizing objects/functions but still passing unstable props from parents (no real win).

**Rule of thumb**: Memoize only when it prevents expensive work or prevents unnecessary renders in hot paths.

---

## Suggested practice exercises

- Explain (out loud) why “state updates batch” and what that implies for reading state immediately after setting it.
- Build a list UI with filters and measure re-renders; reduce unnecessary re-renders using component splitting + memoization.
- Implement a form with accessible errors (labels, `aria-describedby`, error summary) and async submit.
- Create a list with stable keys and demonstrate why index keys break when reordering.
- Write an RTL test for a component that fetches data: assert loading → success → error states using role-based queries.
- Take a component with “global” state and refactor to colocate state; measure re-renders before/after (Profiler).

## Links / references

- React docs: https://react.dev/learn
- React Hooks reference: https://react.dev/reference/react
- React DevTools Profiler: https://react.dev/learn/profile-a-react-app
- Testing Library: queries by role: https://testing-library.com/docs/queries/byrole
