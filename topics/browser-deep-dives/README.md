# Browser deep dives — event loop, rendering labs, workers, IndexedDB

These topics come up in senior front-end interviews because they explain “why it feels slow” and “why this async behavior is surprising”: the **event loop**, the **rendering pipeline**, **workers**, and **IndexedDB**.

## How to use this file

- Treat each section as a **flashcard + lab**: explain the concept, then implement the mini experiment.
- Always connect the concept to a user impact: responsiveness (INP), correctness, reliability, and UX.

---

## Event loop (tasks vs microtasks)

### 1) What is the event loop (high level)?

**Short answer**: The browser runs JS on a single main thread. The event loop pulls work from queues (tasks and microtasks), runs it, and the browser renders frames between turns when possible.

---

### 2) Microtasks vs macrotasks (tasks): what’s the difference?

**Short answer**:
- **Microtasks**: Promise reactions (`then/catch/finally`), `queueMicrotask`. They run **after the current call stack** and generally **before** the next task and before the browser continues to other work.
- **Tasks (macrotasks)**: timers (`setTimeout`), I/O callbacks, message events, etc.

**Interview-friendly rule**: “Microtasks run before the next task.”

---

### 3) What’s the typical ordering in a snippet?

**Short answer**: synchronous code → microtasks → next task.

**Example**:

```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
// A D C B
```

---

### 4) Why can microtasks be a problem?

**Short answer**: An unbounded microtask loop can starve the event loop and delay rendering/input handling (hurting responsiveness).

---

### 5) What counts as a “task” in the browser, practically?

**Short answer**: A task is a unit of work taken from a task queue (e.g., timer callback, message event, I/O callback). After a task completes, the browser can run microtasks and then potentially render.

**Why you care**:
- Long tasks block input/paint (hurts INP).
- Scheduling work as microtasks can starve rendering if abused.

---

## Rendering + layout thrashing (labs)

### 1) What is layout thrashing?

**Short answer**: Repeatedly forcing synchronous layout by alternating DOM reads (layout-dependent) and DOM writes in a tight loop.

**Common forced-layout reads**:
- `getBoundingClientRect()`
- `offsetWidth/offsetHeight`

---

### 2) How do you fix it?

**Short answer**:
- Batch reads together, batch writes together
- Use `requestAnimationFrame` for visual updates
- Avoid measuring layout repeatedly inside loops

---

### 3) Lab: create and fix layout thrashing

**Prompt**:
- Create 200 elements.
- In a loop, read `getBoundingClientRect()` and then write `style.transform`.
- Record a Performance trace.
- Refactor to: read all rects first, then write styles in a separate pass; compare.

---

## Workers (Web Worker / SharedWorker)

### 1) What is a Web Worker?

**Short answer**: A Web Worker runs JS in a background thread, letting you offload CPU-heavy work so the main thread stays responsive. It can’t touch the DOM directly; you communicate via messages.

---

### 2) When should you use a worker?

**Short answer**:
- Heavy parsing/processing (large JSON, data transforms)
- Compression/encryption
- Image processing
- Any computation causing long tasks that hurt INP

---

### 3) Web Worker vs SharedWorker (high level)

**Short answer**:
- **Web Worker**: usually one per page context.
- **SharedWorker**: can be shared across multiple tabs/windows (same origin), useful for shared connections/state.

**Trade-off**: SharedWorker adds complexity and has more constrained support patterns; use when multi-tab coordination is a real requirement.

---

### 4) Worker pitfalls

**Short answer**:
- Serialization cost (structured clone) for large messages
- Debugging complexity
- Need a build strategy for bundling worker code

---

## IndexedDB

### 1) What is IndexedDB and when would you use it?

**Short answer**: IndexedDB is an asynchronous, transactional key-value/document database in the browser. Use it for large client-side storage: offline-first data, caches, and persistent app state beyond what `localStorage` can safely handle.

---

### 2) IndexedDB vs localStorage: key differences

**Short answer**:
- IndexedDB is **async** and supports large structured data.
- localStorage is **sync** (can block) and string-only.

