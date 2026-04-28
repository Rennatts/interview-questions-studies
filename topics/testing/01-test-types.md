# Test types (practical taxonomy)

These labels overlap (teams use them differently). In interviews, anchor them by **goal** (fast feedback vs realistic confidence) and **scope** (unit → slice → full system).

- **Unit**: smallest scope; pure logic; fastest feedback.
- **Integration**: multiple modules together (UI + router + state, UI + mocked network); high signal for front-end apps.
- **E2E**: full user journey in a real browser; keep small and high-value.
- **Smoke test**: a tiny set of critical checks to answer “is the app basically working?” (often run after deploy).
- **Sanity test**: quick check after a small change/fix (“did we break the thing we just touched?”); sometimes used interchangeably with smoke.
- **Regression test**: added specifically to prevent a bug from coming back (can be unit/integration/E2E).
- **Spider test**: broad, shallow sweep across many routes/pages to catch crashes and broken navigation.
- **Snapshot test**: capture output/DOM and diff on change; use sparingly to avoid noisy diffs.
- **Golden/master test**: compare outputs against a “known good” baseline (images, PDFs, ASTs); great when diffing is meaningful.
- **Contract test**: verify consumer ↔ provider expectations (schema, error shape, status codes) without full E2E.
- **Visual regression test**: screenshot diffs to catch unintended UI changes (stabilize fonts/animations/data).
- **Accessibility (a11y) test**: automated checks (axe) + keyboard flows; complements manual audits.
- **Performance test**: measure budgets (LCP/INP, bundle size, render time) in CI; catch regressions early.
- **Security test**: SAST/dependency scanning + targeted checks (e.g., CSP, XSS sinks) depending on the app.
- **Mutation test**: deliberately mutate code to see if tests fail; gauges test suite effectiveness (slower, use selectively).
- **Property-based / fuzz test**: generate many randomized inputs to discover edge cases (great for parsers/formatters/validators).

## Spider testing (aka “spider test”)

A **spider test** is a lightweight, automated sweep that “crawls” key pages/routes (often via the router sitemap or a curated list) to catch **broken navigation, crashes, missing assets, and obvious 4xx/5xx errors**. It’s usually broader than a smoke test (covers many routes) but shallower than full E2E (doesn’t deeply validate complex interactions).

### When it’s useful

- Catch “app is broken” regressions quickly (runtime errors, route misconfig, missing chunks)
- Validate **links** and top-level navigation after refactors
- Run in CI as a fast-ish confidence layer (especially for large SPAs)

### What it typically asserts (examples)

- Every route renders without uncaught exceptions
- Critical elements exist (e.g., page heading, nav, auth gate behavior)
- Network calls don’t return unexpected 5xx (or the UI handles them with a stable fallback)
- No console errors of certain classes (optional; be careful to avoid noisy checks)

### Spider test vs smoke test (rule of thumb)

- **Smoke test**: very small set of **critical flows** (e.g., login → dashboard).
- **Spider test**: broader set of **routes/pages** with shallow assertions (“does it load and not crash?”).

### Minimal “test plan” example

- Visit: `/`, `/login`, `/settings`, `/help`, `/account`
- For each page:
  - assert main heading is visible
  - assert nav renders
  - assert no uncaught errors

