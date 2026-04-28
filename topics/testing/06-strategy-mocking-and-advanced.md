# Strategy, mocking, and advanced topics

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

### 6) What is a smoke test?

**Short answer**: A smoke test is a **small set of critical checks** that answers “is the app basically working?” (e.g., app boots, routing works, a key page renders, a core API call succeeds). It’s usually run on a fresh build or right after deploy.

**Rule of thumb**: Keep it tiny and stable; it’s a gate for obvious breakages, not deep correctness.

---

### 7) What is a sanity test (and how is it different from smoke)?

**Short answer**: A sanity test is a **quick verification after a specific change** (“did we break the thing we just touched?”). Teams sometimes use “sanity” and “smoke” interchangeably, but a useful distinction is:
- **Smoke**: broad “system is alive” checks after build/deploy.
- **Sanity**: narrow checks focused on a recent fix/change.

---

### 8) What is a regression test?

**Short answer**: A regression test is a test added to ensure a **previously fixed bug never comes back**. It’s defined by **why it exists** (prevent recurrence), not by scope—regression tests can be unit, integration, or E2E.

**Best practice**: Reproduce the bug with the smallest, most reliable scope that would have caught it.

---

### 9) What is a spider test (route/page sweep)?

**Short answer**: A spider test is a broad, shallow sweep across many routes/pages to catch **crashes, broken navigation, missing chunks/assets, and obvious 4xx/5xx issues**—without asserting deep user flows.

**Good fit**: Large SPAs after routing refactors or dependency upgrades.

---

### 10) What are golden/master tests (a.k.a. “golden files”)?

**Short answer**: Golden/master tests compare output to a **known-good baseline artifact** (e.g., serialized data, generated HTML, images, PDFs). They’re great when diffs are meaningful and reviewing baseline updates is safe.

**Pitfall**: Baselines can become “rubber-stamped” if diffs are too large/noisy—keep artifacts small and readable.

---

### 11) How do you approach accessibility (a11y) testing?

**Short answer**: Combine:
- **Automated checks** (axe rules, role/name/label assertions, color contrast where feasible)
- **Keyboard flow tests** (tab order, focus traps, escape to close dialogs)
- **Targeted manual audits** for critical paths (screen reader spot-checks)

**Interview-friendly line**: “Automated a11y catches common issues; manual testing validates real usability.”

---

### 12) What does performance testing look like for front-end apps?

**Short answer**: Performance testing is often **regression-focused**: define budgets and alert on changes.

**Common signals**:
- Core Web Vitals (LCP/INP/CLS) in a controlled environment
- Bundle size / code-splitting checks
- Interaction timing (render cost, long tasks) for key screens

**Pitfall**: Measuring on unstable environments; prefer repeatable runs and compare deltas.

---

### 13) What kinds of security tests are common for front-end work?

**Short answer**: Front-end security testing often mixes:
- **Dependency scanning** (known vulnerable packages)
- **Static checks** (lint rules for unsafe patterns, secret scanning)
- **Config checks** (CSP, secure cookies, correct CORS assumptions at the edges)
- **Targeted tests** for risky areas (HTML injection/XSS sinks, URL handling, auth flows)

---

### 14) What is mutation testing and when is it worth it?

**Short answer**: Mutation testing changes code in small ways (mutants) to see whether tests fail. If tests pass, the suite may be missing meaningful assertions.

**When it’s worth it**: Critical logic (money/auth), libraries/utilities, or when you suspect coverage is “high but weak.”

---

### 15) What are property-based tests and fuzz tests?

**Short answer**:
- **Property-based**: generate many inputs and assert **invariants** (properties) always hold (e.g., encode/decode round-trip, sorting keeps order).
- **Fuzzing**: bombard code with randomized or malformed inputs to find crashes or unexpected behavior.

**Good fit**: Parsers, formatters, validators, URL handling, and edge-case-heavy utilities.

