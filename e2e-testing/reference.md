# E2E Testing Reference

## Testing Tool Comparison

| Tool | Best For | Language | Key Strength |
|------|----------|----------|-------------|
| **Playwright** | Web apps | JS/TS/Python/Go/.NET | Cross-browser, auto-wait, codegen |
| **Cypress** | Web apps | JavaScript | Developer-friendly, time-travel debugging |
| **Selenium** | Web apps | Multi-language | Mature ecosystem, wide browser support |
| **Appium** | Mobile apps | Multi-language | Cross-platform mobile testing |

## Selector Strategy (Priority Order)

1. `data-testid="submit-btn"` — Most stable, purpose-built for testing
2. `aria-label="Submit"` — Accessible and meaningful
3. `#submit-button` — ID-based, fairly stable
4. `text="Submit"` — User-facing text, readable but fragile to copy changes
5. `.btn-primary` — Class-based, AVOID (fragile, changes with styling)

## Test Structure Pattern

```
describe('User Journey: [Name]')
  beforeEach → Setup: clean state, seed data, authenticate
  
  test('Step 1: [Action]')
    → Navigate / interact
    → Assert expected state
    
  test('Step 2: [Action]')
    → Continue workflow
    → Assert data integrity
    
  afterEach → Cleanup: reset state, clear data
```

## E2E Test Categories

| Category | What It Tests | Example |
|----------|--------------|---------|
| **Happy Path** | Normal user flow succeeds | User signs up → verifies email → logs in |
| **Error Path** | Graceful error handling | Invalid login → shows error → allows retry |
| **Edge Cases** | Boundary conditions | Empty cart checkout, max-length input |
| **Cross-browser** | Consistency across browsers | Same flow in Chrome, Firefox, Safari |
| **Performance** | Load and response times | Page load < 3s, API response < 500ms |

## CI/CD Integration Checklist

- [ ] Tests run on every PR/commit
- [ ] Parallel execution enabled
- [ ] Test results reported in PR comments
- [ ] Screenshots/videos captured on failure
- [ ] Flaky test detection and quarantine
- [ ] Separate fast (smoke) and full (regression) test suites
- [ ] Environment provisioned and torn down automatically

## Key Metrics to Track

| Metric | Target | Why |
|--------|--------|-----|
| Test pass rate | > 95% | Overall health indicator |
| Test execution time | < 15 min (full suite) | Fast feedback loop |
| Flaky test rate | < 2% | Test reliability |
| Code coverage (E2E) | Critical paths 100% | Risk mitigation |
| Mean time to fix | < 1 day | Team responsiveness |

## Common Pitfalls

| Pitfall | Problem | Solution |
|---------|---------|----------|
| Testing everything E2E | Slow, brittle suite | Use testing pyramid: unit > integration > E2E |
| Hardcoded waits (`sleep`) | Slow and unreliable | Use auto-wait or explicit wait conditions |
| Shared test state | Tests fail randomly | Isolate each test with fresh state |
| Testing implementation details | Breaks on refactor | Test user-visible behavior only |
| No test data strategy | Inconsistent results | Use factories/fixtures, seed before each test |
| Ignoring flaky tests | Erodes trust in suite | Quarantine, investigate, fix or remove |
