# Networking (Front-End) — HTTP basics, caching headers, retries, idempotency, rate limits

Networking questions show up in front-end interviews because they affect reliability and UX: “why is this request failing?”, “why is data stale?”, “how do we retry safely?”, “how do we avoid hammering the API?” This topic focuses on practical HTTP and client-side networking strategies.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Always mention how you’d **debug it in DevTools** (Network tab).
- When discussing retries, always mention **idempotency**.

---

## HTTP basics

### 1) What’s the difference between GET, POST, PUT, PATCH, DELETE?

**Short answer**:
- **GET**: read data (should be safe and idempotent)
- **POST**: create/trigger actions (not necessarily idempotent)
- **PUT**: replace a resource (idempotent)
- **PATCH**: partial update (may or may not be idempotent depending on design)
- **DELETE**: remove a resource (idempotent in most designs)

---

### 2) What do 2xx / 3xx / 4xx / 5xx mean?

**Short answer**:
- **2xx**: success
- **3xx**: redirect
- **4xx**: client issue (bad request, unauthorized, forbidden, not found, validation)
- **5xx**: server issue (retryable in some cases)

---

### 3) What’s the difference between 401 and 403?

**Short answer**:
- **401 Unauthorized**: not authenticated (missing/invalid credentials)
- **403 Forbidden**: authenticated but not allowed (insufficient permissions)

---

## Caching headers (in practice)

### 1) What headers should you recognize for caching?

**Short answer**:
- `Cache-Control` (primary)
- `ETag` / `If-None-Match` (revalidation)
- `Last-Modified` / `If-Modified-Since` (older revalidation)
- `Age` (from shared caches/CDN)
- `Vary` (which request headers affect cache key)

---

### 2) What’s the “fingerprinted assets” caching pattern?

**Short answer**: For hashed JS/CSS/images (content-based filenames), use:
- `Cache-Control: public, max-age=31536000, immutable`

…and keep HTML fresher (short TTL + revalidation) since it’s the entry point.

---

### 3) What does `Vary` do and why can it be dangerous?

**Short answer**: `Vary` tells caches that responses differ based on certain request headers (e.g., `Accept-Encoding`, `Authorization`). Overusing `Vary` can explode cache keys and reduce cache hit rate.

---

## Retries & backoff

### 1) When should the client retry a request?

**Short answer**:
- Retry on transient failures: network errors, timeouts, some **5xx**, and **429** (rate limit) with guidance.
- Don’t blindly retry **4xx** validation/auth errors.

---

### 2) What is exponential backoff with jitter and why use it?

**Short answer**: Increase delay after each retry (e.g., 0.5s, 1s, 2s, 4s…) and add randomness (jitter) to prevent thundering herds when many clients retry simultaneously.

---

### 3) What should you avoid when implementing retries?

**Short answer**:
- Retrying non-idempotent mutations without safeguards
- Retrying too aggressively (DDOS your own API)
- Retrying forever (cap attempts/time)

---

## Idempotency

### 1) What does idempotency mean?

**Short answer**: An operation is idempotent if repeating it multiple times has the same effect as doing it once (from the system’s perspective).

**Why it matters**: It determines whether retries are safe.

---

### 2) How do you make POST requests safer to retry?

**Short answer**: Use idempotency keys (a unique request id) so the server can detect duplicates and return the original result instead of creating duplicates.

---

## Rate limits

### 1) What is HTTP 429 and how should the client react?

**Short answer**: **429 Too Many Requests** means you hit a rate limit. The client should back off, respect `Retry-After` when present, and reduce request frequency (debounce, batch, cache).

---

### 2) How do you prevent rate limiting from the front end?

**Short answer**:
- Debounce/throttle high-frequency requests (search-as-you-type)
- Cache and dedupe requests
- Batch when possible
- Avoid polling too frequently; prefer push (SSE/WS) for real-time needs

---

## API contracts that affect networking (filters/sorting, errors, versions)

### 1) Filtering/sorting contracts: what should the API specify?

**Short answer**:
- How filters are encoded (query params) and how multiple filters combine (AND/OR semantics).
- Sorting fields and direction (e.g., `sort=createdAt:desc`).
- Whether results are stable (tie-breakers like `id`) to prevent duplicates across pages.

**Front-end benefit**: predictable cache keys and fewer pagination bugs.

---

### 2) Error codes taxonomy: what does “good” look like?

**Short answer**:
- Use HTTP status codes for broad class (4xx vs 5xx).
- Provide a stable, machine-readable `code` for app-level handling (e.g., `VALIDATION_ERROR`, `RATE_LIMITED`, `NOT_AUTHORIZED`).
- Include `requestId` to support tracing and support tickets.

---

### 3) Rate limit headers: what should you look for?

**Short answer**:
- `Retry-After` on 429 (backoff guidance).
- If the API provides budget headers, use them to adapt client behavior (slow down, debounce more).

