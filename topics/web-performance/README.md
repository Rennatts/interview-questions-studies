# Web Performance — Critical Rendering Path (Foundations)

Web performance questions are common in interviews because they show whether you can build UIs that feel fast in real-world conditions (slow devices, flaky networks). This section starts with the **Critical Rendering Path (CRP)**: what must happen before the user sees pixels.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- For each concept, connect it to a metric (LCP/CLS/INP) and a concrete optimization you’d actually ship.
- Add your own notes under **Notes / variations**.

---

## Questions & answers

### 1) What is the Critical Rendering Path (CRP)?

**Short answer**: The CRP is the sequence of steps the browser takes to turn HTML/CSS/JS into pixels: **parse → build DOM/CSSOM → render tree → layout → paint → composite**. Performance work often reduces the time to complete the steps required for the first meaningful render.

---

### 2) What are the main pipeline steps from bytes to pixels?

**Short answer** (typical):
1. Download HTML
2. Parse HTML → **DOM**
3. Discover CSS/JS/images/fonts and fetch them (depending on priority)
4. Parse CSS → **CSSOM**
5. Combine DOM + CSSOM → **render tree**
6. **Layout** (reflow): compute geometry
7. **Paint**: draw pixels
8. **Composite**: assemble layers and present the frame

---

### 3) Why is CSS considered render-blocking?

**Short answer**: The browser generally delays painting until it has enough CSS to style the content (to avoid a flash of unstyled content). So CSS can delay First Paint and LCP if it’s large or loaded late.

**Practical optimizations**:
- Ship less CSS (remove unused CSS, split by route)
- Inline critical CSS for above-the-fold content (carefully)
- Use appropriate caching (`Cache-Control`) for static CSS

---

### 4) How do scripts block rendering, and what’s `defer` vs `async`?

**Short answer**:
- A plain `<script>` blocks HTML parsing while it downloads/executes.
- `defer`: downloads in parallel, executes after parsing (order preserved) → good default for most scripts.
- `async`: downloads in parallel, executes as soon as it’s ready (order not guaranteed) → good for independent scripts (analytics).

**Example**:

```html
<script src="/app.js" defer></script>
<script src="https://analytics.example/x.js" async></script>
```

---

### 5) What is the render tree, and what doesn’t get included?

**Short answer**: The render tree is the subset of DOM nodes that affect rendering, combined with computed styles. Elements with `display: none` are excluded (they take no space and don’t paint).

---

### 6) How do layout and paint costs show up in real apps?

**Short answer**:
- Layout is expensive when you frequently change geometry (or read layout in a way that forces sync layout).
- Paint can be expensive for large areas or effects like shadows/filters.
- Composite-only updates are usually fastest (often `transform`/`opacity` animations).

**Common pitfall**: layout thrashing (read layout → write styles → read again repeatedly).

---

### 7) How does the CRP relate to metrics like LCP, CLS, and INP?

**Short answer**:
- **LCP** depends on when the largest above-the-fold element can be fetched, styled, laid out, and painted.
- **CLS** relates to layout stability; shifts happen when sizes/positions change after initial layout (missing image sizes, late fonts, injected content).
- **INP** is about responsiveness; long main-thread tasks delay the next paint after interaction.

---

### 8) What are common “CRP wins” you can explain in an interview?

**Short answer**:
- Reduce render-blocking CSS and JS (split, defer, remove unused)
- Preload critical resources (fonts, hero image) when appropriate
- Avoid layout shifts (set image dimensions; reserve space)
- Prefer `transform`/`opacity` for animations
- Cache static assets aggressively and use a CDN

---

## Code splitting

### 1) What is code splitting and why does it help performance?

**Short answer**: Code splitting breaks your JavaScript bundle into smaller chunks so users download/parse/execute **only what they need right now**, reducing initial load cost and improving responsiveness.

**What it typically improves**:
- Initial load time (less JS upfront)
- Main-thread time (less parse/compile/execute)
- Often helps **LCP** and **INP** when the initial page becomes interactive sooner

---

### 2) What are common types of code splitting?

