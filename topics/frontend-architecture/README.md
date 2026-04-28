# Front-End Architecture — Folder structure patterns & Component-driven architecture

Front-end architecture interviews often test whether you can scale a codebase: clear boundaries, low coupling, consistent patterns, and a structure that supports teams shipping features safely.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Prefer answers that mention **trade-offs** (team size, product complexity, deployment constraints).
- Add your own notes under **Notes / variations**.

---

## Folder structure patterns

### 1) What problem does folder structure solve?

**Short answer**: It makes ownership and boundaries obvious: where code lives, how it’s reused, and how to avoid “grab bag” folders that turn into spaghetti.

**Signals of a good structure**:
- New engineers can predict where code should go.
- Refactors don’t require touching many unrelated folders.
- Boundaries reduce accidental imports and circular dependencies.

---

### 2) Layer-based vs feature-based: what’s the difference?

**Short answer**:
- **Layer-based**: group by technical type (e.g., `components/`, `hooks/`, `utils/`, `services/`).
- **Feature-based**: group by product feature/domain (e.g., `features/billing/`, `features/auth/`), often with local components/hooks inside.

**Rule of thumb**: Feature-based usually scales better for product teams because it keeps related code co-located.

---

### 3) What’s a practical “hybrid” structure that scales?

**Short answer**: Feature-based for most product code, plus a small, curated `shared/` (or `ui/`, `lib/`) for reusable primitives.

**Example**:

```text
src/
  app/                # app shell, routing, providers
  features/
    auth/
      components/
      api/
      hooks/
      routes/
    billing/
      components/
      api/
      hooks/
  shared/
    ui/               # design-system-ish primitives
    lib/              # small utilities (date, currency, fetch wrapper)
    config/
```

---

### 4) How do you prevent “shared/” from becoming a dumping ground?

**Short answer**:
- Define what belongs in shared (UI primitives, truly cross-cutting utilities).
- Prefer duplication over premature abstraction for feature-specific logic.
- Use explicit boundaries (lint rules, import conventions, codeowners).

**Interview-friendly line**: “Shared is for stable primitives, not half-finished feature code.”

---

### 5) Where should tests and styles live?

**Short answer**: Co-locate them near the code they test/style.

**Common patterns**:
- `Component.test.tsx` next to `Component.tsx`
- `Component.module.css` next to `Component.tsx` (or `styles.ts` for CSS-in-JS)
- `__tests__/` only when a framework requires it; otherwise, co-location improves discoverability.

---

### 6) What are “barrel files” (`index.ts`) and what are the trade-offs?

**Short answer**: Barrel files re-export modules to simplify imports.

**Pros**:
- Cleaner imports
- Can define a public API for a folder

**Cons**:
- Can hurt tree-shaking or bundle clarity if misused
- Can increase accidental coupling (“easy to import anything from anywhere”)

---

## Component-driven architecture

### 1) What is component-driven architecture (CDA)?

**Short answer**: Build the UI as a system of reusable components with clear contracts (props, states, accessibility), often developed and tested in isolation (e.g., Storybook-style workflows).

---

### 2) What does a good component API include (beyond props)?

**Short answer**:
- **Accessibility defaults** (labels, roles, keyboard behavior, focus management)
- **Styling/extensibility** strategy (variants, slots, className/style escape hatch)
- **State model** (controlled vs uncontrolled, default values)
- **Composability** (can it be used in different layouts without hacks?)

---

### 3) Presentational vs container components: is this still useful?

**Short answer**: The idea still helps: separate **UI rendering** from **data/side effects**, but modern patterns often do this via hooks + composition rather than strict folder splits.

**Example approach**:
- Feature hook handles data fetching/state.
- UI component receives data and callbacks.

---

### 4) How do design tokens fit into component-driven architecture?

**Short answer**: Tokens (CSS variables / Tailwind config / theme objects) make components consistent and themeable without rewriting styles. Components should consume tokens, not hard-coded colors/spacings.

