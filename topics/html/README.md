# HTML — Semantic HTML (Foundations)

Semantic HTML is a frequent interview topic because it impacts **accessibility**, **SEO**, **maintainability**, and how well assistive tech can understand your UI. This section starts with the core semantic elements and the decisions you should be able to explain.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- When possible, include **the user impact** (screen readers, keyboard users, SEO, styling/hooks for JS).
- Add your own notes under **Notes / variations**.
- For practice, build tiny examples in `exercises/` (or your own sandbox) and verify with DevTools + a screen reader if you can.

---

## Questions & answers

### 1) What is Semantic HTML?

**Short answer**: Semantic HTML means using elements that describe the **meaning/role** of content (e.g., `<header>`, `<nav>`, `<main>`, `<article>`) instead of relying on generic `<div>`/`<span>` for everything.

**Why it matters**:
- **Accessibility**: assistive technologies rely on semantics to provide structure and navigation.
- **SEO & sharing**: better machine readability can help indexing and previews.
- **Maintainability**: clearer markup makes layout and intent easier to understand.

**Example**:

```html
<!-- non-semantic -->
<div class="top">
  <div class="menu">...</div>
</div>

<!-- semantic -->
<header>
  <nav aria-label="Primary">...</nav>
</header>
```

---

### 2) When should you use `<button>` vs `<a>`?

**Short answer**:
- Use `<a href="...">` for **navigation** (go to a new URL / location).
- Use `<button type="button">` for **actions** (submit, open dialog, toggle state, run JS).

**Key behaviors**:
- Links are focusable and activate navigation; they support “open in new tab”, copy link address, etc.
- Buttons support `disabled`, and are the correct control for UI actions.

**Example**:

```html
<a href="/settings">Go to settings</a>
<button type="button" aria-pressed="false">Toggle</button>
```

**Notes / variations**:
- Avoid “fake buttons” (`<div role="button">`) unless you have a strong reason; you’ll re-implement keyboard behavior and states.

---

### 3) What are landmarks and why are they important?

**Short answer**: Landmarks are semantic regions like `<header>`, `<nav>`, `<main>`, `<aside>`, and `<footer>` that help users (especially screen reader users) quickly jump around the page.

**Good defaults**:
- One `<main>` per page.
- Use `<nav>` for major navigation regions (often with `aria-label` if there are multiple).
- Don’t wrap *everything* in landmarks; use them to describe meaningful regions.

**Example**:

```html
<header>...</header>
<nav aria-label="Primary">...</nav>
<main>
  <article>...</article>
</main>
<footer>...</footer>
```

---

### 4) When should you use `<section>` vs `<div>` vs `<article>`?

**Short answer**:
- `<article>`: a **self-contained** piece of content that could stand alone (blog post, card with full content, comment).
- `<section>`: a **thematic grouping** of content, typically with a heading (a chapter-like chunk).
- `<div>`: a generic container when there’s **no semantic meaning** to express.

**Rule of thumb**:
- If it would have a heading in an outline, it’s probably a `<section>`.
- If it could be syndicated / independently distributed, it’s probably an `<article>`.

---

### 5) What’s the right way to structure headings (`<h1>`…`<h6>`)?

**Short answer**: Headings should represent a meaningful hierarchy. Use them to communicate structure, not to “get a font size”.

**Common guidance**:
- Typically one `<h1>` for the page (or major view), then nested headings for subsections.
- Don’t skip levels just for styling (e.g., jumping from `<h2>` to `<h4>`).

**Example**:

```html
<main>
  <h1>Account</h1>
  <section>
    <h2>Security</h2>
    <h3>Password</h3>
  </section>
</main>
```

---

### 6) When do you need ARIA if you already use semantic HTML?

**Short answer**: Prefer native semantics first. Use ARIA mainly to **label**, **describe**, or express **state** when HTML alone isn’t enough.

