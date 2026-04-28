# Browser & Web APIs — DOM & BOM (Foundations)

Browser & Web APIs are frequent interview topics because they explain how your UI actually works outside the framework: **rendering**, **events**, **storage**, **navigation**, **performance**, and **security**. This section starts with the core mental model: **DOM vs BOM**.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- When possible, tie answers to **real bugs** (event bubbling surprises, stale references, layout thrashing, navigation issues).
- Add your own notes under **Notes / variations**.

---

## Questions & answers

### 1) What is the DOM?

**Short answer**: The DOM (Document Object Model) is the browser’s in-memory representation of the HTML document as a tree of nodes. It’s the API you use to read/change structure, content, and attributes.

**Common things you do with the DOM**:
- Query elements: `document.querySelector`, `getElementById`
- Read/update content: `textContent`, `innerHTML` (careful)
- Add/remove nodes: `append`, `remove`
- Work with classes/attributes: `classList`, `setAttribute`

---

### 2) What is the BOM?

**Short answer**: The BOM (Browser Object Model) is a loose term for browser-provided APIs around the **window/tab** environment (navigation, history, storage, timers, location, etc.). It’s not a single spec like the DOM; it’s a bundle of “browser environment” APIs.

**Typical BOM-ish APIs**:
- `window`, `location`, `history`, `navigator`
- `setTimeout` / `setInterval`
- `localStorage` / `sessionStorage`
- `screen` (limited usefulness)

---

### 3) DOM vs BOM: how do you explain the difference in one sentence?

**Short answer**: **DOM = the document content tree**, **BOM = the browser environment around the document** (tab/window, navigation, device info, storage, timers).

---

### 4) What is the difference between `document` and `window`?

**Short answer**:
- `window` is the global object for the browsing context (the tab).
- `document` is the DOM entry point for the page content loaded in that window.

**Example mental model**:
- `window.location` → where you are
- `document.body` → what’s on the page

---

### 5) How does event propagation work? (capturing vs bubbling)

**Short answer**: Events travel through three phases:
1. **Capturing**: from the window/document down to the target
2. **Target**: the event hits the target element
3. **Bubbling**: from the target back up the tree

**Why it matters**:
- Event delegation relies on bubbling (one listener on a parent).
- `stopPropagation()` affects whether it continues upward.

---

### 6) What’s the difference between `event.target` and `event.currentTarget`?

**Short answer**:
- `event.target`: the element that actually triggered the event (deepest node).
- `event.currentTarget`: the element whose listener is currently running.

**Common interview gotcha**: In event delegation, these are often different.

---

### 7) What’s the difference between `textContent` and `innerHTML`?

**Short answer**:
- `textContent`: sets/gets text safely (no HTML parsing; safer against XSS).
- `innerHTML`: parses HTML and replaces children (powerful but risky; can introduce XSS if used with unsanitized input).

**Rule of thumb**: Prefer `textContent` unless you intentionally need HTML and you can guarantee safety.

---

### 8) What is reflow (layout) vs repaint, and how can DOM code trigger jank?

**Short answer**:
- **Reflow/layout**: browser recalculates sizes/positions (often expensive).
- **Repaint**: browser redraws pixels (can be cheaper, but still work).

**Common cause of jank**: layout thrashing (alternating reads that force layout with writes that invalidate it).

**Example pattern to avoid**:
- Read layout (`getBoundingClientRect`) → write style → read layout again in a loop.

---

### 9) `localStorage` vs `sessionStorage`: what’s the difference?

**Short answer**:
- `localStorage`: persists across browser sessions (until cleared).
- `sessionStorage`: scoped to a tab/session; cleared when the tab closes.

**Notes / variations**:
- Both are synchronous APIs and can block the main thread if overused.

---

### 10) History + navigation basics: what do `location` and `history` do?

**Short answer**:
- `location` reads/sets the current URL (navigation).
- `history` manipulates the session history stack (`back`, `forward`, `pushState`, `replaceState`).