**Practical guidance**: Even if you don’t have fancy headers, treat 429 as a signal to back off and reduce frequency.

---

### 4) Idempotency keys: what are the practical details?

**Short answer**:
- Generate a unique id per user-intended action and send it as a header (commonly `Idempotency-Key`).
- Server stores and dedupes for a time window; repeated requests return the original result.
- Important for retries on flaky networks and double-click submits.

**Front-end UX win**: safer retries without creating duplicates.

---

### 5) API versioning & deprecation: what should the client plan for?

**Short answer**:
- Prefer backward-compatible evolution (additive changes) when possible.
- Use deprecation headers/announcements and an upgrade window.
- Avoid breaking response shapes without a migration path (feature flag/dual run).

**Client practice**: monitor for deprecation warnings and keep a dependency upgrade cadence.

---

## Advanced networking (HTTP/2/3, cookies, CORS, retries, circuit breakers)

### 1) HTTP/2 vs HTTP/3: what’s the interview-level difference?

**Short answer**:
- **HTTP/2**: multiplexes many requests over a single TCP connection (reduces head-of-line blocking at the HTTP layer).
- **HTTP/3**: runs over QUIC (UDP-based) and improves behavior under packet loss; connection setup can be faster in some scenarios.

**Practical front-end takeaways**:
- You still benefit from fewer large resources (bundle discipline) and good caching.
- Many “performance wins” are still about payload size, priority, and main-thread work.

---

### 2) Cookies and fetch credentials modes: what should you know?

**Short answer**:
- Cookies are attached based on domain/path/SameSite/Secure rules.
- `fetch` has a `credentials` option that controls whether cookies are sent:
  - `same-origin` (default): send cookies only for same-origin requests
  - `include`: send cookies for cross-origin requests (requires server CORS `credentials` setup)
  - `omit`: never send cookies

**Common bug**: “It works in the browser when same-origin, but not cross-origin” → missing `credentials: "include"` and/or CORS credential headers.

---

### 3) CORS deeper: what triggers a preflight and how do you debug it?

**Short answer**:
- “Non-simple” requests trigger a preflight (OPTIONS) before the real request (custom headers, non-simple content types, some methods).
- Debug by checking the **OPTIONS** request in DevTools and verifying:
  - `Access-Control-Allow-Origin`
  - `Access-Control-Allow-Methods`
  - `Access-Control-Allow-Headers`
  - `Access-Control-Allow-Credentials` (if using cookies)

**Rule of thumb**: If it works in Postman but fails in the browser, suspect CORS/credentials.

---

### 4) Retries policy: what should you retry vs not retry?

**Short answer**: Retry only when it’s likely transient and safe.

**Policy sketch** (use as a checklist):

```text
SAFE to retry (usually):
- Network error / timeout
- 502 / 503 / 504
- 429 (respect Retry-After)

CAREFUL:
- 408 (depends on endpoint and idempotency)

DO NOT blindly retry:
- 400 / 401 / 403 / 404
- Validation errors (often 422)
- Non-idempotent POST without idempotency keys
```

---

### 5) What is a circuit breaker and when would the front end use one?

**Short answer**: A circuit breaker is a safety mechanism that stops calling a failing dependency for a period of time (fail fast) to avoid cascading failures and a bad UX loop.

**Front-end use cases**:
- Repeated 5xx from a non-critical service → disable the feature temporarily and show fallback UI.
- Third-party API is down → stop hammering it; switch to cached data or hide the widget.

**Practical pattern**:
- Trip after N failures in a window → open breaker for T seconds → half-open probe → close if healthy.

---

## Suggested practice exercises

- Given a sequence of failures (timeout → 502 → 200), implement a retry policy with exponential backoff + jitter.
- Design caching headers for HTML vs hashed assets and explain how to debug cache hits in DevTools.
- Design an idempotent create endpoint flow using an idempotency key.
- Implement a client-side “search as you type” with debouncing, cancellation, and rate-limit handling.
- Design a filtering/sorting query contract for a list page and show the resulting cache key strategy on the client.
- Propose an error code taxonomy for a signup endpoint (field errors + requestId) and show how the UI maps codes to messages.
- Create a CORS failure (custom header + credentials) and explain the preflight. Fix it by listing the required server headers.
- Draft a retry policy for a checkout flow: specify which calls retry, which don’t, and how idempotency keys factor in.
- Implement a simple client-side circuit breaker for a non-critical endpoint and show the fallback UX.

## Links / references

- MDN: HTTP overview: https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview
- MDN: HTTP caching: https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching
- IETF: HTTP status codes (RFC 9110): https://www.rfc-editor.org/rfc/rfc9110.html
- MDN: Retry-After: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Retry-After
- MDN: `fetch()` credentials: https://developer.mozilla.org/en-US/docs/Web/API/fetch#credentials
- MDN: CORS: https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
