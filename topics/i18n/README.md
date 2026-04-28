# i18n & localization — formatting, RTL, pluralization, layout expansion, timezone pitfalls

Internationalization (i18n) and localization (l10n) are common front-end interview topics because they impact correctness (dates/currencies), UX (RTL/layout), and global readiness. This section focuses on the highest-signal pitfalls and patterns.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Always mention **real-world edge cases** (plural rules, time zones, long strings, RTL).
- Prefer platform APIs (e.g., `Intl.*`) and avoid reinventing formatting logic.

---

## Core concepts

### 1) i18n vs l10n: what’s the difference?

**Short answer**:
- **i18n**: building the product so it can support multiple locales (architecture + tooling).
- **l10n**: the actual translations and locale-specific adaptation (strings, formats) for a particular market.

---

### 2) What should never be hard-coded for global apps?

**Short answer**:
- Dates/times formatting
- Number/currency formatting
- Pluralization rules
- Text direction assumptions (LTR)
- String concatenation that assumes word order

---

## Formatting (Intl)

### 1) How do you format numbers and currency correctly?

**Short answer**: Use `Intl.NumberFormat` with locale + currency.

**Example**:

```js
new Intl.NumberFormat("pt-BR", { style: "currency", currency: "BRL" }).format(1234.56);
```

---

### 2) How do you format dates and times correctly?

**Short answer**: Use `Intl.DateTimeFormat` and be explicit about time zone when needed.

**Pitfall**: Formatting a server timestamp in the wrong time zone (or assuming local time equals business time).

---

## Pluralization

### 1) Why is pluralization tricky?

**Short answer**: Many languages have more than “singular vs plural” forms, and the rules differ by locale.

**Better approach**:
- Use ICU message formats or `Intl.PluralRules` (or your i18n library’s plural system) instead of `count === 1 ? ... : ...`.

---

## RTL (Right-to-left) support

### 1) What changes when you support RTL languages?

**Short answer**:
- Layout direction flips (horizontal flow).
- Icons and affordances may need mirroring (chevrons/arrows).
- Text alignment and spacing assumptions break.

**Practical guidance**:
- Use logical CSS properties where possible (e.g., `margin-inline-start` instead of `margin-left`).
- Test with an RTL locale early; don’t wait until the end.

---

### 2) What are common RTL bugs?

**Short answer**:
- Hard-coded left/right paddings/margins
- Absolute positioning using `left/right` instead of logical properties
- Incorrect icon direction (back/next)
- Mixed-direction content (numbers/URLs) rendering oddly without proper handling

---

## Layout expansion (longer translations)

### 1) What is layout expansion and why does it matter?

**Short answer**: Translated strings can be significantly longer than English, causing truncation, overlap, or broken layouts.

**Mitigations**:
- Avoid fixed widths for text containers where possible.
- Allow wrapping; handle truncation intentionally (ellipsis + tooltip).
- Test with pseudo-localization (e.g., artificially longer strings).

---

## Time zone pitfalls

### 1) What are common time zone bugs?

**Short answer**:
- Showing dates in the user’s locale/time zone when the business logic requires a specific zone (e.g., market close time).
- Off-by-one day around midnight/UTC conversions.
- Daylight saving transitions (missing/duplicated hours).

**Interview-friendly line**: “Always clarify which time zone is the source of truth.”

---

## Locale negotiation

### 1) How do you decide which locale to use?

**Short answer**: Prefer an explicit user setting, otherwise negotiate using browser preferences (e.g., `Accept-Language` / `navigator.languages`) with a supported-locale fallback.

**Practical order**:
- User profile setting (persisted)
- URL locale prefix (if your app uses it)
- Browser preferences (`navigator.languages`)
- Default app locale (`en-US`)

---

### 2) What’s a common pitfall in locale negotiation?

**Short answer**: Treating locale strings as exact matches only. You should support fallback matching (e.g., `pt-BR` → `pt`) when appropriate, and keep a clear supported-locale list.

---

## Content translation workflows

### 1) What does a typical translation workflow look like?

**Short answer**:
- Extract messages from code (keys + default messages)
- Send to translators (or TMS)
- Receive translations and run checks (missing keys, ICU syntax errors)
- Ship with versioning and fallback behavior

**Common pitfalls**:
- Breaking changes in message keys without migration
- Translators missing context (screenshots, description)

---

## Testing i18n (pseudo-localization)

### 1) What is pseudo-localization and why use it?

**Short answer**: Pseudo-localization generates “fake” translations that are longer and include unusual characters. It reveals layout issues, missing externalization, and truncation bugs early.

**Examples of what it catches**:
- Hard-coded strings not going through the i18n layer
- UI that breaks when strings expand by 30–50%
- Missing RTL readiness

---

## BiDi text gotchas (mixed direction)

### 1) What is BiDi text and why does it matter?

**Short answer**: BiDi (bidirectional) text happens when RTL text contains LTR segments (numbers, URLs, email). Without care, punctuation and ordering can appear confusing.

**Practical guidance**:
- Don’t assume visual order equals logical order.
- Be careful when concatenating strings; keep structured messages in the i18n system (ICU).

---

## Suggested practice exercises

- Replace manual currency formatting with `Intl.NumberFormat` across a small UI.
- Implement pluralized messages in 2 locales (one with complex plural rules).
- Create an RTL test mode and fix layout issues using logical properties.
- Run pseudo-localization and fix components that break under long strings.
- Write test cases for date rendering around a DST boundary and a UTC midnight boundary.
- Implement a locale negotiation function that maps `navigator.languages` to your supported locales with fallbacks.
- Add a pseudo-loc mode and find 5 hard-coded strings that should be externalized.

## Links / references

- MDN: `Intl`: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl
- Unicode ICU message format (conceptual): https://unicode-org.github.io/icu/userguide/format_parse/messages/
- W3C i18n techniques: https://www.w3.org/International/techniques/
- MDN: `navigator.languages`: https://developer.mozilla.org/en-US/docs/Web/API/Navigator/languages
