# Security (Front-End) — XSS/CSRF/CSP, auth storage, OAuth, dependency risks

Front-end security interview questions test whether you understand real web threats and can make safe architectural choices. This topic focuses on the most common areas: **XSS**, **CSRF**, **CSP**, **cookies vs tokens**, **OAuth basics**, **secure storage**, and **dependency risks**.

## How to use this file

- Treat each question as a **flashcard**: answer out loud first, then compare.
- Always describe **threat model**: what the attacker can do and what they can’t.
- Prefer defenses that are **default-safe** (platform features, secure headers, safe APIs).

---

## Core concepts

### 1) What is XSS (Cross-Site Scripting)?

**Short answer**: XSS is when an attacker gets the browser to run **malicious JavaScript** in the context of your site, allowing them to steal data, perform actions as the user, or modify content.

**Why it’s critical**: If an attacker can run JS on your origin, they can often read anything JS can read (including `localStorage` tokens) and act as the user.

---

### 2) How do you prevent XSS in front-end apps?

**Short answer**:
- Prefer safe DOM APIs (`textContent`) and framework escaping by default.
- Avoid injecting unsanitized HTML (`innerHTML`, `dangerouslySetInnerHTML`).
- Sanitize user-generated HTML if you must render it (allowlist-based sanitization).
- Use **CSP** to reduce blast radius.

**Common XSS sources**:
- User input rendered as HTML
- URL/query string reflected into the DOM
- Third-party scripts/widgets

---

### 3) What is CSRF (Cross-Site Request Forgery)?

**Short answer**: CSRF is when a malicious site causes a user’s browser to send authenticated requests to your site (because cookies are automatically attached), performing actions without the user’s intent.

**Key point**: CSRF is primarily a risk when you authenticate with **cookies**.

---

### 4) How do you mitigate CSRF?

**Short answer**:
- Use `SameSite=Lax` or `SameSite=Strict` on auth cookies when possible.
- For state-changing requests, use a CSRF token strategy (synchronizer token / double-submit, depending on architecture).
- Validate `Origin`/`Referer` for sensitive endpoints when appropriate.

**Pitfall**: CSRF defenses belong on the server; front-end can support but can’t enforce them alone.

---

### 5) What is CSP (Content Security Policy) and why does it matter?

**Short answer**: CSP is a response header that restricts what content can load/execute (scripts, styles, frames, connections). It helps prevent or limit XSS by blocking unexpected script execution and restricting sources.

**Practical benefits**:
- Limits where scripts can come from
- Can block inline scripts unless explicitly allowed (nonces/hashes)
- Reduces risk from injected scripts and some supply-chain attacks

---

## Cookies vs tokens (frontend perspective)

### 1) Cookies vs `localStorage`: what’s the security trade-off?

**Short answer**:
- Tokens in `localStorage` are easy for JS to use, but **XSS can steal them**.
- Cookies can be **HttpOnly** (not readable by JS), reducing token theft via XSS, but cookie-based auth needs CSRF protections.

**Interview-friendly guidance**: “Prefer HttpOnly, Secure cookies for session tokens; focus heavily on XSS prevention either way.”

---

### 2) What cookie attributes should you mention for auth?

**Short answer**:
- `HttpOnly`: JS can’t read it
- `Secure`: HTTPS only
- `SameSite=Lax/Strict` (or `None; Secure` if cross-site is required)
- `Path`, `Domain`, `Max-Age/Expires`

---

### 3) When would you choose bearer tokens (Authorization header)?

**Short answer**: When you need non-cookie auth (some API architectures, non-browser clients) or cross-site scenarios where cookie behavior is complex. But you must handle token storage safely and still prevent XSS.

**Pitfall**: “Storing access tokens in `localStorage`” is easy but risky under XSS; consider short-lived tokens and secure refresh strategies (often via HttpOnly cookie refresh).

---

## OAuth basics (interview level)

### 1) What is OAuth, and what is it not?

**Short answer**: OAuth is an **authorization** framework (delegated access). It’s not “authentication” by itself (that’s often layered via OpenID Connect).

---

### 2) What is OIDC (OpenID Connect)?

**Short answer**: OIDC is an identity layer on top of OAuth that standardizes login and identity claims (ID tokens) so you can authenticate users.

---

### 3) What flow should SPAs use today (high level)?

**Short answer**: Use **Authorization Code Flow with PKCE** for browser-based apps (rather than implicit flow).

**Why**: Better security properties and aligns with modern best practices.

---

## Secure storage & handling secrets

### 1) What data should never be stored in the browser?

**Short answer**: Long-lived secrets (private keys, permanent API secrets). The browser is a hostile environment; anything in JS-accessible storage can be exfiltrated under XSS.

---

### 2) What’s a safe approach for sessions in a web app?

**Short answer**:
- Server-managed session id stored in **HttpOnly Secure cookie**.
- CSRF mitigations (`SameSite` + CSRF tokens as needed).
- Short session lifetimes with rotation.

---

### 3) What do you do about sensitive data in logs and analytics?

**Short answer**:
- Avoid logging tokens/PII.
- Scrub query params and request bodies.
- Use allowlists for what you send to analytics/error reporting.

---

## Dependency & supply-chain risks

### 1) What are dependency risks in frontend?

**Short answer**: Third-party packages can introduce vulnerabilities (XSS gadgets, prototype pollution, RCE in build tooling) and can be compromised (supply-chain attacks).

---

### 2) How do you reduce dependency risk?

**Short answer**:
- Minimize dependencies; prefer small, well-maintained packages
- Lockfile + trusted registries
- Automated vulnerability scanning (Dependabot/Snyk/etc.)
- Review/limit third-party scripts and enforce CSP
- Keep build tools updated

---

## Suggested practice exercises

- Find 3 places in a UI where developers commonly introduce XSS and propose safe alternatives.
- Sketch an auth design using HttpOnly cookies + `SameSite` and explain CSRF protections.
- Draft a basic CSP policy for a typical SPA and list what it might break (inline scripts, third-party tags).
- Create a “dependency hygiene” checklist for PR reviews.

## Links / references

- MDN: XSS (overview): https://developer.mozilla.org/en-US/docs/Glossary/Cross-site_scripting
- MDN: CSRF: https://developer.mozilla.org/en-US/docs/Glossary/CSRF
- MDN: CSP: https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP
- OAuth 2.0 (RFC 6749): https://www.rfc-editor.org/rfc/rfc6749
- OIDC overview: https://openid.net/developers/how-connect-works/
