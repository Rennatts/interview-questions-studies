# CSS — Box Model (Foundations)

The CSS box model shows up constantly in interviews because it’s behind many “why is this misaligned / overflowing / shifting?” bugs. If you can reason about content vs padding vs border vs margin (and `box-sizing`), you can debug layout quickly.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Prefer explanations that connect to **real layout bugs** (overflow, alignment, spacing).
- Add your own notes under **Notes / variations**.

---

## Questions & answers

### 1) What is the CSS box model?

**Short answer**: Every element is a rectangle made of **content**, **padding**, **border**, and **margin** (from inside out). The browser uses these to compute layout size and spacing.

**Deep dive**:
- **Content box**: the actual content area (text, image).
- **Padding**: space between content and border; increases the visible box.
- **Border**: wraps padding + content.
- **Margin**: space outside the border; separates elements.

---

### 2) What’s the difference between `content-box` and `border-box`?

**Short answer**:
- `box-sizing: content-box` (default): `width`/`height` apply to **content only**; padding + border add extra size.
- `box-sizing: border-box`: `width`/`height` include **content + padding + border** (easier to reason about).

**Example**:

```css
.a {
  box-sizing: content-box;
  width: 200px;
  padding: 20px;
  border: 5px solid;
  /* total rendered width = 200 + 40 + 10 = 250px */
}

.b {
  box-sizing: border-box;
  width: 200px;
  padding: 20px;
  border: 5px solid;
  /* total rendered width = 200px (content shrinks to fit) */
}
```

**Notes / variations**:
- Many codebases apply:
  - `*, *::before, *::after { box-sizing: border-box; }`
  - because it makes component sizing predictable.

---

### 3) Do vertical margins collapse? When and why?

**Short answer**: Yes—**vertical** margins can collapse between block elements in normal flow (top/bottom margins combine into a single margin). Horizontal margins don’t collapse.

**Common collapse cases**:
- Between adjacent block elements (e.g., two paragraphs).
- Between a parent and its first/last child (in some layouts) when there’s no border/padding/inline content separating them.

**Common ways to prevent collapse**:
- Add `padding` or `border` to the parent.
- Create a new block formatting context (e.g., `display: flow-root`).
- Use `overflow: auto/hidden` (works but can have side effects).

---

### 4) Why does adding `padding` increase the size of my element?

**Short answer**: With the default `box-sizing: content-box`, `width` sets only the content size; padding is added outside that, increasing the total box dimensions.

**Fix**:
- Use `box-sizing: border-box`, or
- Reduce `width` / adjust layout constraints.

---

### 5) What is “margin vs padding” and how do you choose?

**Short answer**:
- **Margin** creates space **outside** the element (between siblings).
- **Padding** creates space **inside** the element (between content and border/background).

**Rule of thumb**:
- Use **padding** for internal spacing within a component.
- Use **margin** for spacing between components (often controlled by the parent layout).

---

### 6) What causes horizontal overflow (`100vw` problems)?

**Short answer**: Common causes are boxes whose computed width exceeds the viewport/container: `width: 100vw` plus scrollbar width, long unbroken strings, fixed-width children, or padding with `content-box`.

**Practical fixes**:
- Prefer `width: 100%` instead of `100vw` for page sections inside the viewport.
- Apply `box-sizing: border-box`.
- Use `overflow-wrap: anywhere;` or `word-break` for long strings.

---

### 7) How do borders and outlines differ in the box model?

**Short answer**:
- `border` affects layout size (it’s part of the box).
- `outline` does **not** take up space and doesn’t affect layout (often used for focus rings).

**Example**:

```css
button:focus-visible {
  outline: 2px solid dodgerblue;
  outline-offset: 2px;
}
```

---

### 8) What is `display: inline` vs `inline-block` vs `block` in terms of the box model?

**Short answer**:
- `block`: starts on a new line; can have width/height; vertical margins behave normally.
- `inline`: flows within text; width/height don’t apply the same way; vertical margins don’t affect line layout as you might expect.
- `inline-block`: flows inline but behaves like a block box (width/height apply).

