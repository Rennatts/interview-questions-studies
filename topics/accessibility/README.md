# Accessibility (a11y) — WCAG principles (POUR)

Accessibility is a frequent interview area because it reflects real product quality: inclusive UX, legal/compliance risk, and engineering discipline. This section starts with WCAG’s four principles, often summarized as **POUR**.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Always connect the concept to a concrete user impact (screen reader, keyboard-only, low vision, cognitive load).
- Add your own notes under **Notes / variations**.

---

## Questions & answers

### 1) What is WCAG and what does “POUR” mean?

**Short answer**: WCAG (Web Content Accessibility Guidelines) is the main standard for web accessibility. Its success criteria are organized under four principles: **Perceivable, Operable, Understandable, Robust** (POUR).

**Interview framing**: “POUR is a checklist for thinking about accessibility beyond just screen readers.”

---

### 2) Perceivable: what does it mean in practice?

**Short answer**: Users must be able to **perceive** the information (with sight, hearing, touch, or assistive tech). If content is only presented in one sensory channel, some users can’t access it.

**Common examples**:
- Text alternatives for images (meaningful `alt`, decorative `alt=""`)
- Captions for video with speech; transcripts for audio
- Sufficient color contrast; don’t use color alone to convey meaning
- Responsive text and layout that works with zoom

**Mini check**:
- “If I turn images off / can’t see color / can’t hear audio, do I still get the message?”

---

### 3) Operable: what does it mean in practice?

**Short answer**: Users must be able to **operate** the interface. Many users don’t use a mouse (keyboard-only, switch devices, voice control).

**Common examples**:
- Full keyboard support (tab order, focus visible, no keyboard traps)
- Controls have correct semantics (use real `<button>` / `<a>`)
- Enough time to complete tasks (avoid timeouts without options)
- Avoid motion/animations that cause issues (support `prefers-reduced-motion`)

**Mini check**:
- “Can I use the whole UI with only a keyboard?”

---

### 4) Understandable: what does it mean in practice?

**Short answer**: Users must be able to **understand** content and how to use the UI.

**Common examples**:
- Clear labels and instructions (forms)
- Helpful, specific error messages (and how to fix)
- Consistent navigation and predictable interactions
- Avoid surprising context changes (e.g., auto-submit on focus change)

**Mini check**:
- “If a user makes a mistake, can they recover easily?”

---

### 5) Robust: what does it mean in practice?

**Short answer**: Content must be **robust** enough to work with different browsers and assistive technologies now and in the future.

**Common examples**:
- Valid, semantic HTML (correct landmarks/headings/forms)
- ARIA used correctly (no conflicting roles; correct states)
- Custom components implement expected keyboard behavior and announce state changes

**Mini check**:
- “Will this still work with different screen readers and browsers?”

---

### 6) How do you translate POUR into everyday engineering decisions?

**Short answer**:
- Start with **semantic HTML** (often solves Robust + Operable cheaply).
- Add ARIA only when needed (labeling/state/relationships).
- Build a11y into component APIs (labels required, focus management patterns).
- Test with keyboard + screen reader spot checks, and run automated audits (as a baseline).

---

## ARIA roles (basics)

### 1) What is ARIA and what does a “role” do?

**Short answer**: ARIA (Accessible Rich Internet Applications) is a set of attributes used to expose **roles**, **names**, **states**, and **relationships** to assistive technologies. A **role** tells assistive tech what an element *is* (button, navigation, dialog, etc.).

---

### 2) What’s the #1 rule of ARIA?

**Short answer**: Prefer **semantic HTML** first. Use ARIA to fill gaps (labeling, state, relationships) when native elements aren’t enough.

**Common pitfall**:
- Adding redundant roles (e.g., `role="button"` on a `<button>`), or overriding semantics without implementing expected behavior.

---

### 3) What is an implicit role, and why should you care?

**Short answer**: Many elements have built-in (implicit) semantics. For example:
- `<button>` → button
- `<a href="...">` → link
- `<nav>` → navigation landmark

**Why it matters**: If you use native elements, you get keyboard support + semantics “for free”.

---

### 4) What is an accessible name, and how do you provide it?

**Short answer**: The accessible name is what a screen reader announces as the control’s label.

**Common sources** (best-first):
- Visible `<label>` (for form controls)
- Element text content (e.g., `<button>Save</button>`)
- `aria-labelledby="id-of-label"`
- `aria-label="..."` (use when no visible label is possible)

---

### 5) Landmark roles: what are they and how do you label multiple regions?

**Short answer**: Landmarks help users navigate by regions (e.g., navigation, main, complementary). If you have multiple regions of the same landmark type, label them.

**Example**:

```html
<nav aria-label="Primary">...</nav>
<nav aria-label="Footer">...</nav>
```

---

### 6) What’s risky about `div` + `role` (e.g., `role="button"`)?

**Short answer**: ARIA does **not** add keyboard behavior. If you build custom widgets, you must implement:
- Focusability (`tabindex="0"`)
- Keyboard interaction (Enter/Space where appropriate)
- Correct states (`aria-pressed`, `aria-expanded`, etc.)

**Interview-friendly guidance**: “Use native controls unless you truly can’t.”

---

### 7) Common role/state patterns you should know

**Short answer**:
- Disclosure/accordion: trigger uses `aria-expanded="true|false"` and is associated with content.
- Toggle button: `aria-pressed="true|false"` on a `<button>`.
- Current page/step: `aria-current="page|step|location|..."` (often on a link).

---

### 8) What is `aria-hidden` and when should you use it?

**Short answer**: `aria-hidden="true"` removes an element (and its subtree) from the accessibility tree.

**Pitfall**:
- Don’t hide focusable elements with `aria-hidden`; they can become “keyboard reachable but screen-reader invisible”.

---

## Keyboard navigation

### 1) Why is keyboard navigation a core accessibility requirement?

**Short answer**: Many users can’t use a mouse (motor impairments, temporary injury, power users, screen reader users). If your UI isn’t usable with a keyboard, it’s not operable.

---

### 2) What are the “default” keyboard interactions you get for free with semantic HTML?

**Short answer**: Native controls (like `<button>`, `<a href>`, `<input>`, `<select>`) are automatically focusable and support expected keys (Tab, Enter, Space, arrows where applicable).

**Interview-friendly guidance**: “Use native elements first—keyboard behavior comes along with semantics.”

---

### 3) What is tab order, and how do you keep it sane?

**Short answer**: Tab order is the sequence of focusable elements when the user presses Tab/Shift+Tab. Keep it aligned with the visual/reading order.

**Best practices**:
- Don’t reorder focus with CSS in ways that disagree with DOM order.
- Avoid positive `tabindex` (e.g., `tabindex="5"`). It creates brittle, non-local focus order.

---

### 4) When should you use `tabindex`?

**Short answer**:
- `tabindex="0"`: make a non-focusable element focusable *in natural order* (use sparingly).
- `tabindex="-1"`: make an element programmatically focusable but not tabbable (useful for focus management).

**Common use cases**:
- Focus a dialog container on open (`tabindex="-1"` + `.focus()`).
- “Roving tabindex” pattern in composite widgets (tabs, menus).

---

### 5) What is a “keyboard trap” and how do you avoid it?

**Short answer**: A keyboard trap is when focus enters a region and the user can’t leave using the keyboard.

**Avoidance**:
- Ensure Tab can always move forward and Shift+Tab can move backward.
- For dialogs/menus, provide an obvious close mechanism and support Escape where appropriate.

---

### 6) What does good focus styling look like?

**Short answer**: Focus should be clearly visible for keyboard users. Don’t remove focus outlines unless you replace them with an equally visible alternative.

**Best practice**:
- Use `:focus-visible` for a clean experience (show focus ring for keyboard, not for mouse clicks).

**Example**:

```css
:focus-visible {
  outline: 2px solid dodgerblue;
  outline-offset: 2px;
}
```

---

### 7) How do you handle focus management for dialogs/modals?

**Short answer**: On open, move focus into the dialog; while open, keep focus within it (focus trap); on close, return focus to the trigger.

**Minimum expectations**:
- Escape closes (when appropriate)
- The dialog has an accessible name (label)
- Background content is not focusable while modal is open (focus trap or inert/aria-hidden strategy done correctly)

---

### 8) What is “roving tabindex” and where is it used?

**Short answer**: A pattern where only one item in a composite widget is tabbable (`tabindex="0"`) and the rest are not (`tabindex="-1"`), while arrow keys move focus within the widget.

**Common widgets**:
- Tabs
- Menus/menubars
- Listbox

---

## Screen readers

### 1) What is a screen reader and what information does it rely on?

**Short answer**: A screen reader is assistive technology that reads and navigates the UI using the **accessibility tree**, which is built from semantic HTML plus ARIA (role, name, states, relationships).

**Interview-friendly framing**: “Screen readers don’t read the pixels; they read semantics.”

---