**Short answer**:
- **Route-level splitting**: each route/page has its own chunk (most common).
- **Component-level splitting**: load heavy components on demand (modals, editors, charts).
- **Vendor splitting**: separate long-lived deps so they cache well (depends on bundler/runtime).

---

### 3) What are the trade-offs / pitfalls of too much splitting?

**Short answer**:
- Too many small chunks → extra network requests and overhead.
- Waterfalls: loading chunk A triggers loading chunk B (serial), delaying UI.
- Harder debugging/observability (sourcemaps, tracing).

**Rule of thumb**: Split on meaningful boundaries (routes, major features), then measure.

---

### 4) How do `prefetch` and `preload` relate to code splitting?

**Short answer**:
- **Preload**: “this is needed soon (high priority)” → use for critical resources.
- **Prefetch**: “this might be needed later (low priority)” → good for likely next routes.

**Interview tip**: “Preload is for the current navigation’s critical path; prefetch is for probable future navigation.”

---

### 5) How does code splitting connect to the CRP?

**Short answer**: The CRP includes waiting for critical JS to download/execute before meaningful UI can render/behave. Code splitting reduces the amount of JS on the critical path, cutting down time spent in JS parse/execute and reducing render-blocking work.

---

### 6) What’s a good way to evaluate whether code splitting helped?

**Short answer**:
- Compare before/after: **LCP**, **INP**, total JS bytes, and main-thread long tasks.
- Watch for regressions: more request waterfalls, broken caching, slower repeat navigations.

---

## Bundle optimization

### 1) What does “bundle optimization” mean?

**Short answer**: Reducing the cost of shipping JavaScript (and CSS) by minimizing **bytes over the network** and **work on the main thread** (parse/compile/execute), without breaking caching or maintainability.

**Common goals**:
- Smaller initial JS on the critical path
- Fewer/shorter long tasks
- Better caching (stable chunks, fingerprinting)

---

### 2) What is tree-shaking and when does it work?

**Short answer**: Tree-shaking removes unused exports during bundling. It works best with **ES modules** and libraries that are designed to be tree-shakeable (no big side effects on import).

**Common pitfalls**:
- Importing from “barrel” files that pull in too much
- Libraries that rely on side effects (harder to shake)
- Misconfigured build that transpiles ESM too early

---

### 3) What’s the difference between minification and compression?

**Short answer**:
- **Minification**: removes whitespace/renames variables in the built files (smaller file on disk).
- **Compression** (gzip/Brotli): reduces bytes sent over the network (server/CDN level).

**Interview note**: You usually want both: minify at build time, compress at delivery time.

---

### 4) How do you find what’s bloating your bundle?

**Short answer**: Use a bundle analyzer and runtime profiling to identify:
- Largest modules/chunks
- Duplicate dependencies
- Over-included locales/polyfills
- Heavy third-party scripts

**Practical workflow**:
- Measure: total JS bytes + main-thread time
- Identify top contributors (largest chunks)
- Remove/replace/split the worst offenders

---

### 5) What are common high-impact optimizations?

**Short answer**:
- **Remove unused dependencies** (and avoid “kitchen sink” libs)
- Replace heavy libs with smaller alternatives (when it’s worth it)
- Import narrowly (e.g., avoid importing entire libraries when you need one function)
- Ensure production builds enable **tree-shaking** and minification
- Split code on meaningful boundaries (routes/features) and avoid waterfalls
- Reduce polyfills (target modern browsers when possible)

---

### 6) How does bundle optimization relate to caching?

**Short answer**: Stable, fingerprinted chunks enable long-lived caching. The goal is to avoid “everything changes, cache misses everywhere” after small code changes.

**Good practices**:
- Content-hashed filenames
- Keep vendor/runtime chunks stable when possible
- Avoid unnecessary churn in entry chunks

---

### 7) What is “third-party script cost” and how do you control it?

**Short answer**: Third-party scripts often add significant JS bytes and main-thread work. Control it by deferring non-critical scripts, loading on interaction, or removing/replacing vendors that aren’t pulling their weight.

**Interview-friendly line**: “Every third-party tag is a performance budget decision.”

---

## Lazy loading

### 1) What is lazy loading?

