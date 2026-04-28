# Testing tools + component testing

## Testing tools (Jest, RTL, Cypress)

### 1) What is Jest and what is it used for?

**Short answer**: Jest is a JavaScript test runner/assertion framework commonly used for **unit** and **integration** tests. It provides test execution, assertions, mocks/spies, and snapshot capabilities.

**When it’s a good fit**:
- Pure logic tests (utils, reducers)
- Component tests when paired with a DOM environment (often via RTL)

---

### 2) What is React Testing Library (RTL) and what’s its philosophy?

**Short answer**: RTL is a library for testing UI by interacting with it like a user would (query by role/text/label), encouraging tests that assert **behavior** rather than implementation details.

**Common best practices**:
- Query by accessible role/name when possible (`getByRole`)
- Prefer user interactions (click/type) over calling component methods
- Avoid brittle selectors and deep DOM coupling

---

### 3) What is Cypress and what is it used for?

**Short answer**: Cypress is a browser-based testing tool primarily used for **E2E** (and sometimes component testing). It runs tests against a running app and is strong for debugging with an interactive runner.

**When it’s a good fit**:
- E2E flows that require real browser behavior
- Visual debugging of flaky interactions and timing issues

---

### 4) How do you choose between Jest+RTL vs Cypress?

**Short answer**:
- **Jest + RTL**: fast, focused tests for UI behavior with mocked network; great for most integration coverage.
- **Cypress**: higher realism with a running app; best for a smaller set of critical E2E flows.

**Interview-friendly line**: “Most coverage in Jest+RTL, a small number of Cypress E2E tests for critical journeys.”

---

### 5) What are common pitfalls with these tools?

**Short answer**:
- Jest: over-mocking, snapshots that become noise, relying on implementation details.
- RTL: testing structure instead of behavior; not waiting for async UI changes.
- Cypress: flakiness from timing assumptions; shared state between tests; over-reliance on unstable selectors.

---

## Component testing (Storybook strategy)

### 1) What is component testing, and how does Storybook help?

**Short answer**: Component testing verifies UI components in isolation with realistic rendering and interactions. Storybook helps by providing a dedicated environment for states/variants and can be used as a foundation for automated tests across those states.

**Good fit**:
- Design system components (Button, Input, Modal, Tabs)
- Visual + interaction coverage across variants/states

---

### 2) What’s a practical component testing strategy?

**Short answer**:
- Define stories for all meaningful states (loading, error, empty, disabled, variants).
- Run automated checks per story (a11y audit, interaction tests, visual diffs).
- Keep component tests fast; rely on integration/E2E for cross-component flows.

---

## Snapshot testing (guidance)

### 1) When are snapshots useful, and when do they become noise?

**Short answer**:
- Useful for stable outputs (small pure components, config objects) where diffs are readable.
- Noise when snapshots are huge or change often; reviewers rubber-stamp updates without understanding.

**Rule of thumb**: Prefer targeted assertions (roles/text/behavior). Use snapshots sparingly and keep them small.

---

## Playwright vs Cypress (comparison)

### 1) How do you compare Playwright and Cypress?

**Short answer**:
- Both are excellent for E2E; choice often comes down to team preference and ecosystem.
- Playwright is strong for cross-browser automation and traces.
- Cypress is strong with an interactive runner and debugging ergonomics.

**Interview framing**: “Pick one, standardize patterns, and focus on reliability: deterministic data, stable selectors, no sleeps.”

