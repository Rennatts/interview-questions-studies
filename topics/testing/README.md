# Testing — Unit testing (Foundations)

Testing questions come up in interviews because they reveal how you balance **confidence**, **speed**, and **maintainability**. This section starts with **unit testing**: small, fast tests focused on a single unit of behavior.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Prefer answers that mention **trade-offs** (mocking, brittleness, coverage vs confidence).
- Add your own notes under **Notes / variations**.

---

## Questions & answers

### 1) What is a unit test?

**Short answer**: A unit test verifies a small unit of behavior (function, module, class method) in isolation, aiming to be **fast**, **deterministic**, and **easy to debug**.

---

### 2) What should you unit test (and what shouldn’t you)?

**Short answer**:
- Unit test **pure logic** and business rules (formatters, validators, reducers, selectors).
- Unit test **edge cases** (nulls, empty arrays, invalid inputs, time zones, rounding).
- Avoid unit testing trivial wrappers or third-party library behavior.

**Rule of thumb**: “If a test fails, I want it to point to a specific unit and a clear reason.”

---

### 3) What makes a good unit test?

**Short answer**:
- Clear arrange/act/assert structure
- Minimal setup; easy to read
- Deterministic (no real network/time/random)
- Tests behavior, not implementation details

---

### 4) What’s the difference between mocks, stubs, and spies?

**Short answer**:
- **Stub**: returns a predefined value (replace a dependency’s output).
- **Spy**: records how something was called (arguments, call count) while optionally calling through.
- **Mock**: a test double with expectations (often includes both behavior + call assertions).

**Notes / variations**:
- Terminology varies by tool; what matters is whether you’re asserting **outputs** or **interactions**.

---

### 5) When should you mock dependencies?

**Short answer**: Mock when the dependency is:
- slow (network, filesystem),
- nondeterministic (time, random),
- hard to set up reliably,
- or outside the unit’s responsibility.

**Pitfall**: Over-mocking can make tests brittle and tightly coupled to internal implementation.

---

### 6) How do you test time, randomness, and async code?

**Short answer**:
- Time: fake timers or inject a clock dependency.
- Randomness: seed or inject a random generator.
- Async: await promises; test success + failure paths.

**Interview-friendly line**: “Make nondeterminism injectable; avoid sleeping in tests.”

---

### 7) What is “testing behavior vs implementation details” (especially for UI)?

**Short answer**: Prefer asserting on **observable behavior** (rendered text, ARIA roles, emitted events, returned values) instead of internals (private state, exact DOM structure, class names).

**Example guidance**:
- Good: “Clicking Save calls onSave and shows success.”
- Brittle: “Internal state field `isSaving` flips to true.”

---

### 8) How do you think about unit test coverage?

**Short answer**: Coverage is a signal, not the goal. Aim for coverage on critical logic, but measure success as **reduced defects** and **confidence to refactor**.

**Common pitfalls**:
- High line coverage with low behavioral confidence
- Spending time testing code that changes often and provides little value

---

## Integration testing

Integration tests verify that multiple units work together correctly (e.g., component + router + data layer, or API layer + DB adapter). They’re usually slower than unit tests but provide higher confidence.

### 1) What is an integration test?

**Short answer**: A test that exercises interactions between multiple modules/components (not necessarily the whole app), validating that their integration behaves correctly.

**Example scope**:
- A React component that calls an API client and renders loading/success/error states.
- A service function that reads/writes to a real (or test) database and applies business rules.

---

### 2) Integration tests vs unit tests vs E2E: what’s the difference?

**Short answer**:
- **Unit**: one unit in isolation (fastest, most local failures).
- **Integration**: a slice of the system (realistic behavior, moderate speed).
- **E2E**: the full user flow through the real app stack (highest realism, slowest).

**Interview-friendly line**: “I use unit tests for logic, integration tests for seams, and E2E for critical flows.”

---

### 3) What should you integration test in front-end apps?

**Short answer**:
- Route/page behavior (routing + rendering + data fetching)
- Form submission flows (validation + request + error handling)
- Component interactions (tabs, dialogs, stateful widgets) with real DOM events
- Error boundaries / fallback UI

**Rule of thumb**: Test the “contracts” between layers (UI ↔ data ↔ domain rules).

---

### 4) How much should you mock in integration tests?

**Short answer**: Mock only what you must to keep tests deterministic and fast; keep the rest real so you test meaningful integration.

**Common boundaries**:
- Mock the network (e.g., API responses) but keep UI + state + routing real.
- Or use a real HTTP layer against a local test server for higher confidence (when feasible).