---

### 5) How do you keep components from becoming overly generic?

**Short answer**:
- Start specific, then abstract only after multiple real use cases.
- Prefer “primitive + composition” over “mega-component with 30 props”.
- Keep feature-specific components inside the feature.

---

## Design systems

### 1) What is a design system (and how is it different from a UI kit)?

**Short answer**: A design system is the combination of **design tokens**, **components**, **guidelines**, and **governance** that ensures a consistent product UI. A UI kit is often just the component library; the design system includes standards and decisions (a11y, content, motion, usage rules).

---

### 2) What are design tokens and why are they important?

**Short answer**: Tokens are the canonical values for UI decisions (colors, spacing, typography, radii, shadows, z-index). They make theming and consistency possible across platforms.

**Common representation**:
- CSS variables (`--color-bg`, `--space-2`)
- Tailwind config / theme object mapping to the same underlying values

---

### 3) What should a “good” component in a design system provide?

**Short answer**:
- Accessibility by default (keyboard support, correct semantics, visible focus, ARIA when needed)
- Variant API (size, intent, state) with sane defaults
- Composability (slots/children patterns without hacks)
- Theming support via tokens (not hard-coded styles)
- Documentation and examples (do/don’t usage)

---

### 4) Controlled vs uncontrolled components: how does this show up in design systems?

**Short answer**: A component should support the right ownership model:
- **Uncontrolled** for simple forms/UX (internal state)
- **Controlled** when app state must drive it (React-style state)

**Interview-friendly line**: “Offer both when it’s valuable, but keep APIs minimal and consistent.”

---

### 5) How do you roll out a design system without blocking product teams?

**Short answer**:
- Start with the highest leverage primitives (Button, Input, Modal, Typography, Spacing)
- Provide codemods / migration guides for adoption
- Make the “right thing” easy (defaults, docs, lint rules)
- Measure adoption and pain points (support channel, feedback loop)

---

### 6) Versioning and breaking changes: how do teams manage it?

**Short answer**:
- Use semantic versioning for the component package
- Deprecate before removing; provide migration paths
- Keep changes backward compatible when possible (additive APIs)

**Common pitfall**: One-off product overrides that diverge from the system—prefer extending tokens/variants instead.

---

## Micro-frontends

### 1) What are micro-frontends?

**Short answer**: Micro-frontends split a front-end into independently developed and deployed “slices” (often by team/domain), then compose them into one user experience.

**Why teams consider them**:
- Team autonomy and parallel delivery
- Independent deploys for large organizations
- Clearer ownership boundaries aligned to domains

---

### 2) What are common micro-frontend integration approaches?

**Short answer** (typical options):
- **Build-time integration**: packages/libraries imported at build (simple, less runtime complexity).
- **Runtime integration**: load remote bundles at runtime (e.g., module federation-style).
- **iFrame integration**: strong isolation, heavier UX trade-offs.
- **Routing composition**: shell app owns routing, delegates routes to feature apps.

**Trade-off lens**: isolation vs performance vs DX vs operational complexity.

---

### 3) What are the biggest trade-offs / failure modes?

**Short answer**:
- Performance overhead (multiple bundles, duplicated dependencies, runtime loading waterfalls)
- Inconsistent UX (design drift) without a shared design system/tokens
- Complex local dev and debugging
- Cross-app state, auth, and routing coordination
- Observability becomes harder (tracing across app boundaries)

---

### 4) How do you avoid duplicated React / dependency conflicts?

**Short answer**: Define shared/singleton dependencies (React, ReactDOM, design system) and enforce versions via tooling and CI. Without this, you can end up with multiple copies and subtle bugs (hooks/context issues).

---

### 5) How do micro-frontends share UI consistency?

**Short answer**: Use a shared **design system + tokens** and a documented set of interaction/accessibility patterns. If each micro-frontend reinvents primitives, the user experience fragments quickly.

---

### 6) When are micro-frontends *not* worth it?

