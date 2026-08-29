---
name: e2e-testing-playwright
description: |
  Complete guide for writing resilient, fast, flake-free End-to-End (E2E) and integration tests with Playwright.
  Covers Page Object Model (POM), locator best practices (role-based, accessibility-first), mock APIs/network interception,
  authentication state caching (storageState), visual regression, and CI execution.

  Use when generating Playwright test suites, debugging test timeouts, or setting up automated UI QA.
license: MIT
metadata:
  version: v1
---

# Playwright E2E & Integration Testing Standard

Write reliable, maintainable, and high-speed Playwright tests following industry best practices.

---

## 1. Locator Strategy: Accessibility & User-First

Never rely on fragile CSS selectors or XPath. Always prioritize accessible roles and visible text:

```ts
// ❌ Avoid fragile selectors
await page.locator('div > .btn-wrapper > button.submit-3').click();

// ✅ Best: Semantic, accessible role queries
await page.getByRole('button', { name: /submit order/i }).click();
await page.getByRole('heading', { level: 1, name: 'Dashboard' });
await page.getByLabel('Email Address').fill('user@example.com');
await page.getByPlaceholder('Search products...').fill('Laptop');
await page.getByTestId('order-status-badge'); // Fallback when role is absent
```

---

## 2. Page Object Model (POM) Structure

Encapsulate DOM queries and reusable user interactions in dedicated Page Objects:

```ts
// pages/LoginPage.ts
import { expect, type Page, type Locator } from '@playwright/test';

export class LoginPage {
  readonly page: Page;
  readonly emailInput: Locator;
  readonly passwordInput: Locator;
  readonly submitButton: Locator;
  readonly errorMessage: Locator;

  constructor(page: Page) {
    this.page = page;
    this.emailInput = page.getByLabel('Email');
    this.passwordInput = page.getByLabel('Password');
    this.submitButton = page.getByRole('button', { name: 'Sign In' });
    this.errorMessage = page.getByRole('alert');
  }

  async goto() {
    await this.page.goto('/login');
  }

  async login(email: string, pass: string) {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(pass);
    await this.submitButton.click();
  }

  async expectError(message: string) {
    await expect(this.errorMessage).toContainText(message);
  }
}
```

---

## 3. Persistent Authentication (`storageState`)

Avoid logging in through the UI for every single test. Cache authenticated cookies/localStorage once:

```ts
// tests/auth.setup.ts
import { test as setup, expect } from '@playwright/test';

const authFile = 'playwright/.auth/user.json';

setup('authenticate', async ({ page }) => {
  await page.goto('/login');
  await page.getByLabel('Email').fill('admin@test.com');
  await page.getByLabel('Password').fill('password123');
  await page.getByRole('button', { name: 'Sign In' }).click();
  await page.waitForURL('/dashboard');
  
  await page.context().storageState({ path: authFile });
});
```

---

## 4. API Mocking & Network Interception

Intercept network requests for deterministic, hermetic tests:

```ts
test('shows error message on API 500 failure', async ({ page }) => {
  await page.route('**/api/v1/analytics', async route => {
    await route.fulfill({
      status: 500,
      contentType: 'application/json',
      body: JSON.stringify({ message: 'Internal Server Error' }),
    });
  });

  await page.goto('/analytics');
  await expect(page.getByText('Unable to load analytics metrics')).toBeVisible();
});
```

---

## 5. Web Assertions & Auto-Waiting

Always use Playwright's async `expect()` assertions that automatically retry until timeout:

```ts
// ✅ Auto-retrying assertions
await expect(page.getByRole('dialog')).toBeVisible({ timeout: 5000 });
await expect(page.getByRole('button', { name: 'Save' })).toBeEnabled();
await expect(page).toHaveURL(/\/settings\/billing/);
```
