# Front-End Engineering Interview Prep

This repository is a personal (but shareable) knowledge base for preparing for **front-end engineering interviews**. It’s organized to help you practice and review:

- Common interview questions (with space for your answers and notes)
- Explanations and references
- Code examples and mini-exercises
- System design prompts (front-end focused)
- Behavioral questions (STAR answers)
- Coding challenges and take-home patterns

## How to use this repository

- **Start with `topics/`**: each topic has its own `README.md` with a curated question list and a place to write your answers.
- **Use `templates/`** to add consistent structure for answers, coding challenges, and system design write-ups.
- **Put practice work in `exercises/`**: small code snippets, katas, “rebuild a component” drills, etc.
- **Add links in `resources/`**: keep references you trust (docs, articles, talks) without cluttering your notes.

Suggested workflow per question:

- Write your answer in your own words (no copy/paste).
- Add 1–2 concrete examples (code or real-world).
- Add a short “common pitfalls” note.
- Add 1 practice exercise to reinforce it.

## Repository structure

```text
.
├── README.md
├── topics/
│   ├── javascript-fundamentals/
│   ├── typescript/
│   ├── react/
│   ├── html/
│   ├── css/
│   ├── browser-apis/
│   ├── web-performance/
│   ├── accessibility/
│   ├── frontend-architecture/
│   ├── testing/
│   ├── dsa-frontend/
│   ├── frontend-system-design/
│   ├── behavioral/
│   ├── coding-challenges/
│   └── take-home-patterns/
├── templates/
├── exercises/
└── resources/
```

## Suggested study plan (scalable)

Tune this to your timeline. A good default is **6–8 weeks** with repetition.

- **Phase 1 — Fundamentals (Week 1–2)**
  - JavaScript fundamentals
  - HTML/CSS
  - Browser APIs
  - TypeScript basics

- **Phase 2 — Framework + Quality (Week 3–4)**
  - React (core patterns, rendering model, hooks)
  - Testing (unit/integration/e2e, test pyramid)
  - Accessibility (ARIA, semantics, keyboard, audits)

- **Phase 3 — Senior topics (Week 5–6)**
  - Web performance (Core Web Vitals, profiling, caching)
  - Front-end architecture (state, data-fetching, layering)
  - Front-end system design (trade-offs, constraints, diagrams)

- **Phase 4 — Interview simulation (Week 7–8, ongoing)**
  - Coding challenges under time constraints
  - Behavioral questions with STAR stories
  - Take-home patterns and “production-ready” polish

## Progress tracking approach

Use lightweight, repeatable signals. Recommended:

- **Per-topic completion**: mark questions you can answer clearly from memory.
- **Confidence levels**: tag answers as `draft`, `reviewed`, or `ready`.
- **Spaced repetition**: revisit “ready” answers every 2–3 weeks, “draft” weekly.
- **Practice artifacts**: keep a list of exercises completed in `exercises/README.md`.

Optional convention inside any topic file:

- `[ ]` not started
- `[~]` drafted
- `[x]` ready

## Contribution guidelines (even if personal)

If you later share this repo (or future-you contributes), keep it consistent:

- Prefer **your own explanations** over pasted content.
- Add sources in the **Links / references** section.
- Keep each question answer **scannable** (short paragraphs, bullets).
- Favor **small, focused code examples** and note the “why”.
- Don’t store secrets (API keys, tokens). Avoid committing `.env`.

## Quick start

- Pick a topic in `topics/`.
- Choose 3–5 questions.
- Write answers using the templates in `templates/`.
- Do one exercise in `exercises/`.

## Front-End Interview Prep (Study Repo)

This repository is a **structured study system** for front-end engineering interview preparation.
It’s designed to scale as you add more topics, questions, explanations, code examples, and practice exercises.

### How this repo is organized

- **`topics/`**: Each topic has its own folder with:
  - **`README.md`**: topic overview + how to study it
  - **`questions.md`**: curated interview Q&A with explanations
  - **`exercises/`**: prompts + solutions + test ideas
  - **`code/`**: runnable code examples (small, focused)

### Study workflow (recommended)

- **First pass**: read a topic `README.md` → skim `questions.md`
- **Second pass**: do exercises *without* looking at solutions
- **Third pass**: review solutions and write your own variations
- **Maintenance**: add “what I got wrong” notes as you practice

### Topics

- [JavaScript fundamentals](topics/javascript-fundamentals/README.md)
- [HTML (Semantic HTML foundations)](topics/html/README.md)
- [CSS (Box Model foundations)](topics/css/README.md)
- [Browser & Web APIs (DOM & BOM foundations)](topics/browser-apis/README.md)
- [Web Performance (Critical Rendering Path)](topics/web-performance/README.md)
- [Accessibility (WCAG principles)](topics/accessibility/README.md)
- [Front-End Architecture (folder patterns)](topics/frontend-architecture/README.md)
- [Testing (Unit testing foundations)](topics/testing/README.md)
- [Front-End System Design (scalable UI)](topics/frontend-system-design/README.md)
- [DSA (Arrays & strings)](topics/dsa-frontend/README.md)
- [Front-End Debugging (playbook)](topics/front-end-debugging/README.md)
- [Behavioral (STAR stories)](topics/behavioral/README.md)
- [Security (Frontend)](topics/security-frontend/README.md)
- [React (core interview topics)](topics/react/README.md)
- [TypeScript (core interview topics)](topics/typescript/README.md)
- [Observability (frontend)](topics/observability/README.md)
- [i18n & localization](topics/i18n/README.md)
- [API design for front-end](topics/api-design-frontend/README.md)
- [Networking](topics/networking/README.md)
- [Browser deep dives](topics/browser-deep-dives/README.md)

### Conventions used in this repo

- **Q&A format**:
  - **Question**
  - **Short answer**
  - **Deep dive**
  - **Common pitfalls**
  - **Follow-ups**
  - **Mini example**
- **Exercises**:
  - `prompt.md` (problem statement)
  - `solution.md` (one or more solutions + trade-offs)
  - optional `test-cases.md` (edge cases to validate)

# interview-questions-studies
