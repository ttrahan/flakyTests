# Custom Instructions for CircleCI Chunk Agent

This file provides guidelines for how Chunk should fix flaky tests in this repository.

## Project Context

This is a demonstration repository comparing different CI/CD tools' approaches to handling flaky tests. The tests are intentionally designed to be flaky to showcase detection and remediation capabilities.

## Command Restrictions

- **Do NOT use `sleep()` or fixed delays** - Instead, use proper async/await patterns with timeouts
- **Do NOT modify stable tests** - Only fix tests in `flaky-tests.test.js` and `teamcity-pattern-tests.test.js`
- **Do NOT modify regression tests** - Tests in `regression-tests.test.js` are intentionally failing and should remain unchanged
- **Avoid `Math.random()` in test logic** - Replace with deterministic patterns or proper mocking

## Code Style Preferences

- **Use async/await syntax** over Promise chains for better readability
- **Prefer explicit timeouts** over random delays (e.g., `setTimeout` with fixed values)
- **Use proper Jest matchers** - Prefer `toBe()`, `toEqual()`, `resolves`, `rejects` over manual assertions
- **Add descriptive test names** that explain what the test validates

## Flaky Test Fix Strategies

### Race Conditions
- Replace `setTimeout` with `Promise` and proper `await` statements
- Use `waitFor` or similar utilities for async operations
- Add proper synchronization mechanisms

### Resource-Dependent Tests
- Mock resource usage instead of relying on actual system resources
- Use Jest's `jest.useFakeTimers()` for time-dependent tests
- Isolate tests from system state

### Network-Dependent Tests
- Mock network calls using Jest's mocking capabilities
- Use `jest.mock()` for external dependencies
- Avoid real network calls in tests

### Order-Dependent Tests
- Remove shared state between tests
- Use `beforeEach` or `afterEach` to reset state
- Make tests independent and idempotent

### Time-Sensitive Tests
- Mock `Date.now()` and time-related functions
- Use `jest.useFakeTimers()` for time-based logic
- Avoid tests that depend on current time

## Security Considerations

- Do not introduce security vulnerabilities while fixing tests
- Avoid using `eval()` or similar dangerous functions
- Maintain test isolation and don't expose sensitive data

## Documentation Standards

- Add comments explaining why a test was flaky and how it was fixed
- Update test descriptions to reflect the fix
- Document any new dependencies or utilities introduced

## Test Categories

- **Flaky Tests** (`flaky-tests.test.js`): These can be fixed
- **Stable Tests** (`stable-tests.test.js`): Do not modify - these are reference tests
- **Regression Tests** (`regression-tests.test.js`): Do not modify - these are intentionally failing
- **TeamCity Pattern Tests** (`teamcity-pattern-tests.test.js`): These can be fixed

## Validation Requirements

- All fixed tests must pass consistently (at least 10 consecutive runs)
- Fixed tests should maintain their original intent
- Do not change test logic, only fix the flakiness source
- Ensure fixes don't introduce new flaky behavior

## Example Fix Pattern

**Before (Flaky):**
```javascript
test('async operation without proper wait', async () => {
  let value = null;
  setTimeout(() => {
    value = 'completed';
  }, Math.random() * 100);
  await new Promise(resolve => setTimeout(resolve, 50));
  expect(value).toBe('completed');
});
```

**After (Fixed):**
```javascript
test('async operation without proper wait', async () => {
  const value = await new Promise(resolve => {
    setTimeout(() => {
      resolve('completed');
    }, 100);
  });
  expect(value).toBe('completed');
});
```

## Notes

- This repository is for demonstration purposes
- Focus on fixing the root cause of flakiness, not masking it
- Maintain test readability and maintainability
- Consider the educational value of the fix for the demo