**Short answer**: Lazy loading defers loading work (bytes, decode, execution) until it’s likely to be needed—typically when something is about to enter the viewport or a user triggers an action.

**Goal**: Reduce initial page cost and keep the critical path focused on above-the-fold content.

---

### 2) What should you lazy load (common targets)?

**Short answer**:
- **Below-the-fold images** and heavy media
- **Iframes/embeds** (maps, videos)
- **Non-critical components** (modals, editors, charts)
- **Routes/features** that aren’t needed on first load

**Rule of thumb**: Don’t lazy-load content that determines **LCP**.

---

### 3) What are the main browser primitives for lazy loading?

**Short answer**:
- `loading="lazy"` for images and iframes
- `IntersectionObserver` for custom “load when near viewport” logic
- Dynamic imports for JS (`import(...)`) (framework/bundler dependent)

**Examples**:

```html
<img src="/gallery/1.jpg" alt="Gallery item" loading="lazy" decoding="async" width="800" height="600" />
<iframe src="https://maps.example/embed" loading="lazy" title="Map"></iframe>
```

---

### 4) What are common lazy-loading pitfalls?

**Short answer**:
- Lazy-loading the hero/LCP image → worse LCP.
- Not reserving space for images/iframes → **CLS** (layout shift).
- Loading too late (only when visible) → users see blank placeholders; use thresholds/preload near viewport.

**Fixes**:
- Set `width`/`height` (or aspect ratio) for media.
- For near-viewport content, start loading slightly before it appears (IntersectionObserver root margin).

---

### 5) How does lazy loading relate to INP and main-thread work?

**Short answer**: Deferring heavy JS and third-party embeds reduces main-thread contention, which often improves responsiveness (INP). But poorly implemented lazy loading can cause jank if it does heavy work at scroll time.

**Best practice**:
- Keep scroll-time work minimal; schedule heavy work during idle time when possible.

---

## Caching strategies

### 1) What’s the goal of caching in web performance?

**Short answer**: Reduce repeat work by reusing previously fetched resources, improving load time and resilience while lowering server load and bandwidth.

---

### 2) What are the main layers of caching you should know?

**Short answer**:
- **Browser HTTP cache** (controlled by response headers)
- **CDN / edge cache** (shared cache close to users)
- **Service Worker cache** (programmable Cache Storage)
- **Application caches** (in-memory, IndexedDB; for data more than static assets)

---

### 3) What is `Cache-Control` and how do you use it?

**Short answer**: `Cache-Control` tells caches how to store and reuse a response.

**Common directives**:
- `max-age`: how long the response is fresh
- `s-maxage`: freshness for shared caches (CDNs)
- `public` / `private`
- `no-store`: do not store (sensitive)
- `no-cache`: can store, but must revalidate before reuse
- `immutable`: won’t change during its lifetime (great with fingerprinted assets)

**Example (fingerprinted static asset)**:

```http
Cache-Control: public, max-age=31536000, immutable
```

---

### 4) What are ETag and revalidation (`If-None-Match`)?

**Short answer**: ETags let the browser ask “has this changed?” If not, the server returns **304 Not Modified**, saving bytes while still confirming freshness.

**When it’s useful**:
- For resources you can’t make immutable (HTML documents, non-fingerprinted assets).

---

### 5) Cache busting: how do you cache static assets safely?

**Short answer**: Use **content hashing** in filenames (e.g., `app.3f9a1c.js`) and serve them with long-lived cache headers (`immutable`). When content changes, the filename changes, so users naturally fetch the new version.

---

### 6) HTML vs JS/CSS caching: what’s a common approach?

**Short answer**:
- Cache **JS/CSS/images** aggressively when fingerprinted.
- Cache **HTML** more conservatively (shorter TTL + revalidation) because it changes more often and controls what assets load.

**Interview framing**: “HTML is the entry point; keep it fresh. Fingerprinted assets can be cached for a year.”

---

### 7) CDN caching: what should you know?

**Short answer**: CDNs cache content at the edge to reduce latency. You need a strategy for:
- Which paths are cacheable
- Purge/invalidation (or rely on fingerprinting)
- Correct `Cache-Control` / `s-maxage`
- Varying by headers (`Vary`) when necessary (but don’t overuse it)

