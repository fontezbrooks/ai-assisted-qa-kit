# CLAUDE.md — Playwright E2E Testing Rules

> Drop this into your project root (or `.claude/CLAUDE.md`) so Claude Code writes
> Playwright tests that are stable, maintainable, and CI-ready by default.
> Adapt the **Project specifics** block to your app, then leave the rest as guardrails.

## Project specifics (EDIT THIS)
- **App under test:** <name + short description>
- **Base URL:** read from `process.env.BASE_URL`; never hardcode environments.
- **Test account(s):** read from env (`TEST_USERNAME`/`TEST_PASSWORD`); never commit credentials.
- **Test directory:** `tests/`  ·  **Page objects:** `pages/`  ·  **Fixtures:** `fixtures/`  ·  **Data:** `utils/`

## Golden rules (always apply)
1. **One behavior per test.** A test asserts a single user-observable outcome. No "kitchen sink" specs.
2. **Arrange via fixtures, not copy-paste.** Auth and setup go in custom fixtures (`fixtures/test-options.ts`), injected into tests. A logged-in test starts with a one-line `loggedIn` fixture, never an inline login.
3. **Page Object Model.** Selectors live in `pages/*`. Page objects expose intent (`login()`, `addToCart()`), never raw locators to the spec. Specs read like user stories.
4. **No hard waits.** Never `waitForTimeout`. Use web-first assertions (`await expect(locator).toBeVisible()`) which auto-wait and auto-retry.
5. **User-facing locators first.** Prefer, in order: `getByRole` → `getByLabel`/`getByPlaceholder` → `getByText` → `getByTestId`. Avoid nth-child, deep CSS, and XPath.
6. **Deterministic data.** Each test provisions its own data (via API or fixture) and cleans up. No order-dependent tests, no shared mutable state.
7. **Tag critical paths** with `@smoke` so they can run as a fast subset.

## Code style
- TypeScript, `strict` mode. No `any`.
- Async/await everywhere; never mix in raw promises.
- Locators are readonly class fields initialized in the page object constructor.
- Assertions belong in the spec or in explicit `expect*`-named page methods — not hidden in action methods.

## What "good" looks like
```ts
// tests/checkout.spec.ts
import { test } from '../fixtures/test-options.js';
import { products, checkoutCustomer } from '../utils/test-data.js';

test('a user can complete an order @smoke', async ({ loggedIn, cartPage, checkoutPage }) => {
  await loggedIn.addToCart(products.backpack);
  await loggedIn.openCart();
  await cartPage.checkout();
  await checkoutPage.fillCustomerInfo(checkoutCustomer.firstName, checkoutCustomer.lastName, checkoutCustomer.postalCode);
  await checkoutPage.finish();
  await checkoutPage.expectOrderComplete();
});
```

## Anti-patterns (reject these in generated code)
- ❌ `await page.waitForTimeout(3000)` → use auto-waiting assertions.
- ❌ `page.locator('div > div:nth-child(3) span')` → use a role/label/test-id.
- ❌ Logging in by filling the form inside every test → use the `loggedIn` fixture.
- ❌ Asserting inside an action method → keep actions and assertions separate.
- ❌ Hardcoded URLs/credentials → read from env.
- ❌ `test.only` committed → never leave it in.

## CI expectations
- Config is env-driven: `retries: process.env.CI ? 2 : 0` (0 locally so flakes surface), `workers: process.env.CI ? 2 : undefined`.
- Artifacts on failure only: `screenshot: 'only-on-failure'`, `video: 'retain-on-failure'`, `trace: 'on-first-retry'`.
- HTML reporter with `open: 'never'`; report is uploaded as a CI artifact.

## When asked to write a new test
1. Identify the user-facing behavior and its single assertion.
2. Reuse or extend a page object — do not put selectors in the spec.
3. Use the smallest fixture that sets up state (`loggedIn`, data fixtures).
4. Pick the most resilient locator available in the DOM.
5. Tag it `@smoke` only if it's a critical revenue/auth path.
