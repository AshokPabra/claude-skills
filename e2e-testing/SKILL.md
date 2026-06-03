---
name: e2e-testing
description: End-to-end testing methodology — plan, design, automate, and execute E2E tests for web and mobile applications following industry best practices.
---

# End-to-End (E2E) Testing Skill

Use this skill when building, planning, or executing end-to-end tests for any application. E2E testing validates an entire application workflow from beginning to end, confirming that integrated components (front end, back end, databases, third-party services) work smoothly together under real-world user scenarios.

## When to Use E2E Testing

- Validating complete user journeys (login → action → logout)
- Verifying integrated components work together (UI + API + DB)
- Testing before major releases or deployments
- Regression testing in CI/CD pipelines
- Evaluating UX quality from a user's perspective

## The 7-Step E2E Testing Process

### 1. Test Planning & Management
- Identify the most critical user journeys and workflows
- Document test scenarios that reflect real-world usage
- Organize test cases into test suites grouped by user journeys, features, or modules
- Prioritize: test the most important workflows first (e.g., login, checkout, signup)

### 2. Test Environment Setup
- Create a staging environment that closely mirrors production
- Integrate all necessary components: APIs, databases, third-party services
- Use synthetic data when real production data is unavailable
- Ensure deep isolation to protect test results from outside interference

### 3. Testing Tool Selection
- **Web applications**: Playwright (recommended), Cypress, Selenium
- **Mobile applications**: Appium
- Always choose automation tools — they streamline execution and save time
- Match tool to application type and team expertise

### 4. Test Creation & Execution
- Write test scripts covering all use scenarios across front end, back end, databases, and APIs
- Validate data integrity throughout test execution
- Monitor application behavior as workflows progress
- Avoid manual testing when automation is available

### 5. Result Validation
- Compare test results against expected outcomes
- Evaluate test coverage metrics
- Identify deficiencies and errors in the code
- Document findings clearly for the team

### 6. Defect Resolution
- Study error messages and performance logs to find root causes
- Debug and fix issues
- Re-run tests to verify fixes
- Run QA functions to establish seamless data quality

### 7. Automation & CI/CD Integration
- Integrate automated tests into the CI/CD pipeline
- Run tests on every commit/PR to catch issues early
- Use parallel test execution to reduce feedback time
- Schedule regular test runs for ongoing regression detection

## Best Practices (Always Follow)

1. **Triage essential workflows** — Prioritize critical user journeys (login, checkout, core features) before edge cases
2. **Choose stable selectors** — Use `data-testid` or `data-*` attributes, never fragile class names
3. **Ensure test independence** — Each test must be repeatable and free from external dependencies (network timing, third-party availability)
4. **Keep tests manageable** — Offload logic verification to unit/integration tests; keep E2E focused on user behavior and system synchronization
5. **Automate everything** — Minimize human error, ensure proper frequency, use cloud-based testing for cross-browser/mobile
6. **Dedicate time to API testing** — Test API functionality and logic before evaluating UI
7. **Run tests in parallel** — Cut execution time and get faster feedback
8. **Revisit and revise** — Periodically review test cases as the application evolves
9. **Foster team collaboration** — Keep DevOps and QA teams aligned; educate on business requirements
