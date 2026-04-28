# Integration testing

Integration tests verify that multiple units work together correctly (e.g., component + router + data layer, or API layer + DB adapter). They’re usually slower than unit tests but provide higher confidence.

## Questions & answers

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

