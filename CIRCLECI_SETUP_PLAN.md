# CircleCI Chunk Agent Setup Plan

## Overview

This document outlines the plan and implementation for integrating CircleCI's Chunk agent into the flaky test demonstration repository. Chunk is CircleCI's AI-powered agent that automatically detects and fixes flaky tests by creating pull requests with remediation code.

## Implementation Summary

### ✅ Completed Tasks

1. **CircleCI Configuration** (`.circleci/config.yml`)
   - Created main pipeline configuration
   - Configured to run Jest tests with JUnit XML output
   - Set up test result storage and artifact collection
   - Uses Node.js 18.20 Docker image

2. **Chunk Agent Setup** (`.circleci/cci-agent-setup.yml`)
   - Created environment setup file for Chunk
   - Defines the environment Chunk uses to run and fix tests
   - Installs npm dependencies
   - Verifies test environment readiness
   - Automatically detected by Chunk when present in the default branch

3. **Custom Instructions** (`.circleci/fix-flaky-test.md`)
   - Created comprehensive guidelines for Chunk's fix strategies
   - Defines fix approaches for different flaky test types:
     - Race conditions
     - Resource-dependent tests
     - Network-dependent tests
     - Order-dependent tests
     - Time-sensitive tests
   - Specifies which tests can be fixed (excludes regression tests)
   - Provides code style preferences and security considerations

4. **Documentation Updates**
   - Updated README.md with CircleCI Chunk setup instructions
   - Added CircleCI to the comparison table
   - Included Chunk in the demo script
   - Updated package.json description

## Next Steps for Setup

### Step 1: Connect Repository to CircleCI (5 minutes)

1. **Sign up/Log in to CircleCI**
   - Go to https://circleci.com/
   - Sign up with GitHub/GitLab/Bitbucket (if not already signed up)

2. **Add Project**
   - Click "Add Projects" in the sidebar
   - Find and select this repository (`flakyTests`)
   - Click "Set Up Project"
   - CircleCI will detect `.circleci/config.yml` automatically

3. **Run First Build**
   - The first build will run automatically
   - Verify tests execute correctly
   - Check that JUnit XML is generated in `test-results/`

### Step 2: Enable Chunk Agent (10 minutes)

1. **Navigate to Organization Settings**
   - In CircleCI, go to your organization settings
   - Click "Chunk Tasks" in the left sidebar

2. **Assign Flaky Test Fixing Task**
   - Click "Assign Task" button
   - Select this project (`flakyTests`)
   - Configure task parameters:
     ```
     Run Frequency: Daily (recommended for demo)
     Max Tests to Fix: 5-10 per run
     Solutions per Test: 3
     Validation Runs: 5-10 consecutive passes
     Max Concurrent PRs: 2-3
     ```

3. **Start the Task**
   - Click "Start Task"
   - Chunk will begin analyzing your test suite after a few builds

### Step 3: Monitor Chunk Activity

1. **Initial Analysis Period** (2-3 builds)
   - Chunk needs test history to identify flaky tests
   - Run builds multiple times to establish patterns
   - You can trigger builds manually or wait for scheduled runs

2. **Review Chunk's Work**
   - Check "Chunk Tasks" dashboard for activity
   - Review pull requests created by Chunk
   - Each PR will contain:
     - Fixed test code
     - Explanation of the fix
     - Validation results (multiple test runs)

3. **Merge Fixes**
   - Review Chunk's fixes carefully
   - Ensure fixes align with project standards
   - Merge PRs when satisfied
   - Chunk will continue monitoring for new flaky tests

## How Chunk Works

### Detection Phase

1. **Test History Analysis**
   - Chunk analyzes test results across multiple builds
   - Identifies tests with inconsistent pass/fail patterns
   - Calculates flakiness rates

2. **Pattern Recognition**
   - Detects common flaky test patterns:
     - Race conditions
     - Timing issues
     - Resource dependencies
     - Order dependencies

### Remediation Phase

1. **Fix Generation**
   - Chunk generates fixes based on:
     - Custom instructions in `fix-flaky-test.md`
     - Best practices for the test framework (Jest)
     - Patterns learned from similar fixes

2. **Validation**
   - Each fix is validated with multiple test runs (5-10)
   - Only fixes that pass consistently are included in PRs
   - Failed fix attempts are logged for learning

3. **Pull Request Creation**
   - Chunk creates PRs with:
     - Fixed test code
     - Clear commit messages
     - Validation results
     - Explanation of changes

## Configuration Files Explained

### `.circleci/config.yml`
**Purpose**: Main CircleCI pipeline configuration

**Key Features**:
- Runs Jest tests with JUnit XML output
- Stores test results for CircleCI UI
- Collects coverage artifacts
- Uses Node.js 18.20 (matches local development)

**Why it matters**: CircleCI needs this to run your tests and generate the test history that Chunk analyzes.

### `.circleci/cci-agent-setup.yml`
**Purpose**: Environment setup for Chunk agent

**Key Features**:
- Defines the exact environment Chunk uses
- Installs dependencies
- Verifies environment readiness

**Why it matters**: Chunk needs a consistent environment to reliably test fixes. This ensures Chunk's test runs match your actual CI environment.

### `.circleci/fix-flaky-test.md`
**Purpose**: Custom instructions guiding Chunk's behavior

**Key Features**:
- Defines fix strategies for different flaky test types
- Sets code style preferences
- Specifies which tests can be fixed
- Provides security and documentation guidelines

**Why it matters**: This file ensures Chunk fixes tests in a way that aligns with your project's standards and practices.

## Expected Outcomes

### Short Term (First Week)
- Chunk analyzes test suite and identifies flaky tests
- First PRs created with fixes for obvious flaky tests
- Test suite becomes more stable

### Medium Term (First Month)
- Most flaky tests identified and fixed
- Reduced false positives in CI/CD
- Improved developer confidence in test suite

### Long Term (Ongoing)
- Chunk continues monitoring for new flaky tests
- Automatic remediation of newly introduced flakiness
- Sustained test suite reliability

## Comparison with Other Tools

### TeamCity
- **Strengths**: Excellent detection and visualization
- **Limitation**: Requires manual fixes
- **Chunk Advantage**: Automated remediation

### Jenkins
- **Strengths**: Highly customizable
- **Limitation**: Requires extensive configuration and plugins
- **Chunk Advantage**: Zero-configuration automated fixes

### CircleCI Chunk
- **Strengths**: 
  - Automatic detection AND remediation
  - Creates actual code fixes, not just reports
  - Validates fixes before creating PRs
  - Learns from your codebase patterns
- **Best For**: Teams wanting hands-off flaky test management

## Troubleshooting

### Chunk Not Detecting Setup File
- Ensure `cci-agent-setup.yml` is in `.circleci/` directory
- Verify file is in the default branch (usually `main` or `master`)
- Check file syntax is valid YAML

### Chunk Not Creating PRs
- Verify Chunk task is active in Organization Settings
- Ensure enough builds have run (Chunk needs test history)
- Check task parameters (max tests, validation runs, etc.)
- Review Chunk task logs for errors

### Fixes Not Working
- Review custom instructions in `fix-flaky-test.md`
- Check if fix strategies need adjustment
- Verify test environment matches `cci-agent-setup.yml`
- Consider providing more specific instructions

## Resources

- [CircleCI Chunk Documentation](https://circleci.com/docs/chunk/)
- [CircleCI Chunk Blog Post](https://circleci.com/blog/fix-flaky-tests-with-chunk/)
- [CircleCI Config Reference](https://circleci.com/docs/config-intro/)
- [Jest Documentation](https://jestjs.io/docs/getting-started)

## Support

For issues or questions:
1. Check CircleCI Chunk documentation
2. Review Chunk task logs in CircleCI UI
3. Check PR descriptions for fix explanations
4. Review custom instructions file for guidance

---

**Status**: ✅ Setup Complete - Ready for CircleCI Integration

**Last Updated**: Implementation completed with all configuration files and documentation in place.