**Pitfall**: If you mock too deep, you’re basically writing unit tests with extra setup.

---

### 5) How do you avoid flaky integration tests?

**Short answer**:
- Avoid relying on real time (fake timers or controlled scheduling)
- Avoid race-y assertions (wait for observable UI states)
- Reset state between tests (DOM, storage, mocks)
- Keep test data deterministic and isolated

---

### 6) What’s a good assertion style for integration tests?

**Short answer**: Assert on **user-visible outcomes** and accessibility semantics (text, roles, labels), not internal implementation details.

**Examples**:
- “The error message appears and the submit button is enabled again.”
- “The list renders 3 items after a successful fetch.”

---

## E2E testing

End-to-end (E2E) tests validate critical user journeys through the system as a whole (browser → UI → network → backend). They’re the most realistic, but also the slowest and most expensive to maintain.

### 1) What is an E2E test?

**Short answer**: A test that exercises a real user flow in a real browser environment, verifying the app works end-to-end across multiple layers.

**Examples**:
- Sign up → verify email message → log in → land on dashboard
- Add item to cart → checkout → see confirmation

---

### 2) E2E vs integration: where do you draw the line?

**Short answer**:
- E2E tests: full stack, real navigation, real browser.
- Integration tests: a slice (often UI + mocked network), faster and more focused.

**Rule of thumb**: Use E2E for a small set of “money flows” and integration tests for most behavior.

---

### 3) What should you E2E test (high value targets)?

**Short answer**:
- Authentication flows (login/logout, session expiry)
- Checkout/payment or other core conversion flows
- Permission/role gates (can/can’t access pages)
- Regression-prone areas (complex forms, file uploads)

---

### 4) How do you keep E2E tests fast and reliable?

**Short answer**:
- Seed deterministic test data (or reset DB between runs)
- Use stable selectors (role/name, or `data-testid` / `data-*` hooks when needed)
- Avoid brittle timing (never “sleep”; wait for specific UI states)
- Mock external third parties (payments, email, maps) via test environments

---

### 5) What are common causes of flaky E2E tests?

**Short answer**:
- Race conditions (waiting for the wrong signal)
- Shared state between tests (dirty DB, cached sessions)
- Unstable selectors (text that changes, DOM structure coupling)
- Environment instability (slow CI, network issues)

**Mitigation**:
- Make tests independent; isolate test data
- Prefer assertions on user-visible state and accessible roles
- Add observability: screenshots, videos, traces on failure

---

### 6) How do you think about “test pyramid” vs “testing trophy”?

**Short answer**: Both are heuristics:
- Test pyramid: lots of unit, some integration, few E2E.
- Testing trophy (frontend): lots of integration, some unit, few E2E, plus static analysis.

**Interview framing**: “I keep E2E small and high-value because it’s costly, and I cover most behavior with integration tests.”

---

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

---

## Test pyramid vs testing trophy (with examples)

### 1) What’s the test pyramid?

**Short answer**: Many unit tests, some integration tests, few E2E tests.

**Example mapping**:
- Unit: validators, reducers, formatters
- Integration: page slice with mocked network (loading/error states)
- E2E: login + checkout money flow

---

### 2) What’s the testing trophy (frontend)?

**Short answer**: Emphasizes more integration tests (UI + real interactions), fewer unit tests, few E2E tests, plus static analysis.

**Example mapping**:
- Static: typecheck + lint
- Integration: RTL tests for component flows (form submit → server error → retry)
- E2E: 3–10 critical journeys

---

## Mocking

### 1) What is mocking (and why do we do it)?

**Short answer**: Mocking replaces real dependencies with test doubles so tests are **fast**, **deterministic**, and focused on the unit or slice you care about (no real network, time, randomness, payments, etc.).

---

### 2) What’s the difference between mocking a module vs mocking a network request?

**Short answer**:
- **Module mocking**: replace an imported function/module (often used in unit tests).
- **Network mocking**: intercept HTTP requests and return controlled responses (often best for integration tests so you keep most of the app real).

**Rule of thumb**: Prefer mocking at the **boundary** (HTTP layer) over mocking deep internals.

---

### 3) When is mocking a bad idea?

**Short answer**: When it makes the test less realistic or tightly couples it to implementation details.

**Common anti-patterns**:
- Mocking the component you’re testing “to make it easier”
- Mocking half the app so the test no longer verifies integration
- Over-asserting on call counts/arguments instead of outcomes

---

### 4) What should you mock most often in front-end tests?

