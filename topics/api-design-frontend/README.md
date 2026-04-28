# API design for front-end — pagination, optimistic updates, error contracts, typing

API design decisions directly affect front-end complexity: caching, pagination UX, optimistic updates, and error handling. This topic focuses on what front-end engineers should ask for (or design) in an API contract to build reliable UIs.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Always connect API design to front-end concerns: caching keys, retries, pagination UX, and type safety.
- When you propose an API, mention **error shape** and **edge cases**.

---

## Pagination

### 1) Offset vs cursor pagination: what’s the difference?

**Short answer**:
- **Offset**: `?limit=20&offset=40` (simple, page-like). Can be inconsistent when data changes (inserts/deletes shift offsets).
- **Cursor**: `?limit=20&cursor=abc` (stable ordering). Better for infinite scroll and large datasets.

**Rule of thumb**: Use cursor pagination for frequently-changing feeds and large datasets; offset can be fine for small, stable lists.

---

### 2) What should a good cursor pagination response include?

**Short answer**:
- `items: T[]`
- `nextCursor: string | null`
- `hasMore: boolean` (optional but convenient)

**Common front-end requirement**: stable sort order (e.g., `createdAt desc, id desc`) to prevent duplicates/missing items.

---

### 3) What edge cases should you call out for pagination?

**Short answer**:
- Duplicates across pages (unstable ordering)
- Items disappearing between pages (deletes)
- New items inserted at the top during scrolling
- Empty pages / end-of-list signaling

**Mitigation**:
- Stable ordering + cursor based on that ordering
- Idempotent dedupe by id on the client

---

## Error contracts

### 1) What is an “error contract”?

**Short answer**: A consistent response shape for errors so the front-end can handle errors predictably (display messages, map field errors, retry).

---

### 2) What should an API error payload include?

**Short answer**:
- A stable `code` (machine-readable)
- A human-friendly `message` (optional, not necessarily user-facing)
- Optional `details` / `fields` for validation errors
- A `requestId` / correlation id for support/debugging

**Example shape (conceptual)**:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "fields": { "email": "Email is invalid" },
    "requestId": "req_123"
  }
}
```

---

### 3) How should the front-end treat 4xx vs 5xx?

**Short answer**:
- **4xx**: user/client issue (show actionable feedback; don’t retry blindly).
- **5xx**: server issue (retry with backoff when safe; show fallback UI).

**Important nuance**: idempotency matters for retries (see below).

---

## Optimistic updates

### 1) What are optimistic updates and when are they worth it?

**Short answer**: Update the UI immediately as if the server succeeded, then confirm/rollback based on response. Worth it for low-risk interactions where latency hurts UX (like/unlike, toggles).

---

### 2) What API support helps optimistic updates?

**Short answer**:
- Idempotent endpoints where possible
- Stable entity ids and updated timestamps/version numbers
- Clear conflict behavior (last-write-wins vs versioned updates)

**Common pattern**:
- Use a version/etag to prevent lost updates (`If-Match` / optimistic concurrency).

---

### 3) How do you handle rollback and reconciliation?

**Short answer**:
- Keep the previous state snapshot for rollback
- On success, reconcile with server’s source of truth
- Dedupe events if real-time updates echo your mutation back

---

## Typing and contracts (TypeScript)

### 1) How do you keep front-end types aligned with the API?

**Short answer**:
- Generate TS types from a schema (OpenAPI/GraphQL) when possible.
- Validate runtime data at boundaries (because TS types don’t exist at runtime).

---

### 2) What’s a good TS type pattern for API responses?

**Short answer**: Use discriminated unions for success/error and keep “loadable” states explicit.

**Example**:

```ts
type ApiOk<T> = { ok: true; data: T; requestId: string };
type ApiErr = { ok: false; error: { code: string; message?: string; requestId: string } };
type ApiResult<T> = ApiOk<T> | ApiErr;
```

---

## Suggested practice exercises

- Design a cursor-paginated “activity feed” endpoint and list the front-end cache keys and dedupe strategy.
- Design an error contract that supports field-level errors for a signup form.
- Implement an optimistic “like” mutation and handle rollback + reconciliation with a server response.
- Model the API types using TS discriminated unions and show narrowing in UI code.

## Links / references

- Stripe API guidelines (good patterns, even if you don’t use Stripe): https://stripe.com/docs/api
- OpenAPI spec: https://spec.openapis.org/oas/latest.html
