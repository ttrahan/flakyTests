# Flaky Test Demo: TeamCity vs Jenkins vs CircleCI Chunk

This demo project showcases different approaches to handling flaky tests: TeamCity's intelligent detection, Jenkins' manual approach, and CircleCI Chunk's automated remediation.

## 🎯 What This Demo Shows

### Test Suites Included

1. **Flaky Tests** (`tests/flaky-tests.test.js`)
   - Race condition tests (fail ~30% of the time)
   - Resource-dependent tests (fail under load)
   - Network-dependent tests (simulate timeouts)
   - Order-dependent tests (fail when run in parallel)
   - Time-sensitive tests (fail at certain times)

2. **Stable Tests** (`tests/stable-tests.test.js`)
   - Always pass - demonstrates TeamCity can identify truly stable tests

3. **Regression Tests** (`tests/regression-tests.test.js`)
   - Always fail - demonstrates TeamCity distinguishes real bugs from flaky tests

## 🚀 Quick Start - TeamCity Cloud (Recommended)

### Step 1: Sign up for TeamCity Cloud (2 minutes)
```bash
# No installation needed!
# Go to: https://www.jetbrains.com/teamcity/cloud/
# Click "Start Free Trial"
# Sign up with email or GitHub
```

### Step 2: Create New Project
1. Click "Create Project"
2. Connect your Git repository (or use the provided demo)
3. TeamCity auto-detects the Node.js project
4. Click "Create"

### Step 3: Run the Build
1. Click "Run" on your build configuration
2. Watch as tests execute
3. After 2-3 builds, see the magic:
   - Flaky tests get special indicators 🔄
   - Flakiness percentages appear
   - Test history shows patterns
   - Build stays green despite flaky failures!

### What You'll See in TeamCity

After running the build 3-5 times, TeamCity will show:

- **Flaky Test Indicators**: Special icons next to non-deterministic tests
- **Flakiness Rate**: "Fails 32% of the time" annotations
- **Smart Build Status**: "Passed with known flaky failures" vs "Failed"
- **Test History Graph**: Visual timeline of each test's behavior
- **Investigation Assignment**: One-click "Assign to Developer" for flaky tests
- **Muted Tests Tab**: Flaky tests moved here but still running

## 🔧 Jenkins Comparison (Manual Setup Required)

### Step 1: Run Jenkins with Docker
```bash
# Start Jenkins
docker run -d \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  --name jenkins \
  jenkins/jenkins:lts

# Get initial admin password
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

### Step 2: Configure Jenkins (10-15 minutes)
1. Navigate to http://localhost:8080
2. Install suggested plugins
3. Install additional plugins:
   - NodeJS Plugin
   - Test Results Analyzer (for basic test history)
   - Flaky Test Handler (for manual flaky marking)
4. Configure NodeJS installation in Global Tools

### Step 3: Create Pipeline Job
1. New Item → Pipeline
2. Paste the provided `Jenkinsfile`
3. Run the build multiple times

### What You'll See in Jenkins

- **No Flaky Detection**: All failures treated the same
- **Blind Retries**: Entire test suite re-runs
- **Manual Investigation**: Click through builds to find patterns
- **No Built-in Intelligence**: Need plugins + custom scripts
- **Poor Visibility**: Test results buried in logs

## 🤖 CircleCI Chunk Agent (Automated Remediation)

CircleCI Chunk is an AI-powered agent that not only detects flaky tests but also automatically fixes them by creating pull requests with remediation code.

### Step 1: Set Up CircleCI Project (5 minutes)

1. **Connect Repository to CircleCI**
   ```bash
   # Go to: https://circleci.com/
   # Sign up or log in
   # Click "Add Projects"
   # Select this repository
   # Click "Set Up Project"
   ```

2. **Verify Configuration**
   - CircleCI will automatically detect `.circleci/config.yml`
   - The configuration runs tests and generates JUnit XML reports
   - First build will run automatically

### Step 2: Enable Chunk Agent

1. **Navigate to Organization Settings**
   - Go to your CircleCI organization settings
   - Click on "Chunk Tasks" in the sidebar

2. **Assign a Flaky Test Fixing Task**
   - Click "Assign Task"
   - Select this project
   - Configure task parameters:
     - **Run Frequency**: Daily (recommended for demo)
     - **Max Tests to Fix**: 5-10 per run
     - **Solutions per Test**: 3
     - **Validation Runs**: 5-10 consecutive passes required
     - **Max Concurrent PRs**: 2-3

3. **Start the Task**
   - Click "Start Task"
   - Chunk will begin analyzing your test suite

### Step 3: Watch Chunk Work

After Chunk analyzes your tests (usually after 2-3 builds), you'll see:

- **Automatic Detection**: Chunk identifies flaky tests from test history
- **Pull Requests Created**: Chunk opens PRs with fixes for flaky tests
- **Fix Validation**: Each fix is validated with multiple test runs
- **Review & Merge**: Review Chunk's fixes and merge when satisfied

### What You'll See with CircleCI Chunk

- **Automated Fixes**: Chunk creates PRs with actual code fixes, not just detection
- **Intelligent Remediation**: Fixes address root causes (race conditions, timing issues, etc.)
- **Custom Instructions**: Chunk follows guidelines in `.circleci/fix-flaky-test.md`
- **Validation**: Each fix is tested multiple times before PR creation
- **Transparency**: Full visibility into what Chunk changed and why

### Chunk Configuration Files

This repository includes:

1. **`.circleci/config.yml`**: Main CircleCI configuration for running tests
2. **`.circleci/cci-agent-setup.yml`**: Environment setup for Chunk agent
3. **`.circleci/fix-flaky-test.md`**: Custom instructions guiding Chunk's fix strategies

### Example Chunk Fix

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

**After (Fixed by Chunk):**
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

## 📊 The Comparison

| Feature | TeamCity | Jenkins | CircleCI Chunk |
|---------|----------|---------|----------------|
| **Setup Time** | 2 minutes (Cloud) | 15+ minutes (self-hosted) | 5 minutes |
| **Flaky Test Detection** | ✅ Automatic | ❌ Requires plugins | ✅ Automatic |
| **Intelligent Retry** | ✅ Only flaky tests | ❌ Entire suite | ✅ Only flaky tests |
| **Historical Analysis** | ✅ Built-in with UI | ❌ Manual or scripted | ✅ Automatic |
| **Test Categorization** | ✅ Flaky/Stable/Failed | ❌ Pass/Fail only | ✅ Flaky/Stable/Failed |
| **Investigation Tracking** | ✅ Native feature | ❌ External tracking | ✅ Via PRs |
| **Muting with Visibility** | ✅ Supported | ❌ Tests disabled entirely | ✅ Fixed via PRs |
| **Statistical Analysis** | ✅ Automatic | ❌ DIY with scripts | ✅ Automatic |
| **UI Test Intelligence** | ✅ Rich visualizations | ❌ Basic reports | ✅ Via CircleCI UI |
| **Automated Remediation** | ❌ Manual fixes | ❌ Manual fixes | ✅ **Automatic PRs** |
| **Fix Validation** | ❌ Manual | ❌ Manual | ✅ **Automatic (5-10 runs)** |
| **Custom Fix Strategies** | ❌ N/A | ❌ N/A | ✅ **Via instructions file** |

## 🎪 Demo Script for Presentations

### Act 1: Show the Problem (Jenkins)
1. Run build in Jenkins
2. Watch random tests fail
3. Re-run entire pipeline
4. Show lack of visibility into which tests are flaky
5. Demonstrate time wasted on investigation

### Act 2: Show the Solution (TeamCity)
1. Run same tests in TeamCity
2. After 3 builds, show flaky test indicators
3. Demonstrate how build passes despite flaky failures
4. Show test history and patterns
5. Assign investigation to team member
6. Show time saved with intelligent retries

### Act 3: The "Wow" Moment
1. Introduce a new genuine bug (uncomment a line in regression tests)
2. TeamCity immediately flags it as a NEW failure, not flaky
3. Jenkins treats it the same as any other failure
4. Show how TeamCity's intelligence accelerates debugging

### Act 4: CircleCI Chunk - The Future (Optional)
1. Show CircleCI running the same tests
2. After a few builds, show Chunk detecting flaky tests
3. Demonstrate Chunk creating a PR with an automated fix
4. Show the fix validation process
5. Highlight how Chunk goes beyond detection to actual remediation

## 🔬 Understanding the Test Behaviors

### Why Tests Are Flaky

The demo includes realistic flaky test patterns:

```javascript
// Race Condition Example
test('async operation without proper wait', async () => {
  let value = null;
  setTimeout(() => {
    value = 'completed';
  }, Math.random() * 100); // Random delay
  
  await new Promise(resolve => setTimeout(resolve, 50));
  expect(value).toBe('completed'); // Fails if timeout hasn't completed
});
```

### How TeamCity Detects Them

TeamCity analyzes:
1. **Pass/Fail Patterns**: Test fails 30% of builds → likely flaky
2. **Failure Clustering**: Fails on specific agents → environment issue
3. **Time Correlation**: Fails at certain times → time-dependent
4. **Change Correlation**: Started failing after unrelated change → flaky

## 📈 Metrics That Matter

After running this demo, you can show stakeholders:

- **Time Saved**: 5-10 minutes per build not re-running stable tests
- **Confidence Restored**: Developers trust green builds again
- **MTTR Reduced**: 40% faster issue identification
- **Productivity Gain**: Less context switching from false alarms

## 🎬 Recording Your Demo

For maximum impact:

1. **Split Screen**: Jenkins on left, TeamCity on right
2. **Run Simultaneously**: Show same commit building on both
3. **Highlight Differences**:
   - Jenkins: Manual investigation, confusion, wasted time
   - TeamCity: Automatic detection, clear categorization, quick resolution
4. **Show the UI**: TeamCity's test report is the star of the show

## 💡 Pro Tips

1. **Run builds 5+ times** before demo to establish flakiness patterns
2. **Use TeamCity's REST API** to show flakiness data programmatically
3. **Set up notifications** to show how TeamCity alerts differ for flaky vs real failures
4. **Create a dashboard** showing flakiness trends over time

## 🚦 Success Criteria

Your demo is successful when viewers say:

> "I had no idea we were wasting so much time on flaky tests!"

> "TeamCity actually knows which tests are problematic?"

> "We need this intelligence in our CI/CD pipeline!"

## 📚 Learn More

- [TeamCity Test Intelligence Docs](https://www.jetbrains.com/help/teamcity/test-intelligence.html)
- [Migration Planning Kit](https://www.jetbrains.com/teamcity/migration/)
- [TeamCity Cloud Free Trial](https://www.jetbrains.com/teamcity/cloud/)
- [CircleCI Chunk Documentation](https://circleci.com/docs/chunk/)
- [CircleCI Chunk Blog Post](https://circleci.com/blog/fix-flaky-tests-with-chunk/)

## 🔧 CircleCI Chunk Setup Details

### Configuration Files

- **`.circleci/config.yml`**: Main CircleCI pipeline configuration
  - Runs Jest tests with JUnit XML output
  - Stores test results and coverage artifacts
  - Uses Node.js 18.20 Docker image

- **`.circleci/cci-agent-setup.yml`**: Chunk agent environment setup
  - Defines the environment Chunk uses to run and fix tests
  - Installs npm dependencies
  - Verifies test environment readiness
  - Automatically detected by Chunk when present

- **`.circleci/fix-flaky-test.md`**: Custom instructions for Chunk
  - Defines fix strategies for different flaky test types
  - Sets code style preferences
  - Specifies which tests can be fixed (not regression tests)
  - Provides examples of good fixes

### Chunk Task Configuration

When setting up Chunk in CircleCI:

1. **Run Frequency**: Start with daily to see results quickly
2. **Max Tests to Fix**: 5-10 per run (adjust based on your needs)
3. **Solutions per Test**: 3 attempts per flaky test
4. **Validation Runs**: 5-10 consecutive passes required before PR creation
5. **Max Concurrent PRs**: 2-3 to avoid overwhelming reviewers

### Monitoring Chunk Activity

- View Chunk tasks in: **Organization Settings → Chunk Tasks**
- Check PRs created by Chunk in your repository
- Review CircleCI builds triggered by Chunk for validation
- Monitor fix success rates in Chunk task dashboard

---

**Remember**: The goal isn't to bash Jenkins—it's to show how modern CI/CD tools like TeamCity and CircleCI Chunk solve real problems that waste developer time and erode confidence in CI/CD pipelines. Each tool offers different strengths: TeamCity excels at detection and visibility, while CircleCI Chunk adds automated remediation.