**Short answer**: For small/medium teams or apps where coordination costs are low, micro-frontends often add more complexity than value. Prefer a modular monolith front-end with clear feature boundaries first.

**Interview-friendly line**: “Micro-frontends are an organizational scaling tool more than a technical one.”

---

## State management strategies

### 1) What problem is “state management” solving?

**Short answer**: Coordinating UI and data over time: **where state lives**, who can update it, how it flows through the app, and how to keep it **predictable**, **testable**, and **performant** as the app grows.

---

### 2) What are the main categories of state in front-end apps?

**Short answer**:
- **Server state**: data from an API (caching, freshness, retries, pagination).
- **Client UI state**: local UI concerns (modal open, selected tab, hover).
- **Global app state**: cross-cutting client state (auth/session, feature flags, theme).
- **Form state**: inputs + validation + submission state.
- **URL state**: route params, query string (shareable/bookmarkable).

**Interview-friendly note**: “Not all state belongs in a global store—classify it first.”

---

### 3) What’s a good default strategy for where state should live?

**Short answer**:
- Keep state **as local as possible** (component/local hook) and lift only when needed.
- Put server state in a server-state cache layer (rather than a generic global store).
- Use the URL for state that should be shareable and restorable (filters, pagination).

---

### 4) How do you manage server state vs client state?

**Short answer**:
- **Server state** needs caching, revalidation, background refetch, deduping, and mutation handling.
- **Client state** needs predictable updates and minimal re-renders.

**Practical takeaway**: Don’t force server data into a global client store unless you have a specific reason; treat it as a cache with policies.

---

### 5) What are common state management patterns/tools (conceptually)?

**Short answer**:
- Local component state + derived state
- Context for dependency injection-ish concerns (theme, i18n) and small shared state
- Reducer/state machine patterns for complex UI flows
- Global stores when state truly spans many features (with clear ownership boundaries)

**Trade-off lens**: simplicity vs consistency vs debugging vs performance.

---

### 6) What are the most common state bugs, and how do you prevent them?

**Short answer**:
- **Duplication**: two sources of truth (UI shows stale data) → pick one owner.
- **Stale derived state**: stored computed values get out of sync → derive on render when cheap.
- **Race conditions** in async flows → cancellation, request ids, or server-state tooling.
- **Over-globalization**: everything in one store → hard to reason, lots of rerenders.

---

### 7) How do you keep state updates performant?

**Short answer**:
- Minimize the surface area of updates (localize state).
- Avoid re-rendering large subtrees for small changes (split components, memoize where needed).
- Keep derived data memoized when expensive.
- Prefer normalized/shared caches for server state to avoid deep prop drilling and duplication.

---

### 8) When should you use URL state?

**Short answer**: When the state should be:
- shareable (copy/paste URL),
- restorable on refresh,
- navigable via back/forward.

**Examples**: search query, filters, pagination, selected item id.

---

## Suggested practice exercises

- Take a small app and refactor to a feature-based structure; write down the import boundaries you would enforce.
- Pick one component (Button, Modal, Tabs) and define:
  - its accessibility requirements
  - controlled/uncontrolled API
  - variant strategy (size, intent)
- Identify one “shared” utility you would *not* move to shared (and why).
- Write a token set (spacing + colors) and show how both Tailwind utilities and a CSS Module component consume the same tokens.
- Define a Button component spec: variants, states, a11y requirements, and deprecation plan for API changes.
- Draw a micro-frontend architecture: shell + 2 feature apps. Decide how to share auth, routing, and the design system, and list the top 3 risks.
- Take a “filters + pagination + sorting” page: decide what lives in URL state vs local state vs server cache, and justify your choices.

## Links / references

- Feature-Sliced Design (conceptual reference): https://feature-sliced.design/
- Storybook (component-driven workflow): https://storybook.js.org/
- WAI-ARIA Authoring Practices (for component behavior): https://www.w3.org/WAI/ARIA/apg/