**Short answer**:
- Network (API responses, errors, timeouts)
- Time (`Date.now`, timers) and randomness
- Browser APIs that are unavailable in the test env (e.g., `IntersectionObserver`)
- Third-party SDKs (analytics, payments, maps)

---

### 5) What’s a good strategy for mocking server state?

**Short answer**: Mock at the HTTP boundary with realistic responses (success, validation errors, 500s), and test the UI states (loading/success/error/retry). Keep fixtures small and representative.

---

### 6) How do you avoid flaky mocks?

**Short answer**:
- Reset mocks between tests
- Avoid shared mutable fixtures
- Make async behavior explicit (await UI changes)
- Use deterministic ids/timestamps in fixtures

---

## Testing (advanced)

### 1) What is contract testing?

**Short answer**: Contract testing verifies that two components that integrate (often **frontend ↔ API**) agree on a shared contract (request/response shapes, status codes, error formats) without needing full end-to-end tests for every case.

**Common approaches**:
- **Schema-based**: validate responses against OpenAPI/JSON Schema.
- **Consumer-driven contracts**: the consumer (frontend) defines expectations; provider verifies them in CI.

---

### 2) Visual regression testing: what is it and when is it worth it?

**Short answer**: Visual regression tests compare screenshots across changes to catch UI diffs (layout, spacing, colors). It’s most valuable for design-system components and critical UI screens where pixel-level regressions matter.

**Pitfalls**:
- Flaky diffs due to fonts/anti-aliasing/animations
- Too many snapshots with low signal

**Mitigations**:
- Disable animations, stabilize fonts, control viewport and data
- Review workflow with clear baselines and approvals

---

### 3) Test data strategies: how do you keep tests deterministic?

**Short answer**:
- Seed data in a known state (fixtures, factories) per test or per suite.
- Use unique ids per run to avoid collisions.
- Keep fixtures small and representative; avoid giant JSON blobs.
- Reset state between tests (DB reset, sandbox accounts, isolated tenants).

**Rule of thumb**: “Every test should be able to run in any order.”

---

### 4) CI flake management: what causes flakes and how do you reduce them?

**Short answer**:
- Causes: timing assumptions, shared state, slow CI machines, parallelism conflicts, network instability.
- Reduce: deterministic data, robust waits (wait for UI state), isolate tests, run in parallel safely, capture traces/screenshots on failure.

**Retry strategy**:
- Retries can reduce noise, but treat retries as a signal—fix the root cause, don’t normalize flakes.

---

### 5) When do you add more E2E vs more integration tests?

**Short answer**:
- Add **integration tests** when you want fast, stable coverage of UI + mocked backend behavior.
- Add **E2E tests** only for a small set of business-critical journeys and high-risk integrations.

---

## Suggested practice exercises

- Write unit tests for a `formatCurrency(amount, locale)` helper (edge cases: negative, rounding, invalid inputs).
- Unit test a validation function (email/password) with a table of cases.
- Unit test a reducer/state transition function for a UI flow (idle → loading → success/error).
- Refactor a function to remove a dependency on `Date.now()` by injecting a clock, then test it.
- Write an integration test for a component that:
  - renders a loading state
  - renders data after a mocked API response
  - renders an error message on failure
- Write an integration test for a form submit flow (client validation + server error response + retry).
- Write an E2E test for a login flow and make it non-flaky (seed user, stable selectors, no sleeps).
- Add failure diagnostics: screenshot/trace on failure and a clean retry strategy.
- Pick one component and write:
  - a Jest unit test for its pure helpers
  - an RTL integration test for its user-visible behavior
  - a Cypress E2E test for the full flow that uses it
- Write one test that over-mocks (brittle), then rewrite it to mock at the HTTP boundary and assert on user-visible behavior.
- Add a contract test for an API response shape (schema validation) and show how it fails fast when the backend changes.
- Add a visual regression test for a Button component (states + variants) and stabilize it (disable animations, stable data).
- Take one flaky CI test and fix it (remove sleeps, isolate data, add trace artifacts).

## Links / references

- Testing Library guiding principle: https://testing-library.com/docs/guiding-principles
- Jest docs: https://jestjs.io/docs/getting-started
- Testing Library: queries by role: https://testing-library.com/docs/queries/byrole
- Playwright docs: https://playwright.dev/docs/intro
- Cypress docs: https://docs.cypress.io/
- Pact (contract testing): https://pact.io/
- Storybook test runner (component testing): https://storybook.js.org/docs/writing-tests/test-runner
- Storybook interaction testing: https://storybook.js.org/docs/writing-tests/interaction-testing