---

### 8) How do Service Worker caching strategies fit in?

**Short answer**: A Service Worker can implement strategies like:
- **cache-first** for static assets
- **network-first** for navigation/data that must be fresh
- **stale-while-revalidate** for a fast UI with background refresh

**Trade-off**: Great UX and offline support, but you must manage update behavior and cache invalidation carefully.

---

## Lighthouse metrics (LCP, CLS, FID)

### 1) What is LCP (Largest Contentful Paint)?

**Short answer**: LCP measures when the **largest content element** in the viewport (often a hero image or large heading) is rendered. It’s a key “how fast did the main content appear?” metric.

**Common causes of poor LCP**:
- Slow server response / high TTFB
- Render-blocking CSS/JS delaying first render
- Slow loading of the LCP resource (hero image/font)
- Heavy main-thread work delaying rendering

**Typical targets (Core Web Vitals guidance)**:
- Good: \( \le 2.5s \)
- Needs improvement: \( 2.5s\text{–}4.0s \)
- Poor: \( > 4.0s \)

**Practical improvements**:
- Reduce render-blocking resources (split, `defer`, remove unused CSS/JS)
- Optimize and prioritize hero images (proper sizing, CDN, consider preload when appropriate)
- Improve TTFB (caching, CDN, server optimization)

---

### 2) What is CLS (Cumulative Layout Shift)?

**Short answer**: CLS measures **visual stability**: how much content shifts around unexpectedly during the page’s lifetime.

**Common causes of CLS**:
- Images/iframes without reserved dimensions
- Late-loading web fonts causing text reflow
- Injecting content above existing content (banners, ads) without reserving space

**Typical targets**:
- Good: \( \le 0.1 \)
- Needs improvement: \( 0.1\text{–}0.25 \)
- Poor: \( > 0.25 \)

**Practical improvements**:
- Always reserve space (`width`/`height`, aspect ratio) for media
- Avoid inserting UI above existing content; reserve slots for dynamic content
- Use sensible font loading strategies to reduce reflow

---

### 3) What is FID (First Input Delay)?

**Short answer**: FID measures the delay between a user’s first interaction (tap/click/key) and when the browser can start running the event handler. It’s essentially a measure of **main-thread blocking** at the moment of first input.

**Important note (interview-relevant)**:
- **FID has been replaced by INP** in Core Web Vitals for most modern guidance, because INP better represents overall interaction responsiveness. You still might see FID in older Lighthouse discussions.

**Common causes of poor FID**:
- Long JS tasks during/after load (heavy bundle, parsing/execution)
- Third-party scripts blocking the main thread

**Typical targets (historical CWV guidance)**:
- Good: \( \le 100ms \)
- Needs improvement: \( 100\text{–}300ms \)
- Poor: \( > 300ms \)

**Practical improvements**:
- Reduce JS (code splitting, remove unused deps)
- Break up long tasks (yield to main thread, schedule work)
- Defer/limit third-party scripts

---

### 4) How do these metrics tie back to the critical rendering path?

**Short answer**:
- **LCP** improves when the CRP is shorter and the LCP resource is prioritized and fast.
- **CLS** improves when layout is stable (space reserved, fewer late style/layout changes).
- **FID/INP** improve when the main thread isn’t blocked by long tasks, especially around interaction time.

---

## Web performance (advanced)

### 1) INP (Interaction to Next Paint): what is it and how do you improve it?

**Short answer**: INP measures how quickly the UI responds to user interactions across the page’s lifetime (not just the first input). It’s strongly affected by **long tasks** and heavy synchronous work.

**High-leverage improvements**:
- Reduce long tasks (split work, avoid heavy sync computation on the main thread)
- Defer non-critical JS (third parties, large features)
- Minimize re-render work (memoization where it matters, component splitting)

---

### 2) What are “long tasks” and why do they matter?

**Short answer**: A long task is main-thread work that blocks the UI long enough to cause noticeable input/animation delay. Long tasks hurt INP and “feel”.

**Typical sources**:
- Large JS bundles parsing/executing
- Expensive renders (large lists, repeated recalculation)
- Heavy JSON processing on the main thread

---

### 3) Virtualization: when is it necessary?

**Short answer**: When rendering large lists/grids causes slow initial render or interaction jank. Virtualization renders only visible items (plus a buffer), reducing DOM size and render cost.

**Interview pitfall**: Virtualization changes UX details (scroll-to, dynamic heights, a11y), so you need careful implementation.

---

### 4) Image optimization beyond “compress it”

**Short answer**:
- Serve correctly sized responsive images (`srcset`/`sizes`)
- Use modern formats when possible (WebP/AVIF)
- Don’t lazy-load the LCP/hero image
- Always reserve space (avoid CLS)

---

### 5) Fonts: what are common performance issues and fixes?

**Short answer**:
- Web fonts can delay text rendering or cause layout shifts.
- Fix with careful font loading strategy and by limiting font weights/variants.

**Practical checklist**:
- Use fewer families/weights
- Consider preloading critical fonts (carefully)
- Use `font-display` strategy to balance FOIT/FOUT

---

### 6) Third-party scripts: what’s the performance strategy?

**Short answer**:
- Audit: do we need it?
- Defer/load on interaction when possible
- Isolate and measure their long-task impact
- Treat tags as budget decisions (bytes + main-thread time)

---

### 7) Performance budgets: what are they and how do you use them?

**Short answer**: Budgets are guardrails (max JS bytes, max LCP/INP regression, max third-party cost) that prevent performance from slowly degrading.

**Examples**:
- “Initial JS ≤ X KB (gz)”
- “INP p75 ≤ Y ms”
- “No new third-party without review”

---

### 8) Lab vs field data: what’s the difference?

**Short answer**:
- **Lab** (Lighthouse, local profiling): controlled, great for debugging.
- **Field** (RUM/real users): real-world, great for prioritization and regression detection.

**Interview framing**: “Use lab to diagnose; use field to decide what matters.”

---

## Suggested practice exercises

- Take a simple page and identify which resources are on the critical path; list what you’d `defer`, split, or inline.
- Create a CLS demo (missing image size) and fix it with explicit dimensions.
- Compare LCP between (a) blocking script, (b) `defer` script, and explain what changes.
- Choose a “heavy” feature (chart editor, markdown preview, code editor) and split it behind a user action; verify the initial bundle is smaller and interaction stays smooth.
- Run a bundle analyzer and list the top 5 contributors; pick one and reduce it (remove/replace/split) and validate improvements in JS bytes + long tasks.
- Add `loading="lazy"` to below-the-fold images, but ensure the hero image loads eagerly; measure the impact on LCP and total bytes.
- Configure caching headers for fingerprinted assets vs HTML and explain the trade-offs; verify with DevTools “Network” caching indicators.
- Pick one page and run Lighthouse. For each metric (LCP/CLS/FID or INP), identify the top 1–2 causes and propose a fix you can validate.
- Pick a slow list UI and add virtualization; measure improvements in interaction responsiveness.
- Audit third-party scripts: identify one that causes long tasks and propose a loading strategy change (defer/on-interaction/remove).

## Links / references

- MDN: Critical rendering path: https://developer.mozilla.org/en-US/docs/Web/Performance/Critical_rendering_path
- web.dev: Optimize LCP: https://web.dev/articles/optimize-lcp
- web.dev: Debug CLS: https://web.dev/articles/debug-layout-shifts
- web.dev: Reduce JavaScript: https://web.dev/articles/reduce-javascript
- web.dev: Minify resources: https://web.dev/articles/minify-css
- MDN: `loading` attribute: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#loading
- web.dev: Lazy loading images: https://web.dev/articles/lazy-loading-images
- MDN: HTTP caching: https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching
- web.dev: Cache-Control: https://web.dev/articles/http-cache
- web.dev: LCP: https://web.dev/articles/lcp
- web.dev: CLS: https://web.dev/articles/cls
- web.dev: FID: https://web.dev/articles/fid
- web.dev: INP: https://web.dev/articles/inp
- web.dev: Optimize long tasks: https://web.dev/articles/optimize-long-tasks