**Use case**:
- SPAs use `pushState`/`replaceState` to update the URL without full page reloads.

---

## Web storage & cookies (localStorage / sessionStorage / cookies)

### 1) What are `localStorage` and `sessionStorage`?

**Short answer**: They’re key/value stores exposed on `window` as part of the Web Storage API.

**Key differences**:
- `localStorage`: persists until cleared (survives browser restarts).
- `sessionStorage`: persists only for the page session (typically per-tab; cleared when the tab closes).

**Important properties**:
- Values are stored as **strings** (you serialize objects via `JSON.stringify`).
- The API is **synchronous** (heavy use can block the main thread).

---

### 2) What are cookies, and how are they different from Web Storage?

**Short answer**: Cookies are small pieces of data attached to HTTP requests (when applicable). They’re used heavily for server-managed sessions and cross-request state.

**Key differences**:
- **Sent to server**: cookies are included in requests (subject to domain/path/samesite/secure rules); storage is not.
- **Size**: cookies are small; storage is usually larger.
- **Access controls**: cookies can be **HttpOnly** (not readable by JS), which is a security advantage for session tokens.

---

### 3) When would you use cookies vs `localStorage`/`sessionStorage`?

**Short answer**:
- Use **cookies** for authentication/session identifiers when the server needs them automatically on requests (often with `HttpOnly` + `Secure` + `SameSite`).
- Use **`localStorage`** for non-sensitive, long-lived client preferences (theme, onboarding dismissed).
- Use **`sessionStorage`** for per-tab ephemeral state (wizard step, temporary flags).

**Interview-friendly guidance**: “Don’t store secrets/tokens in Web Storage; prefer HttpOnly cookies for session tokens.”

---

### 4) Security: XSS vs CSRF — how do these storage options relate?

**Short answer**:
- **XSS**: If an attacker can run JS on your page, they can read `localStorage`/`sessionStorage` and any non-HttpOnly cookies.
- **CSRF**: Cookies are automatically sent with requests, so they can be vulnerable to CSRF unless you mitigate it (e.g., `SameSite`, CSRF tokens).

**Key point**:
- **HttpOnly cookies** reduce token theft via XSS (JS can’t read them), but you still must prevent XSS.
- To protect cookie-based auth from CSRF, use **`SameSite=Lax/Strict`** (or `None; Secure` when cross-site is required) plus CSRF defenses as needed.

---

### 5) Cookie attributes you should know (interview basics)

**Short answer**:
- `HttpOnly`: JS can’t read the cookie (good for session cookies).
- `Secure`: only sent over HTTPS.
- `SameSite`: controls cross-site sending (`Strict`, `Lax`, `None`).
- `Expires` / `Max-Age`: lifetime.
- `Domain` / `Path`: scope.

**Example**:

```http
Set-Cookie: session=abc123; HttpOnly; Secure; SameSite=Lax; Path=/; Max-Age=3600
```

---

### 6) Performance: what’s a common pitfall with `localStorage`?

**Short answer**: It’s synchronous; large reads/writes in hot paths (render loops, scroll handlers) can cause jank. Also, cookies bloat request headers if you store too much in them.

**Rule of thumb**:
- Keep storage reads/writes off the critical path.
- Store small values; avoid writing frequently.

---

### 7) What are some common edge cases or limitations?

**Short answer**:
- Web Storage can be unavailable/limited in some contexts (private mode, disabled storage, quota).
- `sessionStorage` is not shared across tabs (by design).
- Cookies have cross-site restrictions and third-party cookie blocking can break some flows.

---

## Browser rendering pipeline (critical rendering path)

### 1) What are the high-level steps in the browser rendering pipeline?

**Short answer** (typical path):
1. **Parse HTML → DOM**
2. **Parse CSS → CSSOM**
3. **Combine DOM + CSSOM → render tree**
4. **Layout (reflow)**: compute geometry (sizes/positions)
5. **Paint**: draw pixels (text, colors, shadows, etc.)
6. **Compositing**: combine layers and present the final frame (often GPU-accelerated)

**Notes / variations**:
- Not every change triggers every step; browsers try to minimize work.

---

### 2) What is the DOM vs CSSOM vs render tree?

**Short answer**:
- **DOM**: structure/content nodes from HTML.
- **CSSOM**: computed CSS rules/structures from stylesheets.
- **Render tree**: only the nodes that affect rendering (e.g., excludes `display: none`) with styles attached, used for layout/paint.

---

### 3) What triggers layout (reflow) vs paint vs compositing-only updates?

**Short answer**:
- **Layout** triggers when geometry changes (size/position). Examples: changing `width`, `height`, `padding`, `font-size`, inserting/removing DOM nodes.
- **Paint** triggers when visuals change but geometry doesn’t. Examples: `color`, `background-color`, `box-shadow` (often paint-heavy).
- **Composite-only** is the cheapest path; it typically happens when animating **`transform`** and **`opacity`** (browser can move existing painted layers).

**Interview-friendly rule**: “Prefer `transform` and `opacity` for animations; avoid layout-thrashing properties.”

---

### 4) What is “forced synchronous layout” (layout thrashing)?

**Short answer**: It happens when JS reads layout information (which forces the browser to flush pending style/layout work) and then writes styles repeatedly in a tight loop, causing repeated layout recalculations.

**Common layout reads**:
- `getBoundingClientRect()`
- `offsetWidth/offsetHeight`
- `scrollTop` (context-dependent)

**Fix**:
- Batch **reads** together, then batch **writes** together.
- Use `requestAnimationFrame` for DOM writes tied to animation frames.

---

### 5) How do `requestAnimationFrame` and the event loop relate to rendering?

**Short answer**: `requestAnimationFrame(cb)` schedules `cb` to run before the next paint, which is ideal for animation-related DOM updates. If the main thread is busy (long tasks), frames drop and animations stutter.

**Notes / variations**:
- In interviews, a good line is: “Use `requestAnimationFrame` for visual updates; use debouncing/throttling for high-frequency input like scroll/resize.”

---

### 6) How do script and CSS loading affect first render?

**Short answer**:
- CSS is render-blocking: the browser generally waits for CSS needed for the page before painting to avoid FOUSC (flash of unstyled content).
- A normal `<script>` in the document blocks HTML parsing while it downloads/executes.
- `defer` downloads in parallel and executes after parsing (preserving order).
- `async` downloads in parallel and executes as soon as ready (order not guaranteed).

**Practical guidance**:
- Use `defer` for most scripts that don’t need to run before HTML is parsed.
- Inline critical CSS (or load critical CSS early) to improve first paint.

---

### 7) What are common performance metrics tied to the rendering pipeline?

**Short answer**:
- **LCP** (Largest Contentful Paint): when the largest above-the-fold element is rendered.
- **CLS** (Cumulative Layout Shift): layout stability (avoid late-loading size changes).
- **INP** (Interaction to Next Paint): responsiveness to user input.

**Pipeline tie-in**:
- Layout shifts often come from missing image dimensions, late-loaded fonts, or inserting content above existing content.

---

## Suggested practice exercises

- Build a demo that repeatedly reads `getBoundingClientRect()` and writes style in a loop; then refactor to batch reads/writes and compare frame rate.
- Create an animation two ways: one using `top/left`, another using `transform: translate(...)`; compare in Performance panel.
- Add `width`/`height` to images and observe CLS improvements.

## Links / references

- MDN: Critical rendering path: https://developer.mozilla.org/en-US/docs/Web/Performance/Critical_rendering_path
- web.dev: Rendering performance: https://web.dev/articles/rendering-performance

---

## WebSockets

### 1) What are WebSockets and what problem do they solve?

**Short answer**: WebSockets provide a **persistent, full-duplex** connection between the browser and a server, enabling real-time messaging without repeated HTTP requests (e.g., chat, live dashboards, multiplayer, collaboration).

**Key property**: After the initial handshake, data flows both ways over a single long-lived connection.

---

### 2) How does the WebSocket handshake work (high level)?

**Short answer**: The client starts with an HTTP request that asks to **upgrade** the connection. If the server accepts, the protocol switches to WebSocket and the TCP connection stays open for bidirectional frames.

**Interview note**: This is why WebSockets can go through HTTP infrastructure, but proxies/load balancers must support upgrade.

---

### 3) What does the browser WebSocket API look like?

**Short answer**: In the browser you create a `WebSocket(url)` and use event handlers: `onopen`, `onmessage`, `onerror`, `onclose`, plus `send()`.

**Example**:

```js
const ws = new WebSocket("wss://example.com/realtime");

ws.onopen = () => ws.send(JSON.stringify({ type: "hello" }));
ws.onmessage = (e) => console.log("message", e.data);
ws.onerror = (e) => console.error("ws error", e);
ws.onclose = (e) => console.log("closed", e.code, e.reason);
```

---

### 4) WebSockets vs HTTP polling vs SSE: when do you choose what?

**Short answer**:
- **WebSockets**: bi-directional, low-latency, interactive real-time (client ↔ server).
- **SSE (Server-Sent Events)**: server → client only; simpler for streaming updates (notifications, live feeds).
- **Polling/long polling**: simplest, works everywhere, higher overhead and latency.

**Interview-friendly framing**: “If I only need server→client streams, I consider SSE; if I need interactive bi-directional communication, WebSockets.”

---

### 5) How do you handle reconnection and transient network failures?

**Short answer**: Implement reconnection with **exponential backoff + jitter**, and design the protocol so clients can **resubscribe** and **catch up** after reconnect (e.g., last event id/sequence).

**Common best practices**:
- Backoff: 0.5s → 1s → 2s → 4s… with a cap.
- Don’t reconnect forever in tight loops; respect offline state.
- Re-authenticate/refresh tokens if needed.

---

### 6) What are heartbeats/pings and why do you need them?

**Short answer**: Heartbeats detect dead connections (e.g., NAT timeouts, dropped Wi‑Fi) and keep intermediaries from closing idle sockets. Many stacks use ping/pong at the protocol level; at the app level you can also send a lightweight “heartbeat” message.

**Gotcha**: If you rely only on `onclose`, you might not detect half-open connections quickly.

---

### 7) What kind of data can you send? (text vs binary)

**Short answer**: You can send strings and binary data (`Blob`, `ArrayBuffer`). `message` events typically expose `event.data` as a string or blob depending on configuration and server frames.

**Practical note**: For structured messages, most apps use JSON with a `type` field.

---

### 8) Security: what should you know about auth, Origin, and `wss://`?

**Short answer**:
- Use **`wss://`** (WebSocket over TLS) in production.
- The browser includes an **`Origin`** header in the handshake; servers should validate it to prevent cross-site abuse.
- Avoid putting long-lived secrets in the URL. Prefer short-lived tokens and server-side validation.

**Common approaches**:
- Cookie-based auth (server validates session cookie during upgrade).
- Token-based auth (short-lived token passed at connect time, then rotated/renewed via a separate channel).

---

### 9) What do WebSocket close codes mean (conceptually)?

**Short answer**: Close codes communicate why a connection closed (normal closure vs protocol error vs policy violation). You don’t need to memorize all codes, but you should:
- log `code` and `reason`
- treat some closures as retryable and others as terminal (e.g., auth failure)

---

## Suggested practice exercises

- Build a small client that connects, sends a “subscribe” message, and renders updates; add reconnection with exponential backoff.
- Implement a heartbeat strategy and detect stale connections (simulate by disabling network).
- Design a tiny message protocol: `subscribe`, `unsubscribe`, `event`, `error`, with sequence numbers for catch-up.

## Links / references

- MDN: WebSocket API: https://developer.mozilla.org/en-US/docs/Web/API/WebSocket
- web.dev: WebSockets: https://web.dev/articles/websockets