**Common, correct uses**:
- `aria-label` / `aria-labelledby` to name controls/regions
- `aria-describedby` for extra help/error text
- `aria-expanded`, `aria-pressed`, `aria-current` to expose state

**Common pitfalls**:
- Adding `role` that duplicates native semantics (e.g., `role="button"` on a `<button>`).
- Using ARIA to “fix” non-semantic elements instead of using the right element.

---

### 7) What’s the difference between `<strong>` and `<b>`, and `<em>` and `<i>`?

**Short answer**:
- `<strong>` and `<em>` convey **importance/emphasis** (semantic meaning).
- `<b>` and `<i>` are mostly **stylistic** (bring attention / alternate voice) without implying importance/emphasis.

**Rule of thumb**: Use `<strong>`/`<em>` when the meaning matters; style with CSS rather than using `<b>`/`<i>` as “bold/italic buttons”.

---

### 8) What are semantic form best practices?

**Short answer**: Use native form elements and wire them correctly: labels, types, constraints, and error messaging.

**Checklist**:
- Every input has an associated `<label>` (or an accessible name).
- Use correct `type` (`email`, `password`, `number`, etc.) for better keyboards + validation.
- Prefer native constraints (`required`, `minlength`, `pattern`) but pair them with clear error text.
- Group related controls with `<fieldset>` + `<legend>`.

**Example**:

```html
<fieldset>
  <legend>Notifications</legend>

  <label>
    <input type="checkbox" name="email_updates" />
    Email updates
  </label>
</fieldset>
```

---

### 9) What is the `<time>` element used for?

**Short answer**: `<time>` marks up dates/times in a machine-readable way, typically using the `datetime` attribute.

**Example**:

```html
<time datetime="2026-04-28">Apr 28, 2026</time>
```

---

### 10) What is the `<figure>` / `<figcaption>` pattern?

**Short answer**: Use `<figure>` to group media (image, diagram, code sample) with an optional caption via `<figcaption>`.

**Example**:

```html
<figure>
  <img src="architecture.png" alt="High-level architecture diagram" />
  <figcaption>Client → API → Database request flow.</figcaption>
</figure>
```

---

## Forms & validation

### 1) How do you correctly associate a label with an input?

**Short answer**: Use either (a) an explicit label via `for`/`id`, or (b) wrap the input inside the `<label>`.

**Examples**:

```html
<!-- explicit association -->
<label for="email">Email</label>
<input id="email" name="email" type="email" autocomplete="email" />

<!-- implicit association -->
<label>
  Email
  <input name="email" type="email" autocomplete="email" />
</label>
```

**Notes / variations**:
- If you can’t show a visible label, prefer `aria-label` or `aria-labelledby` (but visible labels are best UX).

---

### 2) What’s “native constraint validation” in HTML?

**Short answer**: Browsers can validate form controls automatically based on attributes like `required`, `type="email"`, `min`, `max`, `minlength`, `maxlength`, `pattern`, and can block submission when invalid.

**Key APIs**:
- `checkValidity()` / `reportValidity()`
- `validity` (ValidityState) and `validationMessage`
- `setCustomValidity(message)` for custom validation errors

**Example**:

```html
<form>
  <label for="age">Age</label>
  <input id="age" name="age" type="number" min="18" required />
  <button>Submit</button>
</form>
```

---

### 3) When would you disable native browser validation (`novalidate`)?

**Short answer**: Use `novalidate` when you are implementing a fully custom validation UX (custom error placement, async validation) and want to avoid the browser’s default popups — but you should still use native constraints where they help and mirror errors accessibly.

**Example**:

```html
<form novalidate>
  <!-- custom validation UI -->
</form>
```

---

### 4) How do you implement accessible inline error messages?

**Short answer**: Render an error message in the DOM, connect it to the input with `aria-describedby`, and set `aria-invalid="true"` when invalid. Prefer also using `required` / constraints so the browser’s validity state matches.

**Example**:

```html
<label for="email">Email</label>
<input
  id="email"
  name="email"
  type="email"
  required
  aria-invalid="true"
  aria-describedby="email-error"
/>
<p id="email-error">Enter a valid email address.</p>
```

**Notes / variations**:
- For “error summary” at the top, link each item to the invalid field with anchors and also keep field-level messages.

---

### 5) What’s the difference between `required`, `minlength`, and `pattern`?

**Short answer**:
- `required`: value must be present (non-empty).
- `minlength`: string must have at least N characters (applies to text-like inputs).
- `pattern`: regex constraint (string must match), often used for formatting constraints.

**Pitfalls**:
- Don’t use `pattern` for things better expressed via `type` (e.g., email).
- Keep `pattern` aligned with what users can reasonably type; it’s easy to over-restrict.

---

### 6) `type="email"` vs custom regex: which should you use?

**Short answer**: Prefer `type="email"` for basic email validation (plus `multiple` when needed). Use custom validation only when product requirements truly demand stricter rules.

**Why**:
- Better mobile keyboard suggestions.
- Built-in validity + localized error messaging.

---

### 7) How do you validate fields that depend on each other (e.g., “confirm password”)?

**Short answer**: Use custom validation logic and surface it with `setCustomValidity` (or your framework’s error state), while still using native attributes for simple constraints.

**Example (JS conceptually)**:

```js
confirmInput.setCustomValidity(
  confirmInput.value === passwordInput.value ? "" : "Passwords must match."
);
```

---

### 8) What should you know about `name`, `value`, and form submission?

**Short answer**:
- Only controls with a `name` are submitted.
- Disabled controls are not submitted.
- For checkboxes/radios, submission depends on checked state.

**Common gotcha**:
- A visually correct input without `name` won’t reach the server.

---

### 9) Checkbox and radio groups: what’s the semantic structure?

**Short answer**: Use `<fieldset>`/`<legend>` to label the group, then each control gets its own label. Radios in a group share the same `name`.

**Example**:

```html
<fieldset>
  <legend>Plan</legend>

  <label><input type="radio" name="plan" value="free" /> Free</label>
  <label><input type="radio" name="plan" value="pro" /> Pro</label>
</fieldset>
```

---

### 10) Client-side vs server-side validation: what’s the right mental model?

**Short answer**:
- **Client-side** validation is UX (fast feedback), but not security.
- **Server-side** validation is the source of truth and must be enforced.

**Interview-friendly line**: “Validate on the client for speed, validate on the server for correctness and security.”

---

## Accessibility basics (ARIA roles)

### 1) What is ARIA and when should you use it?

**Short answer**: ARIA (Accessible Rich Internet Applications) is a set of attributes that help communicate **role**, **name**, **state**, and **properties** to assistive technologies—mainly for custom UI widgets and dynamic behavior.

**Rule #1 (native-first)**:
- Prefer **semantic HTML** first (buttons, links, form controls, headings, landmarks).
- Add ARIA when you need to provide an **accessible name**, **relationship**, or **state** that native HTML doesn’t already expose.

**Common safe uses**:
- Naming/labeling: `aria-label`, `aria-labelledby`
- Descriptions: `aria-describedby`
- State: `aria-expanded`, `aria-pressed`, `aria-current`, `aria-selected`

---

### 2) What does “role” mean? What’s an implicit role?

**Short answer**: A role tells assistive tech what an element **is** (button, checkbox, navigation, etc.). Many HTML elements already have **implicit roles**—you usually shouldn’t override them.

**Examples**:
- `<button>` has an implicit `button` role.
- `<a href="...">` has an implicit `link` role.
- `<nav>` maps to a navigation landmark.

**Pitfalls**:
- Adding redundant roles (e.g., `role="button"` on a `<button>`).
- Using a role without implementing expected keyboard behavior and states.

---

### 3) What is an “accessible name” and how do you provide it?

**Short answer**: The accessible name is what screen readers announce as the control’s label (e.g., “Search, edit text”).

**Common sources (roughly best-first)**:
- Visible `<label>` (for form controls)
- Element text content (e.g., `<button>Save</button>`)
- `aria-labelledby` (references another element’s text)
- `aria-label` (last resort: no visible label)

**Example**:

```html
<button aria-label="Close dialog">×</button>
```

---

### 4) Landmark roles: what are they and how do multiple navs work?

**Short answer**: Landmarks let users jump to major regions (navigation, main, banner, contentinfo, complementary). If you have multiple landmarks of the same type (e.g., two navs), label them.

**Example**:

```html
<nav aria-label="Primary">...</nav>
<nav aria-label="Footer">...</nav>
```

---

### 5) What’s wrong with `<div role="button">`?

**Short answer**: It can be made accessible, but it’s easy to get wrong. Native `<button>` already supports keyboard interaction, disabled states, and correct semantics.

**If you must** (know what you’re signing up for):
- Make it focusable (`tabindex="0"`)
- Support keyboard activation (Enter + Space)
- Convey state (`aria-pressed`, `aria-disabled`) when relevant

---

### 6) Role/state pairs you should know (common interview targets)

**Short answer**: ARIA roles often need corresponding state attributes to be meaningful.

**Common pairs**:
- Toggle button: `aria-pressed="true|false"` (on a `<button>`)
- Disclosure (accordion): `aria-expanded="true|false"` on the trigger + relationship to content
- Tabs: `role="tablist"`, `role="tab"`, `role="tabpanel"` + `aria-selected` + roving focus
- Current location: `aria-current="page|step|location|..."` (often on a link)

---

### 7) `aria-hidden` vs visually hidden text: what’s the difference?

**Short answer**:
- `aria-hidden="true"` removes an element (and its subtree) from the accessibility tree.
- Visually hidden text (CSS) is still available to screen readers and is useful for extra context.

**Pitfall**:
- Don’t put focusable elements inside `aria-hidden="true"` containers.

---

### 8) What is keyboard focus management and what does ARIA not do?

**Short answer**: ARIA doesn’t add keyboard behavior. You still need to manage focus order and keyboard interactions with correct elements and/or JS.

**Basics**:
- Use native focusable elements where possible.
- Avoid positive `tabindex` values; prefer natural order and patterns like “roving tabindex” for composite widgets.
- On dialogs/menus, move focus intentionally and return focus to the trigger on close.

---

## SEO fundamentals

### 1) How does SEO relate to HTML (as a front-end engineer)?

**Short answer**: Search engines largely rely on **HTML structure**, **links**, and **metadata** to understand pages. Good semantics improves comprehension for both crawlers and assistive tech; performance and rendering strategy affect what gets indexed.

**Core idea**: Make the page understandable with “just HTML” (progressive enhancement), then layer JS on top.

---

### 2) What are the most important `<head>` tags for SEO?

**Short answer**: `title`, meta description, canonical URL, robots directives (when needed), and correct viewport for mobile UX.

**Example**:

```html
<title>Pricing — Acme</title>
<meta name="description" content="Compare plans and pricing for Acme." />
<link rel="canonical" href="https://example.com/pricing" />
<meta name="robots" content="index,follow" />
```

**Notes / variations**:
- The meta description often influences snippet text, but it’s not a direct ranking “boost” you control; still worth writing well.

---

### 3) What’s the purpose of a canonical URL?

**Short answer**: The canonical URL tells search engines which version of a page is the preferred “source” when multiple URLs have duplicate/near-duplicate content (query params, sorting, UTM tracking, etc.).

**Common cases**:
- `/products?sort=price` vs `/products?sort=popular`
- multiple paths that render the same content

---

### 4) What are `robots` directives and when do you use `noindex`?

**Short answer**: `robots` directives control indexing behavior. Use `noindex` for pages that shouldn’t appear in search results (admin, internal search results, staging content, thin pages).

**Example**:

```html
<meta name="robots" content="noindex,nofollow" />
```

**Pitfall**:
- Don’t “hide” sensitive content with `noindex`; it’s not access control.

---

### 5) Why do headings and semantic structure matter for SEO?

**Short answer**: Headings (`<h1>`…`<h6>`) and semantic regions help define the page topic and content hierarchy. It improves scannability and machine understanding.

**Practical guidance**:
- Use headings for structure, not styling.
- Ensure the main topic is clearly represented near the top (usually in `title` + `<h1>`).

---

### 6) What’s the SEO impact of client-side rendering (CSR) vs SSR/SSG?

**Short answer**:
- With **SSR/SSG**, crawlers immediately see meaningful HTML (fast, reliable indexing).
- With **CSR**, content may depend on JS execution; indexing can be slower or less reliable for complex apps.

**Interview framing**: “If critical content is important for search, ship it in the initial HTML via SSR/SSG when possible.”

---

### 7) What are Open Graph / Twitter meta tags and why do they matter?

**Short answer**: They control how a page looks when shared on social platforms (preview title, description, image). Not traditional SEO ranking signals, but important for click-through and sharing.

**Example**:

```html
<meta property="og:title" content="Acme Pricing" />
<meta property="og:description" content="Plans for teams of every size." />
<meta property="og:image" content="https://example.com/og/pricing.png" />
<meta name="twitter:card" content="summary_large_image" />
```

---

### 8) How do internal links affect SEO?

**Short answer**: Links define site structure and help crawlers discover pages. Good internal linking improves crawlability and helps distribute importance across the site.

**Key points**:
- Use real `<a href="...">` links for navigation (not click handlers on `<div>`).
- Use descriptive link text (avoid “click here”).

---

### 9) What’s structured data (schema.org) and when would you add it?

**Short answer**: Structured data is machine-readable metadata (often JSON-LD) describing entities (Product, Article, Breadcrumb, FAQ). It can enable “rich results” in search.

**Example (JSON-LD conceptually)**:

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Acme",
  "url": "https://example.com"
}
</script>
```

---

### 10) What HTML-level pitfalls commonly hurt SEO?

**Short answer**:
- Missing/duplicated `<title>` tags
- Multiple pages with duplicate content and no canonical strategy
- Navigation that isn’t real links (hurts discovery)
- Shipping “empty” HTML shells for important pages (CSR-only)
- Slow pages and poor Core Web Vitals (often correlates with worse outcomes)
- Incorrect `noindex` on production pages

---

## Media elements

### 1) Images: what’s the difference between `<img>` and CSS background images?

**Short answer**:
- Use `<img>` for **content images** that convey meaning and need `alt`, responsive loading, and SEO/discoverability.
- Use CSS `background-image` for **decorative** visuals that are part of presentation (no meaningful alt text).

**Rule of thumb**: If you’d describe it to a blind user, it’s probably an `<img>`.

---

### 2) How do you write good `alt` text? When should `alt=""` be used?

**Short answer**:
- `alt` should convey the **purpose** of the image in context, not a literal description of pixels.
- Use `alt=""` (empty) for **decorative** images so screen readers skip them.

**Examples**:

```html
<!-- meaningful -->
<img src="/chart.png" alt="Revenue grew from $1M to $3M in 2025." />

<!-- decorative -->
<img src="/divider.svg" alt="" />
```

**Pitfalls**:
- Don’t put “image of …” (screen readers already announce it).
- Don’t omit `alt` on content images.

---

### 3) Responsive images: what are `srcset` and `sizes`?

**Short answer**: `srcset` provides multiple image candidates, and `sizes` tells the browser how wide the image will be in the layout so it can pick the best file.

**Example**:

```html
<img
  src="/hero-800.jpg"
  srcset="/hero-400.jpg 400w, /hero-800.jpg 800w, /hero-1600.jpg 1600w"
  sizes="(max-width: 600px) 100vw, 600px"
  alt="Team collaborating in an office"
/>
```

---

### 4) When do you use `<picture>`?

**Short answer**: Use `<picture>` when you need **art direction** (different crops at different breakpoints) or to serve different formats (e.g., AVIF/WebP with JPEG fallback).

**Example**:

```html
<picture>
  <source type="image/avif" srcset="/hero.avif" />
  <source type="image/webp" srcset="/hero.webp" />
  <img src="/hero.jpg" alt="Product screenshot" />
</picture>
```

---

### 5) Image performance basics: `loading`, `decoding`, and dimensions

**Short answer**:
- `loading="lazy"` defers offscreen images.
- `decoding="async"` hints decode can happen asynchronously.
- Always include `width`/`height` (or equivalent CSS sizing) to reduce layout shift (CLS).

**Example**:

```html
<img
  src="/avatar.jpg"
  alt="Renata"
  width="96"
  height="96"
  loading="lazy"
  decoding="async"
/>
```

**Notes / variations**:
- Avoid lazy-loading the LCP/hero image; prioritize it instead.

---

### 6) Video and audio: what are the key elements and attributes?

**Short answer**:
- `<video>` / `<audio>` provide native playback with `<source>` fallbacks.
- Common attributes: `controls`, `autoplay` (often blocked unless muted), `muted`, `loop`, `playsinline`, `preload`.

**Example**:

```html
<video controls playsinline preload="metadata" width="640">
  <source src="/demo.webm" type="video/webm" />
  <source src="/demo.mp4" type="video/mp4" />
  Sorry, your browser doesn’t support embedded video.
</video>
```

---

### 7) Captions and accessibility: what’s `<track>` for?

**Short answer**: `<track>` adds timed text (captions/subtitles/descriptions). Captions are a major accessibility requirement for video with speech.

**Example**:

```html
<video controls>
  <source src="/talk.mp4" type="video/mp4" />
  <track kind="captions" src="/talk.en.vtt" srclang="en" label="English" default />
</video>
```

**Pitfall**:
- “Autogenerated captions” often aren’t sufficient for accessibility without review.

---

### 8) Embeds and iframes: what should you care about?

**Short answer**:
- Use `<iframe>` for third-party embeds; provide a meaningful `title`.
- Sandbox untrusted content with `sandbox` and limit capabilities via `allow`.

**Example**:

```html
<iframe
  src="https://www.youtube.com/embed/xyz"
  title="Product demo video"
  loading="lazy"
  referrerpolicy="strict-origin-when-cross-origin"
  allow="fullscreen; picture-in-picture"
></iframe>
```

---

## Metadata (meta, canonical, robots)

### 1) What are the most important metadata tags in `<head>`?

**Short answer**: `title`, meta description, viewport, canonical, and robots directives (when needed). These affect SEO, sharing previews, and mobile UX.

**Example**:

```html
<title>Account — Acme</title>
<meta name="description" content="Manage your account settings." />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<link rel="canonical" href="https://example.com/account" />
<meta name="robots" content="index,follow" />
```

---

### 2) What’s the difference between `robots` meta and `robots.txt`?

**Short answer**:
- `robots` meta: page-level indexing directives (index/noindex, follow/nofollow).
- `robots.txt`: crawl directives for user agents (what they should fetch), not a security feature.

---

## Tables (semantic + a11y)

### 1) When should you use a table?

**Short answer**: Use `<table>` for **tabular data** (rows/columns). Don’t use tables for layout.

---

### 2) What are the core semantic building blocks of an accessible table?

**Short answer**:
- `<caption>`: table title/summary (high value for accessibility).
- `<th>` for headers with `scope="col"` / `scope="row"`.
- `<thead>`, `<tbody>`, `<tfoot>` for structure (optional but helpful).

**Example**:

```html
<table>
  <caption>Quarterly revenue</caption>
  <thead>
    <tr>
      <th scope="col">Quarter</th>
      <th scope="col">Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Q1</th>
      <td>$1.2M</td>
    </tr>
  </tbody>
</table>
```

---

## Iframe security (sandbox)

### 1) Why is embedding third-party iframes risky?

**Short answer**: Embedded content can run untrusted code, try to navigate the top page, or abuse permissions. You should restrict capabilities explicitly.

---

### 2) What does the `sandbox` attribute do?

**Short answer**: `sandbox` locks down an iframe by default and you selectively re-enable capabilities with flags (e.g., scripts, forms, same-origin, popups).

**Rule of thumb**: Start strict, then add only what you need.

**Example**:

```html
<iframe
  src="https://third-party.example/widget"
  title="Widget"
  sandbox="allow-scripts allow-forms"
></iframe>
```

---

## ARIA-by-default patterns (native-first)

### 1) What does “ARIA-by-default” mean?

**Short answer**: Prefer native elements whose semantics and keyboard behavior already match the UI. Only add ARIA for labeling/state when necessary.

**Examples**:
- Use `<button>` for actions (not `<div role="button">`).
- Use `<a href>` for navigation (not click handlers).
- Use `<details><summary>` for simple disclosure UI (when it fits).

---

### 2) What ARIA attributes are commonly correct to add on top of native HTML?

**Short answer**:
- `aria-expanded` on disclosure triggers
- `aria-current="page"` for nav links
- `aria-describedby` for help/error text
- `aria-invalid` for invalid inputs

---

## Forms edge cases (autocomplete, inputmode)

### 1) Why do `autocomplete` and `inputmode` matter?

**Short answer**: They improve UX and correctness—better keyboard layouts on mobile, better autofill, and fewer user errors.

---

### 2) Common `autocomplete` values you should know

**Short answer** (examples):
- `email`, `username`, `current-password`, `new-password`
- `name`, `given-name`, `family-name`
- `street-address`, `address-line1`, `postal-code`, `country`

**Pitfall**: Turning off autocomplete by default can harm usability; do it only for well-justified cases.

---

### 3) When do you use `inputmode`?

**Short answer**: When you want a specific mobile keyboard while keeping a text input type.

**Examples**:

```html
<input name="otp" inputmode="numeric" autocomplete="one-time-code" />
<input name="search" inputmode="search" />
```

---

## Suggested practice exercises

- Take a `<div>`-heavy snippet and refactor it to use `<header>`, `<nav>`, `<main>`, `<section>`, and `<footer>`.
- Build a “card list” and decide which wrapper should be `<article>` vs `<div>`; explain why.
- Create a form with:
  - a grouped set of checkboxes (use `<fieldset>`/`<legend>`)
  - proper labels
  - inline error text connected with `aria-describedby`
- Replace a clickable `<div>` with a real `<button>` and verify keyboard behavior.
- Build a login form that uses native constraints (`required`, `minlength`, `type="email"`), then add:
  - field-level errors
  - a top-level error summary
  - a simulated server error (e.g., “invalid credentials”) that is announced clearly
- Mark up a dataset as a proper table with `<caption>` and `<th scope=...>`.
- Embed a third-party widget in an `<iframe sandbox=...>` and justify each allowed capability.

## Links / references

- MDN HTML elements reference: https://developer.mozilla.org/en-US/docs/Web/HTML/Element
- MDN: Document and website structure: https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Structuring_content/Structuring_a_page_of_content
- WAI-ARIA Authoring Practices (APG): https://www.w3.org/WAI/ARIA/apg/
- MDN: Client-side form validation: https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms/Form_validation
- MDN: ARIA basics: https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA
- Google Search Central (SEO starter): https://developers.google.com/search/docs/fundamentals/seo-starter-guide
- MDN: Responsive images: https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Structuring_content/Responsive_images
- MDN: `<video>`: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video
- MDN: `<meta>`: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta
- MDN: `<table>`: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/table
- MDN: `<iframe>`: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe
