# End-to-end (E2E) testing

End-to-end (E2E) tests validate critical user journeys through the system as a whole (browser → UI → network → backend). They’re the most realistic, but also the slowest and most expensive to maintain.

## Questions & answers

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

