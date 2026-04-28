# Front-End Debugging — Practical playbook (multi-topic)

Debugging is a core interview signal: how you reduce uncertainty, isolate variables, and fix issues without guessing. This topic is a practical, repeatable playbook covering multiple debugging areas you’ll hit in real front-end work.

## How to use this file

- Treat each section as a **checklist** you can apply under pressure.
- Always start by writing: **expected behavior**, **actual behavior**, **steps to reproduce**, **environment** (browser/device/network).
- Prefer minimal, reversible changes while you isolate the root cause.

---

## Debugging mindset (the 5-minute framework)

### 1) What’s a good default debugging loop?

**Short answer**:
- Reproduce reliably (or make it reproducible).
- Reduce the problem (min repro, binary search the cause).
- Observe with the right tool (console/network/perf/layout).
- Form a hypothesis, test it, repeat.
- Fix + add regression coverage (test/log/guardrail) if appropriate.

---

## JavaScript errors & runtime behavior

### 1) How do you debug “it works locally but not in prod”?

**Short answer**:
- Confirm build version + environment flags.
- Check sourcemaps availability and error stack traces.
- Compare runtime config (API base URL, CSP, cookies, auth).
- Look for timing/race issues (slower prod, different data).
- Check browser differences and polyfills.

---

### 2) How do you debug an exception quickly?

**Short answer**:
- Read the full stack trace (top frame is often the symptom; find the first app frame).
- Add a breakpoint at the throw site or at the call site.
- Inspect inputs and invariants (nulls, unexpected shapes).
- Confirm whether it’s data-driven (specific user/account).

---

### 3) Common “silent failure” sources

**Short answer**:
- Promise rejections not handled
- `catch` blocks swallowing errors
- feature flags short-circuiting logic
- exceptions inside event handlers that aren’t surfaced (depending on environment)

---

## Chrome/Browser DevTools essentials

### 1) What panels do you use most and for what?

**Short answer**:
- **Console**: errors, logs, live evaluation.
- **Sources**: breakpoints, call stack, watch expressions.
- **Network**: request/response, caching, headers, timing, waterfalls.
- **Elements**: DOM inspection, computed styles, box model.
- **Application**: storage, cookies, service workers, cache storage.
- **Performance**: flame charts, long tasks, layout/paint timing.

---

### 2) What are your “first 3” DevTools checks?

**Short answer**:
- Console errors/warnings (including mixed content/CORS).
- Network: failed requests (4xx/5xx), blocked, pending, wrong payload.
- Elements/Computed: is the DOM what you think it is + are styles applied?

---

## Network debugging (APIs, CORS, caching)

### 1) How do you debug “API call fails”?

**Short answer**:
- Verify URL/method/query/body matches expectations.
- Check status code and response body.
- Inspect request headers (auth, content-type) and response headers (CORS, cache).
- Check preflight requests (OPTIONS) and whether they fail.

---

### 2) CORS: what’s the quickest way to reason about it?

**Short answer**: It’s enforced by the browser. Look for:
- `Access-Control-Allow-Origin`
- `Access-Control-Allow-Credentials` (cookies)
- preflight (OPTIONS) failures for non-simple requests

**Rule of thumb**: If it works in curl/Postman but fails in the browser, suspect CORS/cookies.

---

### 3) Debugging caching issues (stale data/assets)

**Short answer**:
- In Network, check cache headers (`Cache-Control`, `ETag`, `Age`) and “from disk cache”.
- Hard reload / disable cache to confirm cache involvement.
- Verify asset fingerprinting (hash in filename) for static assets.

---

## CSS/layout debugging (the “why is it not aligned?” bucket)

### 1) Fast checklist for layout bugs

**Short answer**:
- Is the correct element selected (inspect DOM)?
- Check the box model (margin/padding/border).
- Check `display` (block/inline/flex/grid) and `position`.
- Check overflow constraints (`overflow: hidden`, scroll containers).
- Check `min-width/min-height` constraints (flex shrink gotchas).
- Check stacking context/z-index for “behind” issues.

---

### 2) “Why isn’t my `z-index` working?”

**Short answer**:
- The element must participate in stacking (positioned / stacking context rules).
- A parent stacking context can trap children below siblings in other contexts.
- Check transforms/opacity on ancestors that create new stacking contexts.

---

## Performance debugging (jank, slow pages)

### 1) What’s your approach to “scrolling is janky”?

**Short answer**:
- Use Performance panel to find long tasks and frequent layout/paint.
- Look for layout thrashing (read layout + write styles repeatedly).
- Reduce work on scroll (debounce/throttle, passive listeners).
- Virtualize large lists; avoid re-rendering huge subtrees.

---

### 2) What’s a quick way to debug main-thread blocking?

**Short answer**:
- Use Performance: identify long tasks and their call stacks.
- Look at JS bundle size and third-party scripts.
- Break up heavy work; schedule with `requestAnimationFrame` / idle time (when appropriate).

---

## Accessibility debugging

### 1) How do you debug “screen reader says button is unlabeled”?

**Short answer**:
- Inspect accessible name sources: visible text, `<label>`, `aria-labelledby`, `aria-label`.
- Use DevTools accessibility pane (if available) to confirm role/name/state.
- Ensure icon-only controls have a name.

---

### 2) How do you debug keyboard issues?

**Short answer**:
- Tab through: focus order and focus visibility.
- Ensure interactive elements are native or implement `tabindex` + key handlers correctly.
- Watch for focus traps and modals that don’t restore focus.

---

## State, storage, and auth debugging

### 1) How do you debug “login works but requests are unauthenticated”?

**Short answer**:
- Check cookie presence and attributes (`Secure`, `SameSite`, domain/path).
- Confirm requests include credentials when needed.
- Check token storage approach (avoid leaking tokens to XSS).
- Verify CORS + credentials configuration.

---

### 2) How do you debug cross-tab auth inconsistencies?

**Short answer**:
- Confirm shared auth mechanism (cookie session vs localStorage tokens).
- Add cross-tab broadcast (storage events / BroadcastChannel) for logout.
- Ensure caches are cleared/invalidated on logout.

---

## Production debugging & observability

### 1) What do you log/measure to make debugging easier?

**Short answer**:
- Correlation/request ids for API calls
- Key UI events (navigation, submit, save) with outcome and timing
- Error reporting with user/session context (safe, non-PII)
- Performance signals (LCP/CLS/INP, long tasks) sampled

---

### 2) How do you avoid “debugging by console.log” in production?

**Short answer**:
- Use structured logging and error reporting.
- Add feature flags for safe rollouts.
- Use dashboards/alerts for regression detection.

---

## Suggested practice exercises

- Reproduce a CORS failure and fix it by adjusting server headers (explain the preflight).
- Create a layout bug (flex overflow) and fix it using `min-width: 0`.
- Record a Performance trace for a slow interaction and identify the longest task.
- Debug an unlabeled icon button and fix it with an accessible name.
- Simulate a stale asset caching bug and fix it using fingerprinted filenames + cache headers.

## Links / references

- Chrome DevTools docs: https://developer.chrome.com/docs/devtools/
- web.dev: Fix long tasks: https://web.dev/articles/optimize-long-tasks
- MDN: CORS: https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