---

## Service Workers

### 1) What is a Service Worker?

**Short answer**: A Service Worker is a script the browser runs in the background (separate from the page) that can **intercept network requests**, manage **caching**, enable **offline** experiences, and support features like **push notifications**.

**Key point**: It’s event-driven (not always running) and has no direct DOM access.

---

### 2) What problems do Service Workers solve?

**Short answer**:
- Offline support (cache-first experiences)
- Faster repeat visits (cache + prefetch)
- Resilient network handling (fallbacks, retries)
- App-like behavior for PWAs

---

### 3) What’s the Service Worker lifecycle?

**Short answer**:
- **Register** from the page (`navigator.serviceWorker.register(...)`)
- **Install**: often used to pre-cache assets
- **Activate**: clean up old caches, take control
- **Fetch**: intercept requests and respond from cache/network

**Common interview gotcha**: A newly installed SW usually doesn’t control existing tabs until reload (unless you use `clients.claim()` / skip-waiting patterns carefully).

---

### 4) What does “scope” mean for Service Workers?

**Short answer**: A Service Worker only controls pages/requests within its **scope**, which is derived from its URL path (and can be configured). Put the SW at the right path if you want it to control the whole site.

**Common bug**: “My SW isn’t intercepting requests” → it’s often registered under a too-narrow path.

---

### 5) What are common caching strategies?

**Short answer** (typical patterns):
- **Cache-first**: serve from cache, fall back to network (good for static assets).
- **Network-first**: try network, fall back to cache (good for frequently updated data).
- **Stale-while-revalidate**: serve cached immediately, update cache in background (good UX for many resources).
- **Cache-only / Network-only**: specialized cases.

**Interview framing**: “Pick per resource type: immutable assets vs HTML shell vs API data.”

---

### 6) How do updates work, and what are common pitfalls?

**Short answer**:
- The browser checks for SW updates and installs a new version when the script changes.
- An updated SW often waits until old clients close before activating (to avoid breaking open tabs).

**Pitfalls**:
- Cache invalidation: you must version caches and clean old caches during activate.
- “Stuck” clients: overly aggressive skip-waiting can break in-flight sessions if not coordinated.

---

### 7) Security constraints: why do Service Workers require HTTPS?

**Short answer**: Because a SW can intercept and modify network traffic for its scope, it’s a powerful capability; HTTPS (or localhost) is required to prevent man-in-the-middle attacks.

---

### 8) Service Workers vs HTTP cache: what’s the difference?

**Short answer**:
- HTTP cache is controlled by headers (`Cache-Control`, ETag) and the browser’s caching logic.
- Service Worker cache (Cache Storage API) is programmable; you decide what to cache and when to serve it.

**Best practice**: Use both—HTTP caching for normal behavior, SW caching for offline/resilience and advanced control.

---

## Suggested practice exercises

- Pre-cache a small asset list during install; add an activate step to clean up old cache versions.
- Implement stale-while-revalidate for a JSON endpoint.
- Build an offline fallback page for navigation requests.

## Links / references

- MDN: Service Worker API: https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API
- web.dev: Service workers: https://web.dev/learn/pwa/service-workers/
## Suggested practice exercises

- Implement event delegation for a list (click on a button inside each item) and log `target` vs `currentTarget`.
- Build a tiny “router” demo using `history.pushState` and `popstate`.
- Create a performance demo where you trigger layout thrashing, then fix it by batching reads/writes.
- Implement “remember theme” using `localStorage`, but keep the initial theme flash-free (apply early).
- Sketch an auth flow that uses HttpOnly cookies + `SameSite` and explain CSRF protections.

## Links / references

- MDN: DOM introduction: https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction
- MDN: `Window`: https://developer.mozilla.org/en-US/docs/Web/API/Window
- MDN: Events: https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/Events
- MDN: `localStorage`: https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage
- MDN: Web Storage API: https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API
- MDN: `document.cookie`: https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie
