# Practice exercises + links

## Suggested practice exercises

- Write unit tests for a `formatCurrency(amount, locale)` helper (edge cases: negative, rounding, invalid inputs).
- Unit test a validation function (email/password) with a table of cases.
- Unit test a reducer/state transition function for a UI flow (idle → loading → success/error).
- Refactor a function to remove a dependency on `Date.now()` by injecting a clock, then test it.
- Write an integration test for a component that:
  - renders a loading state
  - renders data after a mocked API response
  - renders an error message on failure
- Write an integration test for a form submit flow (client validation + server error response + retry).
- Write an E2E test for a login flow and make it non-flaky (seed user, stable selectors, no sleeps).
- Add failure diagnostics: screenshot/trace on failure and a clean retry strategy.
- Pick one component and write:
  - a Jest unit test for its pure helpers
  - an RTL integration test for its user-visible behavior
  - a Cypress E2E test for the full flow that uses it
- Write one test that over-mocks (brittle), then rewrite it to mock at the HTTP boundary and assert on user-visible behavior.
- Add a contract test for an API response shape (schema validation) and show how it fails fast when the backend changes.
- Add a visual regression test for a Button component (states + variants) and stabilize it (disable animations, stable data).
- Take one flaky CI test and fix it (remove sleeps, isolate data, add trace artifacts).

## Links / references

- Testing Library guiding principle: https://testing-library.com/docs/guiding-principles
- Jest docs: https://jestjs.io/docs/getting-started
- Testing Library: queries by role: https://testing-library.com/docs/queries/byrole
- Playwright docs: https://playwright.dev/docs/intro
- Cypress docs: https://docs.cypress.io/
- Pact (contract testing): https://pact.io/
- Storybook test runner (component testing): https://storybook.js.org/docs/writing-tests/test-runner
- Storybook interaction testing: https://storybook.js.org/docs/writing-tests/interaction-testing

