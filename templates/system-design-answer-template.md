# Front-end system design answer template

## Problem statement (restate)

<!-- Restate the problem in your own words. -->

## Goals / non-goals

- **Goals**:
  - <!-- goal -->
- **Non-goals**:
  - <!-- non-goal -->

## Users, use cases, and constraints

- **Primary flows**:
- **Scale assumptions**: users/day, QPS, payload sizes, devices
- **Constraints**: latency, offline, SEO, auth, privacy, accessibility, i18n

## High-level architecture

<!-- Draw a simple diagram (ASCII or link to an image) and describe components. -->

- **Client**:
- **Backend dependencies**:
- **CDN / edge**:

## Data model & API contracts

- **Key entities**:
- **API endpoints / GraphQL ops**:
- **Caching strategy**: HTTP cache headers, SWR, normalized cache, invalidation

## State management & data fetching

- **Server state**:
- **Client state**:
- **Async patterns**: retries, backoff, cancellation, optimistic updates

## Performance plan

- **Core Web Vitals targets**:
- **Bundle strategy**: splitting, lazy loading, tree-shaking
- **Rendering strategy**: SSR/CSR/SSG/streaming (if relevant)
- **Media**: responsive images, compression, formats

## Accessibility plan

- **Semantics & keyboard**:
- **ARIA usage**:
- **Testing**: axe, keyboard checks, screen reader spot checks

## Reliability & resilience

- **Error handling**: error boundaries, retries, fallback UI
- **Observability**: logging, metrics, tracing, RUM

## Security & privacy

- **Auth**: sessions/JWT, token storage, CSRF considerations
- **XSS/Content Security Policy**:
- **Sensitive data**: PII, redaction, retention

## Testing strategy

- **Unit**:
- **Integration**:
- **E2E**:

## Rollout & maintainability

- **Feature flags**:
- **Migration plan**:
- **Code organization**:

## Open questions

- <!-- Anything you’d ask the interviewer -->

