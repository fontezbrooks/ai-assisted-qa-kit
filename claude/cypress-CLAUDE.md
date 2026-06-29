# CLAUDE.md — Cypress E2E Testing Rules

> Drop this into your project root (or `.claude/CLAUDE.md`) so Claude Code writes
> Cypress tests that are stable and maintainable by default.
> Edit the **Project specifics** block; keep the rest as guardrails.

## Project specifics (EDIT THIS)
- **App under test:** <name + short description>
- **Base URL:** set in `cypress.config.ts` `e2e.baseUrl` or `CYPRESS_BASE_URL`; never hardcode.
- **Test account(s):** read from `Cypress.env(...)`; never commit credentials.
- **Spec directory:** `cypress/e2e/`  ·  **Page objects/commands:** `cypress/support/`  ·  **Fixtures:** `cypress/fixtures/`

## Golden rules (always apply)
1. **One behavior per test.** Single user-observable assertion per `it`.
2. **Setup via custom commands.** Auth/setup go in `Cypress.Commands.add(...)` (e.g. `cy.login()`), defined in `cypress/support/commands.ts`. Prefer programmatic login (`cy.request`) over driving the UI.
3. **Page objects / app actions.** Keep selectors out of specs — centralize in command or page-object modules.
4. **No arbitrary waits.** Never `cy.wait(3000)`. Use `cy.intercept()` + `cy.wait('@alias')` for network, and assertion-based retry (`.should(...)`) for UI.
5. **Resilient selectors.** Prefer `data-cy`/`data-test` attributes via `cy.get('[data-cy=...]')` or `cy.contains(role/text)`. Avoid nth-child and brittle CSS.
6. **Deterministic data.** Seed via `cy.request`/tasks and reset state with `beforeEach`. No order-dependent specs.
7. **Tag critical paths** (e.g. with `@smoke` via grep) for a fast subset.

## Code style
- TypeScript. Chainers typed; no `any`.
- Use `cy.intercept` to assert on network rather than sleeping.
- Assertions in the spec or in explicitly named helpers — not buried in commands.

## What "good" looks like
```ts
// cypress/e2e/checkout.cy.ts
describe('Checkout', () => {
  beforeEach(() => {
    cy.login(Cypress.env('TEST_USERNAME'), Cypress.env('TEST_PASSWORD')); // programmatic
  });

  it('completes an order @smoke', () => {
    cy.addToCart('Sauce Labs Backpack');
    cy.get('[data-test=shopping-cart-link]').click();
    cy.get('[data-test=checkout]').click();
    cy.fillCheckout({ firstName: 'Ada', lastName: 'Lovelace', postalCode: '94016' });
    cy.get('[data-test=finish]').click();
    cy.get('[data-test=complete-header]').should('have.text', 'Thank you for your order!');
  });
});
```

## Anti-patterns (reject these)
- ❌ `cy.wait(3000)` → use `cy.intercept` + alias waits or `.should()` retry.
- ❌ Logging in through the UI in every test → `cy.login()` via `cy.request`.
- ❌ Brittle CSS/nth-child selectors → `data-cy` attributes.
- ❌ Hardcoded URLs/credentials → config + `Cypress.env`.
- ❌ Order-dependent specs → reset state in `beforeEach`.

## When asked to write a new test
1. Identify the single behavior + assertion.
2. Use/extend a custom command for setup (programmatic where possible).
3. Stub or wait on real network calls with `cy.intercept`.
4. Select via `data-cy`/role/text, never brittle CSS.
