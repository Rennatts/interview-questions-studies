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

## Frontend security (advanced)

### 1) CSP tuning: how do you make CSP strict without breaking the app?

**Short answer**: Start with **report-only**, inventory what the app loads/executes, then tighten step-by-step:
- Use **nonces/hashes** instead of allowing `unsafe-inline` scripts.
- Restrict `script-src`, `connect-src`, `frame-src` to known domains.
- Add `report-to`/`report-uri` to collect violations during rollout.

**Common pitfalls**:
- Third-party tags that inject inline scripts
- Inline event handlers (`onclick=...`) and inline script blocks
- Missing domains for APIs/WebSockets in `connect-src`

---

### 2) What are Trusted Types and when are they useful?

**Short answer**: Trusted Types is a browser security feature that helps prevent DOM XSS by requiring “trusted” values for dangerous sinks (like `innerHTML`) when enforced via CSP. It’s most useful in large apps where you want to eliminate ad-hoc HTML injection and enforce safe patterns.

**Interview-friendly line**: “Trusted Types makes unsafe DOM injection harder by default and pushes sanitization to approved code paths.”

---

### 3) What is Subresource Integrity (SRI) and when do you use it?

**Short answer**: SRI pins a fetched resource (usually a third-party script/style) to a known hash so if the resource is tampered with, the browser refuses to load it.

**Example**:

```html
<script
  src="https://cdn.example.com/lib.min.js"
  integrity="sha384-..."
  crossorigin="anonymous"
></script>
```

**Trade-off**: Updating the library requires updating the integrity hash; it’s best for versioned CDN assets.

---

### 4) Dependency governance: what does a mature approach look like?

**Short answer**:
- Define allowed registries and lockfiles; require reproducible builds.
- Automate vulnerability scanning and set SLAs for patching.
- Review and limit high-risk dependencies (parsers, templating, build plugins).
- Audit third-party scripts separately (they execute with high privilege in the browser).
- Maintain an inventory of dependencies and owners.

---

### 5) CSRF nuances: when do you end up with `SameSite=None` and what does that imply?

**Short answer**: `SameSite=None` is used when cookies must be sent in cross-site contexts (e.g., some embedded or cross-domain auth flows). It requires `Secure`, and it re-introduces more CSRF exposure unless you add additional server-side defenses.

**Implications**:
- You likely need a stronger CSRF strategy (CSRF tokens and/or strict Origin checks for state-changing requests).
- Cookie scope (`Domain`, `Path`) should be as narrow as possible.

---

### 6) Token refresh patterns (frontend-safe options)

**Short answer**: Prefer short-lived access tokens and a refresh mechanism that minimizes XSS exposure.

**Common safer pattern**:
- Access token: short-lived (in memory) for API calls.
- Refresh token: stored in **HttpOnly Secure cookie** (not readable by JS).
- Refresh endpoint rotates tokens and enforces CSRF protections if cookie-based.

**Pitfall**: Storing refresh tokens in `localStorage` makes them XSS-extractable and increases blast radius.

---

### 7) Secure file upload basics: what should the front end care about?

**Short answer**:
- Validate **file type and size** on the client for UX, but enforce it on the server.
- Use signed URLs / direct-to-storage uploads when appropriate (reduces API load).
- Avoid rendering untrusted file contents as HTML; treat user files as untrusted.

**Practical front-end checklist**:
- Restrict file picker with `accept` (UX only).
- Show progress and handle retry safely (idempotency).
- If displaying images, use safe rendering paths and don’t assume metadata is trustworthy.

---

### 8) Clickjacking: what is it and how do you mitigate it?

**Short answer**: Clickjacking is when your site is embedded in an attacker-controlled frame and users are tricked into clicking invisible/overlaid UI.

**Mitigations**:
- Prefer CSP `frame-ancestors` to control who can embed your pages.
- Alternatively `X-Frame-Options` (older; less flexible).

**Interview-friendly line**: “Use `frame-ancestors` as the modern, explicit control.”

---

### 9) Dependency scanning workflow: what does “good” look like in practice?

**Short answer**:
- Run automated scanning on PRs and on a schedule (advisories change over time).
- Triage by exploitability and reachability (prod vs dev dependency, runtime vs build-only).
- Patch quickly for high severity; track SLAs; document exceptions.

**Common steps**:
- Dependabot/renovate opens PRs → CI runs tests → security review → merge
- Periodic audit report → backlog for medium/low risk issues

---

## LGPD (Lei Geral de Proteção de Dados) — front-end security & privacy

### 1) O que é LGPD (em uma frase)?

**Short answer**: LGPD é a lei brasileira que regula o tratamento de **dados pessoais** (incluindo dados pessoais sensíveis) e define princípios, bases legais e direitos dos titulares.

---

### 2) O que conta como dado pessoal (e por que isso importa no front-end)?

**Short answer**: Qualquer informação relacionada a pessoa identificada ou identificável (ex: email, telefone, IP em certos contextos, identificadores de dispositivo, ids de conta). No front-end isso aparece em:
- formulários e validações
- logs, analytics, session replay
- cookies/storage e identificadores persistentes

---

### 3) Princípios LGPD que impactam decisões técnicas no front-end

**Short answer** (os mais “engenharia-friendly”):
- **Finalidade / adequação**: colete só o que precisa para um objetivo claro.
- **Necessidade (minimização)**: evite “capturar tudo” (DOM, campos, payloads).
- **Segurança / prevenção**: proteja dados em trânsito e reduza superfícies de ataque.
- **Transparência**: explique coleta/uso (consentimento quando aplicável).
- **Qualidade dos dados**: evite dados errados e permita correções.

---

### 4) Consentimento vs outras bases legais: o que o front-end deve saber?

**Short answer**: Nem todo tratamento exige consentimento, mas quando consentimento é a base, o front-end precisa:
- coletar/registrar escolha de forma clara
- permitir revogação
- respeitar a escolha (não disparar tags antes do opt-in)

**Prática comum**:
- “Consent mode”/tag manager: só ativar analytics/ads após consentimento.

---

### 5) Segurança LGPD na prática: checklist técnico rápido

**Short answer**:
- **HTTPS** sempre; cookies de sessão com `HttpOnly` + `Secure` + `SameSite`.
- Evitar armazenar dados sensíveis em `localStorage`/`sessionStorage`.
- Redação/mascaramento de PII em logs, erros e replay.
- Limitar coleta de analytics (allowlist de campos).
- Sanitização/escapes para reduzir risco de XSS (que pode exfiltrar dados pessoais).

---

### 6) Direitos do titular: o que isso implica no produto?

**Short answer**: O usuário pode solicitar acesso, correção, exclusão e portabilidade (entre outros). No front-end, isso normalmente implica:
- fluxos de “download my data” / “delete my account”
- estados de UI e confirmações seguras
- auditoria/observabilidade sem expor PII (ids hashados)

---

## Suggested practice exercises

- Find 3 places in a UI where developers commonly introduce XSS and propose safe alternatives.
- Sketch an auth design using HttpOnly cookies + `SameSite` and explain CSRF protections.
- Draft a basic CSP policy for a typical SPA and list what it might break (inline scripts, third-party tags).
- Create a “dependency hygiene” checklist for PR reviews.
- Create a CSP rollout plan (report-only → enforce) and list what you’ll measure in violation reports.
- Pick one third-party script and decide whether to self-host it, apply SRI, or remove it; justify the decision.
- Sketch a cross-site scenario that forces `SameSite=None` and list the CSRF mitigations you’d require.
- Design a secure upload UX: client-side checks + signed URL flow + server validation + safe preview.
- Liste quais dados pessoais sua app envia para analytics/logs e proponha uma política de redaction/allowlist compatível com LGPD.
- Desenhe um fluxo de “delete account” (LGPD) incluindo confirmações, segurança (reauth), e comportamento de observabilidade (sem PII).

## Links / references

- MDN: XSS (overview): https://developer.mozilla.org/en-US/docs/Glossary/Cross-site_scripting
- MDN: CSRF: https://developer.mozilla.org/en-US/docs/Glossary/CSRF
- MDN: CSP: https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP
- web.dev: CSP: https://web.dev/articles/csp
- web.dev: Trusted Types: https://web.dev/articles/trusted-types
- MDN: Subresource Integrity: https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity
- MDN: CSP `frame-ancestors`: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors
- MDN: SameSite cookies: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite
- OAuth 2.0 (RFC 6749): https://www.rfc-editor.org/rfc/rfc6749
- OIDC overview: https://openid.net/developers/how-connect-works/
- Texto da LGPD (Lei 13.709/2018): https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm
- ANPD (Autoridade Nacional): https://www.gov.br/anpd/pt-br