**Notes / variations**:
- Layout issues often come from expecting `inline` elements to accept height/margins like blocks.

---

## Flexbox

Flexbox is optimized for **1-dimensional layout** (row *or* column). Interviews often focus on: axes, alignment, item sizing, and common “why won’t it shrink?” bugs.

### 1) What are the main axis and cross axis?

**Short answer**:
- **Main axis**: direction items are laid out (`flex-direction`).
- **Cross axis**: perpendicular to the main axis.

**Rule**:
- `flex-direction: row` → main axis is horizontal, cross axis is vertical.
- `flex-direction: column` → main axis is vertical, cross axis is horizontal.

---

### 2) What do `justify-content`, `align-items`, and `align-content` do?

**Short answer**:
- `justify-content`: distributes items **along the main axis** (space-between, center, etc.).
- `align-items`: aligns items **along the cross axis** (for the line).
- `align-content`: distributes **lines** along the cross axis (only matters with wrapping and extra space).

**Common pitfall**:
- Using `align-content` expecting it to align items when there is only one line; you usually want `align-items`.

---

### 3) What does `gap` do in flex layouts?

**Short answer**: `gap` adds spacing **between** flex items (and between rows/columns when wrapped) without needing margins on children.

**Example**:

```css
.row {
  display: flex;
  gap: 12px;
}
```

---

### 4) What’s the difference between `flex: 1`, `flex: auto`, and `flex: none`?

**Short answer**:
- `flex: 1` → `flex: 1 1 0%` (grow + shrink, base size 0; good for equal columns).
- `flex: auto` → `flex: 1 1 auto` (base size is the item’s size; can preserve intrinsic sizes more).
- `flex: none` → `flex: 0 0 auto` (no grow, no shrink).

**Interview tip**: Know the 3 parts: `flex: <grow> <shrink> <basis>`.

---

### 5) Why won’t my flex item shrink? (the `min-width: auto` gotcha)

**Short answer**: Flex items default to `min-width: auto`, which means they may refuse to shrink below their content’s intrinsic size (e.g., long text), causing overflow.

**Fix**:
- Set `min-width: 0` (or `min-height: 0` for column layouts) on the flex item that should be allowed to shrink.

**Example**:

```css
.container { display: flex; }
.sidebar { flex: 0 0 280px; }
.content { flex: 1; min-width: 0; } /* allows text to shrink/wrap */
```

---

### 6) Centering with Flexbox: what’s the standard pattern?

**Short answer**: Use `justify-content` and `align-items` (on the container).

**Example**:

```css
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

---

### 7) Wrapping: how do `flex-wrap` and `align-content` interact?

**Short answer**:
- `flex-wrap: wrap` allows items to flow into multiple lines.
- `align-content` controls spacing between those lines when there’s extra cross-axis space.

**Example**:

```css
.wrap {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  align-content: flex-start;
}
```

---

### 8) Common sizing patterns (practical recipes)

**Short answer**:
- **Sticky sidebar + fluid content**:
  - sidebar: `flex: 0 0 280px`
  - content: `flex: 1; min-width: 0`
- **Equal columns**:
  - children: `flex: 1` (and optionally `min-width` to control wrap)
- **Right-aligned actions**:
  - add `margin-left: auto` to the first item you want pushed to the end

**Example**:

```css
.toolbar {
  display: flex;
  gap: 8px;
}
.toolbar .spacer {
  margin-left: auto;
}
```

---

## Grid

CSS Grid is designed for **2-dimensional layout** (rows *and* columns). Interviews often test your ability to define tracks, place items, and build responsive layouts with `fr`, `minmax()`, and `auto-fit/auto-fill`.

### 1) What’s the difference between Grid and Flexbox?

**Short answer**:
- **Flexbox**: 1D layout (layout along one axis; cross-axis alignment).
- **Grid**: 2D layout (define rows and columns simultaneously).

**Rule of thumb**:
- Use Grid for page-level layouts and “cards in a matrix”.
- Use Flexbox inside components (toolbars, alignment, simple rows/columns).

---

### 2) What are grid tracks, lines, and cells?

**Short answer**:
- **Tracks** are rows/columns (`grid-template-rows/columns`).
- **Lines** are the boundaries between tracks (numbered; can be named).
- A **cell** is the intersection of a row and a column; a **grid area** can span multiple cells.

---

### 3) What does `fr` mean? How is it different from `%`?

**Short answer**: `fr` is a fractional unit of **remaining free space** in the grid container after fixed sizes are accounted for. `%` is a proportion of the container size regardless of other track sizing rules.

**Example**:

```css
.layout {
  display: grid;
  grid-template-columns: 240px 1fr;
}
```

---

### 4) What’s `minmax()` and why is it useful?

**Short answer**: `minmax(min, max)` sets a track’s minimum and maximum size, enabling responsive behavior like “don’t shrink below X, but grow if there’s room”.

**Example**:

```css
.cards {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 16px;
}
```

**Notes / variations**:
- `minmax(0, 1fr)` is a common pattern to avoid overflow from long content.

---

### 5) What are `auto-fit` and `auto-fill`? (responsive columns)

**Short answer**:
- Both are used with `repeat()` to create as many columns as fit.
- `auto-fit` collapses empty tracks (so items stretch to fill).
- `auto-fill` keeps the tracks, even if empty (can preserve gutter/track structure).

**Example**:

```css
.cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 16px;
}
```

---

### 6) Explicit grid vs implicit grid: what’s the difference?

**Short answer**:
- **Explicit grid**: what you define via `grid-template-rows/columns/areas`.
- **Implicit grid**: extra rows/columns created automatically when items are placed outside the explicit grid.

**Related properties**:
- `grid-auto-rows`, `grid-auto-columns`
- `grid-auto-flow` (row/column placement; `dense` packing)

---

### 7) How do you place items in Grid?

**Short answer**:
- By line numbers: `grid-column: 1 / 3; grid-row: 2 / 4;`
- By spans: `grid-column: span 2;`
- By named areas: `grid-template-areas` + `grid-area`

**Example (areas)**:

```css
.page {
  display: grid;
  grid-template-columns: 240px 1fr;
  grid-template-areas: "sidebar main";
  gap: 16px;
}
.sidebar { grid-area: sidebar; }
.main { grid-area: main; }
```

---

### 8) Alignment in Grid: what do `justify-items` / `align-items` control?

**Short answer**:
- `justify-items` / `align-items` align **items within their grid cells**.
- `justify-content` / `align-content` align the **grid tracks within the container** when there’s extra space.

**Common pitfall**:
- Mixing up “align items inside cells” vs “move the whole grid”.

---

## Positioning

Positioning comes up in interviews because it’s behind tooltips/menus, sticky headers, modals, and “why is this behind that?” bugs. The key is understanding the **containing block** and **stacking contexts**.

### 1) What are the `position` values and what do they do?

**Short answer**:
- `static`: default; `top/right/bottom/left` (or `inset`) don’t apply.
- `relative`: stays in normal flow, but can be offset; also becomes an anchor for absolute children.
- `absolute`: removed from normal flow; positioned relative to the nearest positioned ancestor (or initial containing block if none).
- `fixed`: removed from flow; positioned relative to the viewport (in most cases).
- `sticky`: behaves like `relative` until a scroll threshold, then behaves like `fixed` within its scroll container.

---

### 2) What does “containing block” mean for absolutely positioned elements?

**Short answer**: An absolutely positioned element uses the nearest ancestor that establishes a containing block (commonly the nearest ancestor with `position` not `static`, e.g. `relative/absolute/fixed/sticky`) as its reference for offsets.

**Common bug**: “My tooltip is positioned relative to the page, not the card.”

**Fix**:
- Put `position: relative` on the card/container you want to anchor to.

---

### 3) What do `top/right/bottom/left` and `inset` apply to?

**Short answer**: Offsets apply to positioned elements (`relative/absolute/fixed/sticky`). `inset` is shorthand for the 4 sides.

**Example**:

```css
.overlay {
  position: absolute;
  inset: 0; /* top:0; right:0; bottom:0; left:0 */
}
```

---

### 4) Why doesn’t `z-index` work sometimes?

**Short answer**: `z-index` only affects positioned elements (or elements that create stacking contexts) and is scoped by **stacking contexts**. If two elements are in different stacking contexts, their `z-index` values don’t directly compete globally.

**Common stacking context creators** (not exhaustive):
- `position` + `z-index` (for non-static positioned elements)
- `opacity < 1`
- `transform` / `filter`
- `isolation: isolate`

**Practical debugging**:
- Check which element created a new stacking context.
- Avoid “random huge z-index”; fix the stacking context boundaries.

---

### 5) `absolute` vs `fixed`: when would you use each?

**Short answer**:
- `absolute`: anchored to a specific component/section (dropdown inside header, tooltip on card).
- `fixed`: anchored to the viewport (floating action button, persistent header, toast container).

**Common pitfall**:
- A transformed parent can change how `fixed` behaves (it may act like it’s anchored to that ancestor). If you see odd fixed behavior, inspect ancestor transforms.

---

### 6) Why isn’t `position: sticky` working?

**Short answer**: Sticky needs (a) a scroll container, (b) a threshold like `top: 0`, and (c) no ancestor that prevents sticky behavior (most commonly an ancestor with `overflow: hidden/auto/scroll` that changes the scroll container, or insufficient space).

**Checklist**:
- You set `top` (or `bottom/left/right`) — e.g. `top: 0`.
- The sticky element’s parent has enough height for it to “stick”.
- You’re not accidentally sticking inside a different scroll container due to `overflow`.

---

### 7) Does `position: relative` remove an element from flow?

**Short answer**: No. The element keeps its original space in the layout; it’s just visually offset.

---

## Stacking contexts (deep dive)

Stacking contexts explain most “my dropdown is behind the header” and “z-index doesn’t work” bugs. The key idea: **z-index compares only within the same stacking context**.

### 1) What is a stacking context?

**Short answer**: A stacking context is a self-contained z-index universe. Children’s z-index values are compared **within** that context; the whole context is then stacked as a single unit against sibling contexts.

---

### 2) What commonly creates a new stacking context?

**Short answer** (common triggers):
- Positioned element with `z-index` (non-auto)
- `transform` (any non-`none`)
- `opacity < 1`
- `filter`
- `isolation: isolate`
- `position: fixed` / `sticky` (in many cases, plus their own stacking behavior)

**Practical debugging**:
- If a child can’t rise above something “outside”, check ancestors for `transform`/`opacity`/`z-index` that created a new context.

---

### 3) A reliable mental model for fixing z-index issues

**Short answer**:
- Prefer fewer contexts (don’t sprinkle `z-index` everywhere).
- Create one intentional “overlay layer” root when needed (modals/toasts).
- Avoid putting overlays inside elements that create stacking contexts (e.g., transformed parents).

---

## Layout patterns cookbook

### 1) Holy Grail layout (header / footer / sidebar / main)

**Short answer**: Use Grid for 2D page layout; it’s straightforward and stable.

**Example**:

```css
.page {
  min-height: 100dvh;
  display: grid;
  grid-template-rows: auto 1fr auto;
  grid-template-columns: 280px 1fr;
  grid-template-areas:
    "header header"
    "sidebar main"
    "footer footer";
}
.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main { grid-area: main; min-width: 0; }
.footer { grid-area: footer; }
```

---

### 2) Sticky footer (footer at bottom when content is short)

**Short answer**: Use a column layout with `min-height: 100dvh` and make the main content flex/grow.

**Example**:

```css
.app {
  min-height: 100dvh;
  display: flex;
  flex-direction: column;
}
.content { flex: 1; }
```

---

## CSS variables & tokens

### 1) What are CSS variables and why are they useful?

**Short answer**: CSS variables (custom properties) store design values (colors, spacing, radii) and can be themed at runtime. They enable consistent styling across components and architectures (plain CSS, Modules, Tailwind).

**Example**:

```css
:root {
  --space-2: 8px;
  --radius-2: 8px;
  --color-bg: #fff;
  --color-fg: #111;
}
.card {
  padding: var(--space-2);
  border-radius: var(--radius-2);
  background: var(--color-bg);
  color: var(--color-fg);
}
```

---

### 2) How do you implement theming with tokens?

**Short answer**: Override variables at a scope boundary (often on `html` or a root wrapper) using a theme attribute/class.

**Example**:

```css
html[data-theme="dark"] {
  --color-bg: #111;
  --color-fg: #f5f5f5;
}
```

---

## Container queries (deeper)

### 1) `@container` vs `@media`: what’s the key difference?

**Short answer**:
- `@media` reacts to the **viewport**.
- `@container` reacts to the **container’s size**, making components more reusable in different layouts.

---

### 2) What’s a common pitfall with container queries?

**Short answer**: Forgetting to establish a container (`container-type: inline-size`) on an ancestor, so the query never matches.

---

## Accessibility in CSS

### 1) Why is `:focus-visible` recommended?

**Short answer**: It provides a visible focus ring for keyboard users while avoiding focus outlines on mouse clicks (cleaner UX without harming accessibility).

**Example**:

```css
:focus-visible {
  outline: 2px solid dodgerblue;
  outline-offset: 2px;
}
```

---

### 2) How do you support `prefers-reduced-motion`?

**Short answer**: Reduce or disable non-essential animations and smooth scrolling for users who request less motion.

**Example**:

```css
@media (prefers-reduced-motion: reduce) {
  html:focus-within { scroll-behavior: auto; }
  * { transition-duration: 1ms !important; animation-duration: 1ms !important; }
}
```

---

## Specificity & cascade

This is one of the fastest ways to debug “why didn’t my CSS apply?” The browser resolves conflicts using the **cascade** (which rule wins) and **specificity** (how targeted the selector is).

### 1) What is the CSS cascade (high-level)?

**Short answer**: When multiple rules match the same element/property, the browser chooses the winner by considering (in order): **importance**, **origin/layer**, **specificity**, then **source order**.

**Practical mental model**:
- First decide which declarations are even “eligible” (important vs normal, author vs user-agent, layers).
- Then among eligible ones, higher specificity wins.
- If specificity ties, the later rule wins.

---

### 2) How do you calculate specificity?

**Short answer**: Think of specificity as a 3-part score:
- **IDs** (highest)
- **classes/attributes/pseudo-classes**
- **elements/pseudo-elements** (lowest)

**Examples**:
- `#header .item` beats `.item`
- `.card[data-state="open"]` beats `.card`
- `button.primary` beats `button`

**Notes / variations**:
- `:where(...)` has **zero specificity** (useful for low-specificity defaults).
- `:is(...)` takes the specificity of its most specific argument.

---

### 3) What wins: inline styles, `!important`, and normal rules?

**Short answer**:
- `!important` beats normal declarations (within the same origin/layer rules).
- Inline styles are very strong, but an author stylesheet `!important` can override a normal inline style.

**Debug tip**: If you see `!important` everywhere, you’re probably fighting specificity or architecture—consider refactoring selectors rather than escalating further.

---

### 4) If two selectors have the same specificity, what happens?

**Short answer**: **Source order** decides: the later declaration wins.

**Example**:

```css
.btn { color: black; }
.btn { color: red; } /* wins */
```

---

### 5) What’s a common specificity trap in real apps?

**Short answer**: Overly specific selectors (e.g., `#app .page .card .title`) make overrides painful and encourage `!important`.

**Better patterns**:
- Keep selectors shallow (one class is often enough).
- Prefer component-scoped classes and predictable naming over DOM-dependent selectors.
- Use cascade intentionally (base → component → variant → state).

---

## Responsive design

Responsive design is about making layouts adapt across screen sizes, input types, and content lengths. Interviews usually look for: **mobile-first thinking**, **fluid sizing**, and knowing when to use **media queries** vs modern tools like **container queries**.

### 1) What’s “mobile-first” responsive design?

**Short answer**: Start with styles for small screens by default, then add enhancements using `@media (min-width: ...)` for larger screens.

**Why it’s popular**:
- Defaults are simpler and work well on constrained screens.
- You progressively add complexity as space becomes available.

**Example**:

```css
.grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 12px;
}

@media (min-width: 768px) {
  .grid { grid-template-columns: repeat(3, minmax(0, 1fr)); }
}
```

---

### 2) How do you choose breakpoints?

**Short answer**: Choose breakpoints based on **content/layout needs**, not specific device models. Add a breakpoint when the design “breaks”.

**Good heuristics**:
- Use a small set of breakpoints (e.g., 2–4) to avoid complexity.
- Test with real content (long titles, localization, dynamic data).

---

### 3) What are fluid layouts and why do they matter?

**Short answer**: Fluid layouts use relative units and flexible constraints so UI adapts smoothly, not only at breakpoints.

**Common tools**:
- `%`, `vw`, `vh` (use carefully)
- `min()`, `max()`, `clamp()`
- Grid/Flex with `fr` and `minmax()`

**Example**:

```css
.container {
  width: min(100% - 32px, 1120px);
  margin: 0 auto;
}
```

---

### 4) How do you make typography responsive without tons of media queries?

**Short answer**: Use `clamp()` to scale font sizes within a range.

**Example**:

```css
h1 {
  font-size: clamp(1.6rem, 2.5vw + 1rem, 3rem);
  line-height: 1.1;
}
```

---

### 5) What’s the difference between `px`, `em`, and `rem` for responsive UI?

**Short answer**:
- `px`: fixed; useful for hairlines but not scalable with user font settings.
- `em`: relative to the current element’s font size (can compound).
- `rem`: relative to the root font size; predictable scaling across the page.

**Interview-friendly guidance**: Use `rem` for most spacing/typography so user zoom and base font settings scale the UI naturally.

---

### 6) How do you prevent overflow issues in responsive layouts?

**Short answer**: Let items shrink and wrap, and constrain long content.

**Practical checklist**:
- In flex layouts, add `min-width: 0` to shrinkable children.
- Use `overflow-wrap: anywhere;` for long strings (IDs, URLs).
- Prefer `max-width: 100%` for media inside containers.
- Use responsive grid patterns: `repeat(auto-fit, minmax(...))`.

---

### 7) What are container queries and when do they help?

**Short answer**: Container queries let a component adapt based on its **container size**, not the viewport—great for reusable components in unknown layouts (sidebars, cards, dashboards).

**Example**:

```css
.card {
  container-type: inline-size;
}

@container (min-width: 480px) {
  .card .content {
    display: grid;
    grid-template-columns: 160px 1fr;
    gap: 12px;
  }
}
```

---

### 8) What’s the difference between responsive layout and responsive images?

**Short answer**: Layout responsiveness is mostly CSS; responsive images involve HTML attributes like `srcset`/`sizes` (plus CSS sizing) so the browser downloads an appropriately sized image for the device and layout.

**Practical note**:
- Even with a responsive layout, a single giant `img` file can hurt performance unless you provide responsive sources.

---

## Animations & transitions

Animations show up in interviews as a proxy for performance and UX judgment: can you make motion feel good without jank, and can you keep it accessible?

### 1) What’s the difference between a transition and an animation?

**Short answer**:
- **Transition**: interpolates between two states when a property changes (e.g., hover/focus, class toggle).
- **Animation**: runs a keyframe timeline (`@keyframes`) independent of a single state change; can loop, alternate, and run complex sequences.

---

### 2) What are the key transition properties?

**Short answer**: `transition-property`, `transition-duration`, `transition-timing-function`, and `transition-delay` (or the shorthand `transition`).

**Example**:

```css
.btn {
  transition: transform 150ms ease, background-color 150ms ease;
}
.btn:hover {
  transform: translateY(-1px);
}
```

**Common pitfall**:
- Avoid `transition: all`; it can cause unexpected animations and performance issues.

---

### 3) How do CSS keyframe animations work?

**Short answer**: You define `@keyframes` with percentage stops, then apply them via `animation-*` properties (duration, easing, iteration count, direction, fill-mode, etc.).

**Example**:

```css
@keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}

.toast {
  animation: fade-in 200ms ease-out;
}
```

---