---

### 3) Common IndexedDB pitfalls

**Short answer**:
- Schema/version upgrades are awkward; you must handle migrations.
- Data consistency and invalidation strategies matter (especially offline sync).
- Transactions require careful scoping.

---

## Fetch & Streams

### 1) What is streaming, and why does it matter?

**Short answer**: Streaming lets you process data incrementally as it arrives instead of waiting for the full payload. It can improve perceived performance for large responses and enables progressive rendering/processing.

---

### 2) What are common browser stream use cases?

**Short answer**:
- Read large responses incrementally (e.g., NDJSON/log streams)
- Upload/download progress and chunk processing
- Media processing pipelines (advanced)

---

### 3) What’s the high-level API shape (conceptually)?

**Short answer**:
- `fetch()` returns a `Response`
- `Response.body` is a `ReadableStream` (when supported)
- You can decode bytes with `TextDecoderStream` or a `TextDecoder`

**Pitfall**: Not all responses are streamable the same way (CORS, compression, buffering by intermediaries).

---

## IntersectionObserver

### 1) What is `IntersectionObserver` and what is it for?

**Short answer**: It lets you observe when an element enters/exits a root’s viewport (or a scroll container) without wiring heavy scroll handlers. It’s commonly used for lazy loading, infinite scroll, and visibility tracking.

---

### 2) Common pitfalls with `IntersectionObserver`

**Short answer**:
- Observing the wrong root (default is viewport)
- Forgetting thresholds/root margins (loads too late)
- Not disconnecting observers (leaks)

---

## History/router patterns

### 1) What is the History API used for in SPAs?

**Short answer**: SPAs use `history.pushState`/`replaceState` to change the URL without a full page reload, and listen to `popstate` for back/forward navigation.

---

### 2) Common router edge cases you should mention

**Short answer**:
- Scroll restoration (restore on back/forward)
- Handling 404s and unknown routes
- Deep linking and refresh behavior (server must serve the app shell)
- Keeping URL state in sync (filters/pagination)

---

## Permissions & media APIs

### 1) What is the Permissions API (high level)?

**Short answer**: It lets you query permission state (granted/denied/prompt) for some capabilities and react to changes, improving UX around prompting.

---

### 2) What are common media APIs front-end engineers use?

**Short answer**:
- `getUserMedia` (camera/mic)
- `MediaDevices` enumeration (available devices)
- `MediaRecorder` (recording)

**Pitfall**: These require secure contexts (HTTPS) and careful UX around prompts and fallback states.

---

## Suggested practice exercises

- Predict the output ordering of 10 small snippets mixing `setTimeout`, Promises, and `queueMicrotask`.
- Implement the layout thrashing lab and fix it; measure long tasks before/after.
- Offload a heavy computation to a Web Worker and show improved responsiveness.
- Build a tiny “offline cache” layer using IndexedDB (store and retrieve API responses with timestamps).
- Use `IntersectionObserver` to lazy-load a list of images with a root margin and compare to a scroll handler approach.
- Build a tiny router demo: `pushState`, `popstate`, and a simple route matcher; implement scroll restoration.
- Fetch a large text response and process it incrementally (stream) to avoid blocking the main thread.
- Build a “request permission” UX for camera/mic with clear fallback when denied.

## Links / references

- MDN: Event loop: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop
- MDN: `queueMicrotask`: https://developer.mozilla.org/en-US/docs/Web/API/queueMicrotask
- web.dev: Rendering performance: https://web.dev/articles/rendering-performance
- MDN: Web Workers: https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API
- MDN: IndexedDB: https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API
- MDN: Fetch API: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API
- MDN: Streams API: https://developer.mozilla.org/en-US/docs/Web/API/Streams_API
- MDN: Intersection Observer API: https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API
- MDN: History API: https://developer.mozilla.org/en-US/docs/Web/API/History_API
- MDN: Permissions API: https://developer.mozilla.org/en-US/docs/Web/API/Permissions_API
- MDN: MediaDevices / getUserMedia: https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia
