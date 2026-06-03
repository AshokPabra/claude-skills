# E2E Testing Examples

## Example 1: Basic Login Flow (Playwright)

Test a standard login workflow end-to-end.

```typescript
import { test, expect } from '@playwright/test';

test.describe('User Login Journey', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/login');
  });

  test('successful login redirects to dashboard', async ({ page }) => {
    await page.fill('[data-testid="email-input"]', 'user@example.com');
    await page.fill('[data-testid="password-input"]', 'securePassword123');
    await page.click('[data-testid="login-button"]');

    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('[data-testid="welcome-message"]')).toContainText('Welcome');
  });

  test('invalid credentials show error message', async ({ page }) => {
    await page.fill('[data-testid="email-input"]', 'user@example.com');
    await page.fill('[data-testid="password-input"]', 'wrongPassword');
    await page.click('[data-testid="login-button"]');

    await expect(page.locator('[data-testid="error-alert"]')).toBeVisible();
    await expect(page.locator('[data-testid="error-alert"]')).toContainText('Invalid credentials');
    await expect(page).toHaveURL('/login');
  });
});
```

## Example 2: E-Commerce Checkout Flow (Playwright)

Full user journey from browsing to purchase confirmation.

```typescript
import { test, expect } from '@playwright/test';

test.describe('Checkout Journey', () => {
  test.beforeEach(async ({ page }) => {
    // Seed test user and authenticate
    await page.goto('/');
    await page.evaluate(() => {
      localStorage.setItem('auth_token', 'test-token-123');
    });
  });

  test('complete purchase flow', async ({ page }) => {
    // Step 1: Browse and add to cart
    await page.goto('/products');
    await page.click('[data-testid="product-card-1"] [data-testid="add-to-cart"]');
    await expect(page.locator('[data-testid="cart-count"]')).toHaveText('1');

    // Step 2: Go to cart and verify
    await page.click('[data-testid="cart-icon"]');
    await expect(page.locator('[data-testid="cart-item"]')).toHaveCount(1);

    // Step 3: Proceed to checkout
    await page.click('[data-testid="checkout-button"]');
    await expect(page).toHaveURL('/checkout');

    // Step 4: Fill shipping details
    await page.fill('[data-testid="address-input"]', '123 Test Street');
    await page.fill('[data-testid="city-input"]', 'Testville');
    await page.fill('[data-testid="zip-input"]', '12345');

    // Step 5: Fill payment and submit
    await page.fill('[data-testid="card-number"]', '4111111111111111');
    await page.fill('[data-testid="card-expiry"]', '12/28');
    await page.fill('[data-testid="card-cvc"]', '123');
    await page.click('[data-testid="place-order-button"]');

    // Step 6: Verify confirmation
    await expect(page).toHaveURL(/\/order-confirmation/);
    await expect(page.locator('[data-testid="order-status"]')).toContainText('confirmed');
  });
});
```

## Example 3: API + UI Integration Test (Playwright)

Verify that API responses correctly render in the UI.

```typescript
import { test, expect } from '@playwright/test';

test.describe('API Integration', () => {
  test('user profile data loads from API and displays correctly', async ({ page }) => {
    // Intercept API call to verify it happens
    const apiPromise = page.waitForResponse(resp =>
      resp.url().includes('/api/users/profile') && resp.status() === 200
    );

    await page.goto('/profile');

    // Wait for API response
    const response = await apiPromise;
    const userData = await response.json();

    // Verify UI reflects API data
    await expect(page.locator('[data-testid="user-name"]')).toHaveText(userData.name);
    await expect(page.locator('[data-testid="user-email"]')).toHaveText(userData.email);
  });

  test('handles API failure gracefully', async ({ page }) => {
    // Mock API failure
    await page.route('**/api/users/profile', route =>
      route.fulfill({ status: 500, body: 'Internal Server Error' })
    );

    await page.goto('/profile');

    await expect(page.locator('[data-testid="error-state"]')).toBeVisible();
    await expect(page.locator('[data-testid="retry-button"]')).toBeVisible();
  });
});
```

## Example 4: CI/CD Pipeline Configuration (GitHub Actions)

Run E2E tests automatically on every PR.

```yaml
# .github/workflows/e2e-tests.yml
name: E2E Tests

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps

      - name: Start application
        run: npm run start &
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/testdb

      - name: Wait for app
        run: npx wait-on http://localhost:3000 --timeout 30000

      - name: Run E2E tests
        run: npx playwright test --workers=4

      - name: Upload test report
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: playwright-report
          path: playwright-report/
```

## Example 5: Parallel Cross-Browser Testing (Playwright Config)

Configure E2E tests to run across multiple browsers in parallel.

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './tests/e2e',
  fullyParallel: true,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 4 : undefined,
  reporter: [
    ['html'],
    ['junit', { outputFile: 'test-results/results.xml' }],
  ],
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'mobile-chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'mobile-safari',
      use: { ...devices['iPhone 13'] },
    },
  ],
  webServer: {
    command: 'npm run start',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```
