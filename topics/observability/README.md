# Observability (Front-End) — error reporting, logging, tracing, RUM, feature flags, rollouts

Front-end observability is how you detect, understand, and fix issues in production: errors, slow interactions, broken flows, and regressions. Interviews often look for practical thinking: what you measure, how you triage, and how you roll out changes safely.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Always connect observability to an action: **detect → diagnose → fix → prevent**.
- Be explicit about privacy: avoid collecting sensitive data.

---

## Error reporting

### 1) What is error reporting and what problems does it solve?

**Short answer**: Error reporting captures runtime crashes/exceptions in production (stack traces, environment, breadcrumbs) so you can debug issues you can’t reproduce locally.

**Key outputs**:
- Stack traces with sourcemaps
- Frequency, affected users, and “new vs known”
- Context (route, browser, release version)

---

### 2) What context should you attach to front-end errors?

**Short answer**:
- Release/build version (commit SHA)
- Route + key UI action (what user was doing)
- Browser/OS/device, viewport
- Network status (offline/slow), API correlation/request id

**Privacy rule**: attach identifiers safely (hashed user id), don’t attach PII or tokens.

---

## Logging

### 1) What’s the difference between logging and error reporting?

**Short answer**: Logs are structured events you choose to emit (“user clicked Save”, “API returned 500”), while error reporting captures unexpected failures (exceptions). Logs are proactive signals; errors are reactive signals.

---

### 2) What does “structured logging” mean?

**Short answer**: Logs are emitted as structured fields (JSON-like) so you can query and aggregate them reliably (e.g., `event=checkout_failed`, `reason=payment_declined`, `release=...`).

---

## Tracing (distributed tracing)

### 1) What is tracing, and why does it matter for front-end apps?

**Short answer**: Tracing connects a user action to backend work by propagating a **correlation id / trace id** across services. It helps answer “this click was slow—where was the time spent?”

**Front-end role**:
- Create/propagate request ids
- Capture timing for key spans (navigation, data fetch, render)

---

### 2) What is a correlation/request id?

**Short answer**: A unique id attached to an API call and included in logs on both client and server so you can follow a single request through the system.

---

## RUM (Real User Monitoring)

### 1) What is RUM and what do you measure?

**Short answer**: RUM collects performance and reliability data from real users in production.

**Common signals**:
- Core Web Vitals: **LCP**, **CLS**, **INP**
- JS errors, unhandled rejections
- Route changes and resource timings
- Long tasks and interaction timings

**Trade-off**: sampling and privacy are essential.

---

### 2) Lab vs field performance data: why do both matter?

**Short answer**:
- Lab: controlled and great for debugging and profiling.
- Field: real-world and great for prioritization and regression detection.

---

## Feature flags

### 1) What are feature flags and why do teams use them?

**Short answer**: Feature flags let you ship code behind a switch so you can enable it gradually, test in production safely, and roll back without redeploying.

**Common uses**:
- Gradual rollouts and canaries
- A/B testing
- Kill switch for risky features

---

### 2) What are common feature-flag pitfalls?

**Short answer**:
- Permanent flags that never get removed (tech debt)
- Flag explosion (hard to reason about combinations)
- Different code paths causing inconsistent UX or data contracts

**Mitigation**:
- Expiration dates + removal process
- Keep flags scoped and documented

---

## Rollout strategies

### 1) What rollout strategies should you mention in interviews?

**Short answer**:
- **Canary**: small % of users first
- **Gradual ramp**: 1% → 5% → 25% → 50% → 100%
- **Kill switch**: instant disable on incident
- **Shadow/dual run**: run new logic in parallel (read-only) to compare

---

### 2) What metrics do you watch during a rollout?

**Short answer**:
- Error rate / crash-free sessions
- Core Web Vitals (INP/LCP/CLS) and long tasks
- Conversion/critical funnel metrics
- API error rates and latency (p95/p99)

**Rule of thumb**: define rollback criteria before you start ramping.

---

## SLOs / SLIs (front-end)

### 1) What are SLIs and SLOs?

**Short answer**:
- **SLI (Service Level Indicator)**: a measurable signal (e.g., crash-free sessions %, INP p75, API error rate).
- **SLO (Service Level Objective)**: the target you commit to (e.g., “crash-free sessions ≥ 99.9% weekly”).

**Interview-friendly line**: “SLOs turn ‘performance matters’ into an explicit engineering contract.”

---

### 2) What are practical SLIs for front-end apps?

**Short answer**:
- Reliability: crash-free sessions, unhandled rejections rate
- Performance: INP/LCP/CLS percentiles (p75), long task rate
- Availability-ish: navigation success rate, API error rate for key endpoints
- UX: funnel completion for critical journeys (checkout, signup)

---

## Release markers

### 1) What is a release marker and why use it?

**Short answer**: A release marker tags observability data with a deploy/version boundary so you can correlate regressions (errors/vitals) to a specific release.

**Common fields**:
- version/commit SHA
- deploy time
- environment (prod/staging)

---

## Sampling strategies

### 1) Why do you need sampling in RUM and logs?

**Short answer**: Collecting everything is expensive and risky (cost + privacy). Sampling keeps costs manageable while still detecting regressions.

**Common strategies**:
- Random sampling (e.g., 5% of sessions)
- Adaptive sampling (increase on errors/regressions)
- Stratified sampling (ensure coverage for key routes/devices)

---

## Privacy & redaction patterns

### 1) What should never be captured in logs/RUM/error reports?

**Short answer**: Secrets and sensitive user data: access tokens, passwords, full credit card numbers, personal data not needed for debugging.

---

### 2) Practical redaction patterns

**Short answer**:
- Scrub query params (allowlist keys)
- Redact request/response bodies by default; allowlist safe fields
- Hash user identifiers instead of raw emails
- Mask input fields and DOM text capture in session replay tools

---

## Incident workflow

### 1) What’s a sane incident workflow for a front-end regression?

**Short answer**:
- Detect (alerts on errors/vitals/funnel drop)
- Triage (scope: who/where/when; is it release-correlated?)
- Mitigate (kill switch/rollback/disable flag)
- Fix (root cause, patch, validate)
- Prevent (tests, monitors, budgets, playbook update)

**Good habit**: decide rollback criteria before rollout, not during the incident.

---

## Suggested practice exercises

- Define an “incident playbook” for a spike in JS errors after a release (triage, rollback, fix, prevent).
- Design a dashboard with 5 key RUM metrics and thresholds that would page you.
- Propose a flag rollout plan for a risky UI refactor (ramp schedule + rollback criteria).
- Define 3 front-end SLOs for a product (reliability + performance + funnel) and how you’d alert on them.
- Create a privacy allowlist for logging (what fields are allowed) and a redaction rule set for query params.

## Links / references

- web.dev: INP: https://web.dev/articles/inp
- web.dev: RUM overview: https://web.dev/articles/vitals
- OpenTelemetry (tracing concepts): https://opentelemetry.io/docs/concepts/observability-primer/