### 2) What is the accessibility tree (high level)?

**Short answer**: It’s the structured representation of UI elements exposed to assistive tech. Elements can be included/excluded (e.g., `display: none` and `aria-hidden="true"` remove content from the tree).

---

### 3) What makes a control understandable to a screen reader?

**Short answer**: A control needs:
- A **role** (what it is)
- An **accessible name** (what it’s called)
- Relevant **state/value** (expanded, checked, invalid, etc.)

**Examples**:
- `<button>Save</button>` (role + name via text)
- `<input id="email">` + `<label for="email">Email</label>` (name via label)
- `aria-expanded="true"` on an accordion trigger (state)

---

### 4) Accessible names: `aria-label` vs `aria-labelledby` — when do you use which?

**Short answer**:
- Prefer visible text + native labeling first.
- Use `aria-labelledby` when there is already visible text elsewhere you want to reference.
- Use `aria-label` when there is **no visible label** (e.g., icon-only button).

**Common pitfall**:
- Using `aria-label` when a visible label exists can create mismatches between what sighted users see and what screen reader users hear.

---

### 5) What is `aria-live`, and when do you need it?

**Short answer**: `aria-live` announces dynamic content updates that aren’t triggered by a user moving focus (e.g., “Saved”, “3 errors found”, incoming chat message).

**Common values**:
- `aria-live="polite"`: announce when the user is idle (most status updates).
- `aria-live="assertive"`: interrupt to announce immediately (use sparingly).

**Example**:

```html
<div aria-live="polite" role="status" id="toast-region"></div>
```

---

### 6) Forms and screen readers: what are the essentials?

**Short answer**:
- Inputs need labels (native `<label>` preferred).
- Errors should be connected with `aria-describedby` and flagged with `aria-invalid="true"`.
- Consider an error summary that’s reachable and links to fields.

---

### 7) What are common screen reader testing habits for engineers?

**Short answer**:
- Navigate the page by headings/landmarks (are they meaningful?)
- Tab through interactive elements (focus order and names make sense?)
- Trigger validation errors and confirm they’re announced
- Check that icons/buttons have clear names (no “button, unlabeled”)

**Practical note**: Automated tools help, but they can’t validate the quality of labels, instructions, and interaction flows.

---

## Color contrast

### 1) What is color contrast and why does it matter?

**Short answer**: Color contrast measures how distinguishable text and UI elements are from their background. Poor contrast makes content hard or impossible to read for users with low vision, color vision deficiency, glare, or when using dim screens.

---

### 2) What are the common WCAG contrast targets for text?

**Short answer** (WCAG AA):
- Normal text: at least **4.5:1**
- Large text: at least **3:1** (large means roughly \( \ge 24px \) regular or \( \ge 18.66px \) bold)

**Notes / variations**:
- AAA targets are stricter (often 7:1 for normal text).

---

### 3) Contrast vs “don’t use color alone”: what’s the difference?

**Short answer**:
- **Contrast**: can you read/see it against the background?
- **Not color alone**: can you understand the meaning without relying solely on color (e.g., error state indicated only by red).

**Example**:
- Bad: red border only
- Better: red border + error text + icon + `aria-invalid` and `aria-describedby`

---

### 4) Do icons, borders, and focus rings have contrast requirements?

**Short answer**: Yes. Non-text UI components (icons, input borders, focus indicators) need sufficient contrast against adjacent colors so they’re visible.

**Practical takeaway**:
- Treat focus rings as first-class UI: they must be clearly visible on all backgrounds.

---

### 5) What are common real-world contrast failures?

**Short answer**:
- Light gray text on white backgrounds
- Placeholder text used as the only label (and low contrast)
- Disabled states that become unreadable
- Focus styles that are too subtle or removed entirely

---

### 6) How do teams maintain good contrast at scale?

**Short answer**:
- Define design tokens with contrast in mind (foreground/background pairs).
- Test in design tools and in the browser (automated audits + manual review).
- Provide accessible defaults for components (buttons, inputs, links, focus ring).

---

## ARIA patterns (APG mapping)

ARIA patterns are easiest when you map them to the WAI-ARIA Authoring Practices (APG): roles, keyboard interactions, and required states.

### 1) Dialog (modal): what are the key requirements?

**Short answer**:
- The dialog has an **accessible name** (title via `aria-labelledby` or `aria-label`).
- On open: move focus into the dialog; while open: **trap focus**; on close: return focus to the trigger.
- Support Escape to close when appropriate.

**APG mapping (conceptual)**:
- role: `dialog` (or `alertdialog` for urgent confirmation)
- name: `aria-labelledby` / `aria-label`
- focus management: trap + restore focus

---

### 2) Combobox (autocomplete): why is it hard?

**Short answer**: Comboboxes combine an input, a popup list, keyboard navigation, and active option announcements. If you build it yourself, you must implement the full interaction model (arrows, Enter, Escape, and proper ARIA state).

**APG mapping (conceptual)**:
- input: role `combobox` + `aria-expanded`, `aria-controls`
- popup: role `listbox`
- option: role `option`
- active option: `aria-activedescendant`

**Rule of thumb**: Prefer a proven component that follows APG, or use native controls when possible.

---

### 3) Tabs: what are the key ARIA roles and keyboard expectations?

**Short answer**:
- Use arrow keys to move focus between tabs (roving tabindex pattern).
- Expose selected state and relationships so screen readers announce the active tab/panel.

**APG mapping (conceptual)**:
- container: `role="tablist"`
- tabs: `role="tab"` + `aria-selected`
- panels: `role="tabpanel"` labeled by the active tab

---

## Form error announcements (a11y)

### 1) What’s the goal when announcing form errors?

**Short answer**: Ensure users learn **what failed**, **why**, and **how to fix it**, without hunting—especially when using a screen reader.

---

### 2) What’s a solid, practical error pattern?

**Short answer**:
- Field-level message + connect it with `aria-describedby`
- Set `aria-invalid="true"` for invalid fields
- Add an error summary on submit that is focusable and announced (commonly via `role="alert"` or a live region)

**Pitfall**: Error text exists visually but isn’t programmatically connected to the field.

---

## Focus trapping (best practices)

### 1) What is focus trapping and when do you need it?

**Short answer**: Focus trapping keeps keyboard focus inside a modal dialog while it’s open. You need it for true modals that block interaction with the rest of the page.

**Common pitfalls**:
- Not restoring focus to the opener
- Trapping focus but leaving background content tabbable
- Hidden/disabled controls still reachable in the tab order

---

## Audits (axe / Lighthouse)

### 1) What can automated audits catch (and what can’t they)?

**Short answer**:
- Catch: missing labels, low contrast, unnamed buttons, some ARIA misuse.
- Can’t fully catch: whether labels/instructions make sense, keyboard UX quality, complex widget behavior.

**Interview-friendly line**: “Use axe/Lighthouse as a baseline, then do keyboard + screen reader spot checks on critical flows.”

---

## Suggested practice exercises

- Audit a simple page against POUR: list 2 issues per principle.
- Take a custom “div button” and make it accessible (or replace with `<button>`), including keyboard interaction and focus states.
- Build a small form with:
  - visible labels
  - inline errors connected with `aria-describedby`
  - error summary that links to fields
- Add captions (`<track>`) to a video element and verify it works.
- Add `aria-label` to an icon-only button (close, search) and verify its announced name with a screen reader.
- Create two navs and label them using `aria-label` (or `aria-labelledby`) so they’re distinguishable.
- Build a modal: trap focus, support Escape, and return focus to the trigger on close. Verify with only keyboard.
- Implement tabs with roving tabindex + arrow key navigation (use APG as guidance).
- Add an `aria-live` region for a “Saved” status message and confirm it’s announced without stealing focus.
- Create a form error flow where the error summary is announced and each field error is linked via `aria-describedby`.
- Pick 5 color pairs from your design system (text + background) and verify they meet 4.5:1 for normal text; adjust tokens if they don’t.
- Use axe or Lighthouse to audit a page, fix 3 issues, and explain which ones were easy vs required human judgment.
- Build a dialog with correct focus management and confirm it meets APG expectations.

## Links / references

- WCAG overview: https://www.w3.org/WAI/standards-guidelines/wcag/
- WCAG 2.2 quick reference: https://www.w3.org/WAI/WCAG22/quickref/
- WAI-ARIA Authoring Practices (APG): https://www.w3.org/WAI/ARIA/apg/
- WebAIM contrast checker: https://webaim.org/resources/contrastchecker/
- MDN: ARIA: https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA
- MDN: Focus management: https://developer.mozilla.org/en-US/docs/Web/Accessibility/Keyboard-navigable_JavaScript_widgets
- WebAIM: Screen readers: https://webaim.org/articles/screenreader/
- WCAG (Understanding contrast): https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html
- axe-core: https://github.com/dequelabs/axe-core
- Lighthouse: https://developer.chrome.com/docs/lighthouse/overview/