### 4) Which CSS properties are “safe” (performant) to animate?

**Short answer**: Prefer animating **`transform`** and **`opacity`**. Avoid animating layout-affecting properties (`width`, `height`, `top/left`, `margin`) when possible because they can trigger layout/reflow.

**Rule of thumb**:
- **Best**: `transform`, `opacity`
- **Sometimes OK**: `filter` (can be expensive), `box-shadow` (often expensive)
- **Avoid**: anything that changes layout/geometry frequently (can cause jank)

---

### 5) What’s easing and how do you choose timing functions?

**Short answer**: Easing controls the motion curve (acceleration/deceleration). Use ease-out for entrances, ease-in for exits, and avoid overly bouncy motion unless it matches the product tone.

**Practical defaults**:
- `ease-out` for appearing (feels snappy)
- `ease-in` for disappearing
- Use consistent durations (e.g., 150–250ms for micro-interactions)

---

### 6) Accessibility: what is `prefers-reduced-motion` and how do you support it?

**Short answer**: It’s a user preference signaling reduced motion. You should reduce/disable non-essential animations when it’s enabled.

**Example**:

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 1ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 1ms !important;
    scroll-behavior: auto !important;
  }
}
```

**Notes / variations**:
- Prefer targeted overrides when possible; global overrides can be too aggressive in some apps.

---

### 7) What is `will-change` and when should you use it?

**Short answer**: `will-change` hints that an element will change (often via `transform`/`opacity`). It can improve performance in some cases, but overusing it increases memory usage and can make performance worse.

**Rule of thumb**: Use it sparingly for elements that animate frequently, and remove it when not needed (e.g., apply shortly before the animation).

---

## CSS architecture (BEM, CSS Modules, Tailwind)

CSS architecture is about keeping styles **scalable**, **predictable**, and **maintainable** as the app and team grow. Interviews often want you to explain trade-offs: scoping, naming, overrides, bundle size, and DX.

### 1) What problem is “CSS architecture” trying to solve?

**Short answer**: Avoid global collisions, brittle overrides, and unreadable styles by establishing conventions for **scoping**, **naming**, **composition**, and **ownership** of styles.

**Common failure modes**:
- “One small change broke a different page” (global leakage)
- Specificity wars (`!important`, deep selectors)
- Inconsistent spacing/typography (no shared tokens)

---

### 2) What is BEM and why would you use it?

**Short answer**: BEM (Block–Element–Modifier) is a naming convention that makes class names encode component structure and variants.

**Example**:

```html
<div class="card card--featured">
  <h3 class="card__title">Title</h3>
  <p class="card__body">...</p>
</div>
```

**Pros**:
- Predictable, works in plain CSS
- Reduces selector nesting (usually class-only)

**Cons**:
- Verbose class names
- Still global unless combined with scoping conventions (folders, prefixes)

---

### 3) What are CSS Modules and what problem do they solve?

**Short answer**: CSS Modules scope class names locally by default (build step hashes them), preventing global collisions while keeping authoring in CSS.

**Typical usage (conceptual)**:
- Write `.button { ... }` in `Button.module.css`
- Import `styles` and apply `className={styles.button}`

**Pros**:
- Strong scoping (no accidental global overrides)
- Works well with component-based UI (React/Vue/etc.)

**Cons / trade-offs**:
- Dynamic class composition can get noisy
- Global styles (resets, typography, tokens) still need a strategy

---

### 4) What is Tailwind (utility-first CSS) and why do teams adopt it?

**Short answer**: Tailwind is a utility-first framework: you compose designs by combining small utility classes (often generated from a design system config).

**Pros**:
- Fast iteration, fewer context switches
- Consistent design tokens (spacing, colors, typography) via config
- Avoids naming bikeshedding and many specificity issues

**Cons / trade-offs**:
- Markup can get dense (many classes)
- Team needs conventions for abstraction (components, `@apply` policy, class grouping)
- Requires build tooling and understanding of purge/content configuration

---

### 5) How do these approaches compare (when scaling a codebase)?

**Short answer**:
- **BEM**: scalable in plain CSS if conventions are enforced; beware global namespace.
- **CSS Modules**: strong component scoping; good default for component apps.
- **Tailwind**: strong token consistency + low-specificity utilities; great DX if the team embraces utility composition.

**Interview framing**: “I pick based on team size, existing stack, and how much I want to rely on tooling vs conventions.”

---

### 6) What’s a good strategy for global styles in any architecture?

**Short answer**: Keep globals minimal and intentional:
- CSS reset / normalize
- Base typography (body, headings)
- Design tokens (CSS variables)
- A small set of layout utilities (optional)

**Example (tokens)**:

```css
:root {
  --space-1: 4px;
  --space-2: 8px;
  --radius-2: 8px;
  --color-bg: #ffffff;
  --color-fg: #111111;
}
```

---

### 7) Common pitfalls and how to avoid them

**Short answer**:
- **BEM**: deep nesting/selectors → keep selectors class-based and shallow.
- **CSS Modules**: mixing “local” and “global” ad-hoc → define a clear global layer (tokens/base).
- **Tailwind**: inconsistent composition → create reusable components and document patterns (button variants, spacing rules).

---

### 8) How do you talk about “theming” across these approaches?

**Short answer**: Prefer **design tokens** (CSS variables) as the foundation; then each system (BEM, Modules, Tailwind) can consume tokens.

**Example**:
- Light/dark theme toggles by switching a class on `<html>` and redefining variables.

---

## Suggested practice exercises

- Given a `200px` wide card with padding + border, compute the total rendered width for both `content-box` and `border-box`.
- Reproduce a **margin-collapsing** bug (parent/child) and fix it using two different strategies.
- Create a layout that overflows horizontally; debug and eliminate the overflow without hiding it globally.
- Build a header with left nav + right actions using flex; try both `justify-content: space-between` and `margin-left: auto` and explain trade-offs.
- Reproduce a flex overflow bug (long text in a flex child) and fix it with `min-width: 0`.
- Build a classic page layout (sidebar + main) using Grid `grid-template-areas`.
- Build a responsive card grid using `repeat(auto-fit, minmax(...))` and explain `auto-fit` vs `auto-fill`.
- Build a tooltip anchored to a card using `position: absolute` with `inset` and offsets; explain which ancestor is the containing block.
- Implement a sticky table header and debug a case where it fails due to an `overflow` ancestor.
- Create two conflicting `.btn` rules and predict which wins by specificity vs source order; then refactor to avoid `!important`.
- Write a “base styles” rule using `:where()` and show how a component class can override it cleanly.
- Build a responsive 3-column layout that becomes 1-column on small screens using mobile-first `min-width` queries.
- Create a card component that switches layout using a container query (sidebar vs full-width page).
- Create a hover interaction that uses `transform` (not `top/left`) and compare performance in DevTools.
- Add `prefers-reduced-motion` handling for a loading spinner and a toast enter/exit animation.
- Style the same “Button” component three ways: BEM, CSS Modules, and Tailwind. Write down what’s easier/harder to maintain.
- Create a small token set with CSS variables and consume it from both Tailwind (config) and non-Tailwind CSS.
- Reproduce a z-index bug caused by a stacking context (e.g., parent `transform`) and fix it by changing the stacking context boundary.
- Implement the Holy Grail layout using Grid and then re-implement it using Flexbox; compare complexity.

## Links / references

- MDN: The box model: https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model
- MDN: `box-sizing`: https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing
- MDN: Flexbox: https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox
- MDN: Grid: https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Grids
- MDN: `position`: https://developer.mozilla.org/en-US/docs/Web/CSS/position
- MDN: Specificity: https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity
- MDN: Media queries: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_media_queries/Using_media_queries
- MDN: `clamp()`: https://developer.mozilla.org/en-US/docs/Web/CSS/clamp
- MDN: CSS transitions: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_transitions/Using_CSS_transitions
- MDN: CSS animations: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animations/Using_CSS_animations
- MDN: Stacking context: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_positioned_layout/Understanding_z-index/Stacking_context
- MDN: CSS custom properties: https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties
- MDN: Container queries: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries
