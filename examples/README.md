# Before / After — what these rules actually change

Same prompt ("write a Playwright test that logs in and adds a product to the cart"),
same model — the only difference is whether this kit's `CLAUDE.md` / `.cursorrules` are
installed. This is the value: the AI stops producing flaky, unmaintainable tests.

---

## ❌ BEFORE (no rules) — what AI gives you by default

```ts
import { test, expect } from '@playwright/test';

test('add to cart', async ({ page }) => {
  await page.goto('https://www.saucedemo.com');
  await page.fill('#user-name', 'standard_user');
  await page.fill('#password', 'secret_sauce');
  await page.click('#login-button');
  await page.waitForTimeout(2000);                       // ❌ hard wait
  await page.click('div.inventory_item:nth-child(1) button'); // ❌ brittle selector
  await page.waitForTimeout(1000);                       // ❌ hard wait
  const badge = await page.textContent('.shopping_cart_badge');
  expect(badge).toBe('1');                               // ❌ no auto-retry, can race
});
```

**Problems:** hardcoded URL + credentials, two `waitForTimeout` calls (slow + flaky),
an nth-child selector that breaks when the catalog reorders, login duplicated into every
test, and a non-retrying assertion that races the UI.

---

## ✅ AFTER (kit installed) — what AI gives you instead

```ts
// tests/cart.spec.ts
import { test } from '../fixtures/test-options.js';
import { products } from '../utils/test-data.js';

test('adding a product updates the cart badge @smoke', async ({ loggedIn }) => {
  await loggedIn.addToCart(products.backpack);
  await loggedIn.expectCartCount(1);
});
```

```ts
// pages/InventoryPage.ts (generated to match conventions)
async addToCart(productName: string): Promise<void> {
  const slug = productName.toLowerCase().replace(/\s+/g, '-');
  await this.byTestId(`add-to-cart-${slug}`).click();
}
async expectCartCount(count: number): Promise<void> {
  await expect(this.cartBadge).toHaveText(String(count)); // auto-waits + retries
}
```

**What changed:** login moved into the `loggedIn` fixture (one line, reused everywhere),
test data centralized, a stable test-id locator, a web-first assertion that auto-waits,
no `waitForTimeout`, no secrets in the spec, and a `@smoke` tag for the critical path.

---

## Why it matters
The "before" test passes today and randomly fails next week — the exact pattern that
makes teams stop trusting their suite. The "after" test is the kind a senior SDET would
write, produced at AI speed. That's what you're installing.
