# FinTrack Pro - GitHub Actions CI/CD Pipeline
## Complete Automation for Quality & Deployment

**Status:** READY TO IMPLEMENT  
**Platform:** GitHub Actions  
**Triggers:** Push to main/develop, Pull Requests

---

## 1. CI/CD PIPELINE OVERVIEW

```
Code Push to GitHub
        ↓
    ┌───┴────────────────┐
    │                    │
    ↓                    ↓
  Lint            Run Tests
    │                    │
    └───┬─────────┬──────┘
        │         │
        ↓         ↓
   Analyze    Build APK
        │         │
        └─────┬───┘
              ↓
        Code Coverage
              ↓
        Build iOS
              ↓
        ✅ PASS / ❌ FAIL
              ↓
        Notify Slack (optional)
```

---

## 2. GITHUB ACTIONS WORKFLOWS

### 2.1 Main CI Workflow (Tests + Build)

**File:** `.github/workflows/flutter-ci.yml`

```yaml
name: Flutter CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        flutter-version: [ '3.13.0' ]
    
    steps:
      # Step 1: Checkout code
      - name: Checkout code
        uses: actions/checkout@v3
      
      # Step 2: Setup Flutter
      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: ${{ matrix.flutter-version }}
          channel: 'stable'
      
      # Step 3: Install dependencies
      - name: Install dependencies
        run: flutter pub get
      
      # Step 4: Format check
      - name: Check code formatting
        run: dart format --set-exit-if-changed lib test
        continue-on-error: true
      
      # Step 5: Analyze code
      - name: Analyze code
        run: flutter analyze
      
      # Step 6: Run unit tests
      - name: Run unit tests
        run: flutter test --coverage
        timeout-minutes: 15
      
      # Step 7: Upload coverage to Codecov
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          flags: unittests
          name: codecov-flutter
          fail_ci_if_error: false
      
      # Step 8: Check coverage threshold
      - name: Check coverage threshold
        run: |
          coverage=$(grep -o 'line-rate="[^"]*"' coverage/lcov.xml | grep -o '[0-9]*\.[0-9]*')
          echo "Code Coverage: ${coverage}%"
          if (( $(echo "$coverage < 80" | bc -l) )); then
            echo "Coverage below 80% threshold!"
            exit 1
          fi
        continue-on-error: true
      
      # Step 9: Build APK
      - name: Build APK
        run: flutter build apk --debug
        timeout-minutes: 20
      
      # Step 10: Build iOS (Mac runner only, optional)
      - name: Build iOS
        if: runner.os == 'macOS'
        run: flutter build ios --debug --no-codesign
        timeout-minutes: 30
      
      # Step 11: Upload APK artifact
      - name: Upload APK artifact
        uses: actions/upload-artifact@v3
        if: success()
        with:
          name: app-debug.apk
          path: build/app/outputs/flutter-apk/app-debug.apk
          retention-days: 5
      
      # Step 12: Notify on failure
      - name: Notify on failure
        if: failure()
        uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '❌ CI Pipeline failed. Please check the logs.'
            })

  # Job 2: Code Quality Analysis
  code-quality:
    runs-on: ubuntu-latest
    needs: build
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.13.0'
      
      - name: Install dependencies
        run: flutter pub get
      
      # Lint analysis
      - name: Run lint checks
        run: flutter analyze
      
      # Additional security checks (optional)
      - name: Run security checks
        run: |
          flutter pub outdated --exit-on-outdated
        continue-on-error: true

  # Job 3: Integration Tests (Optional, can run separately)
  integration-tests:
    runs-on: ubuntu-latest
    if: github.event_name == 'pull_request' || github.ref == 'refs/heads/develop'
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.13.0'
      
      - name: Install dependencies
        run: flutter pub get
      
      - name: Run integration tests
        run: flutter drive --driver=test_driver/integration_test.dart --target=integration_test/sms_to_approval_flow_test.dart
        timeout-minutes: 30
        continue-on-error: true
```

### 2.2 Nightly Security Scan Workflow

**File:** `.github/workflows/security-scan.yml`

```yaml
name: Security Scan

on:
  schedule:
    # Run every night at 2 AM UTC
    - cron: '0 2 * * *'
  workflow_dispatch: # Allow manual trigger

jobs:
  security-scan:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.13.0'
      
      - name: Install dependencies
        run: flutter pub get
      
      # Check for outdated dependencies
      - name: Check for outdated dependencies
        run: flutter pub outdated
      
      # Scan for known vulnerabilities
      - name: Scan for vulnerabilities
        run: dart pub global activate dependency_validator && dependency_validator
        continue-on-error: true
      
      # Check for leaked secrets
      - name: Detect secrets
        uses: TruffleHog/trufflehog@main
        with:
          path: ./
          base: ${{ github.event.repository.default_branch }}
          head: HEAD
          extra_args: --debug --only-verified
        continue-on-error: true
```

### 2.3 Release Build Workflow

**File:** `.github/workflows/release-build.yml`

```yaml
name: Release Build

on:
  push:
    tags:
      - 'v*.*.*'
  workflow_dispatch:

jobs:
  build-release:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.13.0'
      
      - name: Install dependencies
        run: flutter pub get
      
      # Build release APK
      - name: Build release APK
        run: flutter build apk --release
        timeout-minutes: 20
      
      # Build release app bundle (for Play Store)
      - name: Build App Bundle
        run: flutter build appbundle --release
        timeout-minutes: 20
      
      # Create GitHub Release
      - name: Create Release
        uses: softprops/action-gh-release@v1
        if: startsWith(github.ref, 'refs/tags/')
        with:
          files: |
            build/app/outputs/flutter-apk/app-release.apk
            build/app/outputs/bundle/release/app-release.aab
          body_path: RELEASE_NOTES.md
          draft: false
          prerelease: false
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Notify success
        uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '✅ Release build successful!\n\nAPK: build/app/outputs/flutter-apk/app-release.apk\nAAB: build/app/outputs/bundle/release/app-release.aab'
            })
```

---

## 3. PRE-COMMIT HOOKS

### 3.1 Git Hook Setup

**File:** `.git/hooks/pre-commit` (Auto-setup script)

```bash
#!/bin/bash

# Pre-commit hook for Flutter/Dart projects
# Install: Run `chmod +x .githooks/pre-commit && git config core.hooksPath .githooks`

echo "🔍 Running pre-commit checks..."

# Check if Flutter is installed
if ! command -v flutter &> /dev/null; then
    echo "❌ Flutter is not installed"
    exit 1
fi

# Format check
echo "📝 Checking code format..."
dart format --set-exit-if-changed lib test
if [ $? -ne 0 ]; then
    echo "❌ Code format check failed. Run: dart format lib test"
    exit 1
fi

# Analyze
echo "🔎 Running static analysis..."
flutter analyze
if [ $? -ne 0 ]; then
    echo "❌ Analysis found issues"
    exit 1
fi

# Run quick tests
echo "🧪 Running quick tests..."
flutter test --coverage
if [ $? -ne 0 ]; then
    echo "❌ Tests failed"
    exit 1
fi

echo "✅ Pre-commit checks passed!"
exit 0
```

**File:** `setup-hooks.sh` (Setup script)

```bash
#!/bin/bash

# Setup git hooks
mkdir -p .githooks
cp .githooks/pre-commit.example .githooks/pre-commit
chmod +x .githooks/pre-commit
git config core.hooksPath .githooks

echo "✅ Git hooks installed!"
echo "Run: git config core.hooksPath .githooks"
```

### 3.2 Pre-commit Configuration

**File:** `.pre-commit-config.yaml`

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-merge-conflict
      - id: detect-private-key

  - repo: https://github.com/google/dart-lang
    rev: v2.18.5
    hooks:
      - id: dart-format
        args: [ '--set-exit-if-changed' ]
      - id: dart-analyze
```

---

## 4. GITHUB BRANCH PROTECTION RULES

### Configure in GitHub Settings:

```
Repository Settings → Branches → Branch Protection Rules

Rule for: main
├─ ✅ Require pull request reviews before merging
│  └─ Required number of reviewers: 1
├─ ✅ Require status checks to pass before merging
│  ├─ flutter-ci / build
│  ├─ flutter-ci / code-quality
│  └─ codecov/project (for coverage)
├─ ✅ Require code to be up to date before merging
├─ ✅ Require branches to be up to date before merging
└─ ✅ Enforce all configured restrictions for administrators

Rule for: develop
├─ ✅ Require pull request reviews before merging
│  └─ Required number of reviewers: 1
├─ ✅ Require status checks to pass before merging
│  └─ flutter-ci / build
└─ ✅ Require branches to be up to date before merging
```

---

## 5. CODE COVERAGE CONFIGURATION

### 5.1 Coverage Report Generation

**File:** `coverage/.gitignore`

```
# Coverage files
*.html
*.json
lcov.info
```

**File:** `.github/workflows/coverage-report.yml`

```yaml
name: Coverage Report

on:
  pull_request:
    branches: [ main, develop ]

jobs:
  coverage:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.13.0'
      
      - name: Install dependencies
        run: flutter pub get
      
      - name: Generate coverage
        run: flutter test --coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          flags: unittests
          name: codecov-flutter
          verbose: true
      
      - name: Comment PR with coverage
        uses: romeovs/lcov-reporter-action@v0.3.1
        if: always()
        with:
          lcov-file: ./coverage/lcov.info
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

---

## 6. DEPENDENCY UPDATES

### Automated Dependency Updates

**File:** `.github/dependabot.yml`

```yaml
version: 2
updates:
  - package-ecosystem: "pub"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
      time: "03:00"
    open-pull-requests-limit: 5
    reviewers:
      - "your-github-username"
    labels:
      - "dependencies"
    commit-message:
      prefix: "chore(deps):"
      prefix-major: "chore(deps)!"
      prefix-minor: "chore(deps):"
      prefix-patch: "chore(deps):"
    allow:
      - dependency-type: "direct"
        dependency-type: "indirect"
```

---

## 7. COMMIT MESSAGE CONVENTIONS

### Conventional Commits Format

```
<type>(<scope>): <subject>

<body>

<footer>

TYPES:
- feat: A new feature
- fix: A bug fix
- docs: Documentation only changes
- style: Changes that don't affect code meaning (formatting, etc)
- refactor: Code change that neither fixes a bug nor adds a feature
- perf: Code change that improves performance
- test: Adding missing tests or correcting existing tests
- chore: Changes to build process, dependencies, or tooling

EXAMPLES:
feat(sms-parser): add HDFC bank pattern matching
  - Add regex pattern for HDFC SMS format
  - 92%+ confidence scoring
  - Handles multiple amount formats

fix(approval): prevent duplicate transaction approval
  - Add duplicate detection check
  - Fixes #123

refactor(auth): simplify login logic
  - Extract validation to separate method
  - Improve error handling

test(transaction): add unit tests for validation
  - Add 10 new test cases
  - 100% coverage for validators
```

---

## 8. PULL REQUEST TEMPLATE

**File:** `.github/pull_request_template.md`

```markdown
## Description
<!-- Describe your changes in detail -->

## Related Issues
<!-- Link to related issues -->
Fixes #(issue number)
Related to #(issue number)

## Type of Change
<!-- Mark with an X -->
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
<!-- Describe how you tested your changes -->
- [ ] Unit tests added
- [ ] Integration tests added
- [ ] Manual testing completed
- [ ] No new tests needed

## Test Coverage
<!-- What's the coverage for new code? -->
- Unit test coverage: ___%
- Integration test coverage: ___%

## Checklist
- [ ] Code follows style guidelines
- [ ] Tests passing locally (`flutter test`)
- [ ] Code coverage >= 80%
- [ ] Self-reviewed code
- [ ] Comments added for complex logic
- [ ] Documentation updated
- [ ] No breaking changes (or documented)
- [ ] Related issues linked

## Screenshots (if applicable)
<!-- Add screenshots for UI changes -->

## Performance Impact
<!-- Any performance implications? -->

## Additional Context
<!-- Any other context? -->
```

---

## 9. GITHUB ACTIONS SECRETS

### Setup in GitHub Settings:

```
Settings → Secrets and Variables → Actions

Required Secrets (if using external services):
├─ CODECOV_TOKEN
├─ SLACK_WEBHOOK (for notifications)
├─ FIREBASE_API_KEY
└─ PLAYSTORE_API_KEY (for releases)
```

---

## 10. WORKFLOW MONITORING

### GitHub Status Checks:

```
Pull Request Checks:
✅ flutter-ci / build
✅ flutter-ci / code-quality
✅ codecov/project/patch
✅ codecov/project/project
```

### View Workflow Runs:

- GitHub Actions tab → All Workflows
- Click workflow name to view runs
- Click run to view step logs

---

## 11. LOCAL DEVELOPMENT WITH CI

### Before Every Commit:

```bash
# 1. Format code
dart format lib test

# 2. Analyze
flutter analyze

# 3. Run tests
flutter test --coverage

# 4. Check coverage
# Review coverage/lcov.info

# 5. Commit with conventional message
git commit -m "feat(feature-name): description"
```

### Pre-commit Hook Setup:

```bash
# Make hook executable
chmod +x .githooks/pre-commit

# Configure git to use hooks
git config core.hooksPath .githooks

# Now hooks run automatically before each commit
```

---

## 12. CI/CD STATUS BADGE

### Add to README.md:

```markdown
# FinTrack Pro

[![Flutter CI](https://github.com/your-username/fintrack-pro/actions/workflows/flutter-ci.yml/badge.svg)](https://github.com/your-username/fintrack-pro/actions)
[![codecov](https://codecov.io/gh/your-username/fintrack-pro/branch/main/graph/badge.svg)](https://codecov.io/gh/your-username/fintrack-pro)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
```

---

## 13. TROUBLESHOOTING CI FAILURES

### Common Issues & Solutions:

```
❌ "flutter pub get" fails
→ Check pubspec.lock is in git
→ Clear pub cache: flutter pub cache clean

❌ "flutter analyze" fails
→ Review analyzer warnings: flutter analyze --pub
→ Fix deprecation warnings

❌ "flutter test" fails
→ Run locally: flutter test
→ Check test/pubspec.yaml dependencies

❌ Coverage below threshold
→ Write more tests (target: 80%+)
→ Check coverage/lcov.info

❌ APK build fails
→ Check build.gradle configuration
→ Verify signing setup for release builds

❌ Artifact upload fails
→ Check file path exists
→ Verify artifact naming
```

---

## 14. DEPLOYMENT WORKFLOW (From CI to Stores)

### Release Process:

```
1. Push code to develop
   ↓
2. Create PR to main
   ↓
3. All CI checks pass
   ↓
4. Code review approved
   ↓
5. Merge to main
   ↓
6. Create release tag: v1.0.0
   ↓
7. GitHub Actions runs release-build.yml
   ↓
8. Build APK + AAB (Android) + IPA (iOS)
   ↓
9. Upload artifacts to GitHub Release
   ↓
10. Manual upload to:
    ├─ Google Play Console (Android)
    └─ App Store Connect (iOS)
```

---

## 15. MONITORING & ALERTS

### Setup Notifications:

**Email Alerts:**
- GitHub Actions → Email on workflow failures

**Slack Integration (Optional):**

```yaml
- name: Notify Slack
  if: failure()
  uses: slackapi/slack-github-action@v1.24.0
  with:
    webhook-url: ${{ secrets.SLACK_WEBHOOK }}
    payload: |
      {
        "text": "CI Pipeline failed",
        "blocks": [
          {
            "type": "section",
            "text": {
              "type": "mrkdwn",
              "text": "❌ CI Pipeline failed\n*Repository:* ${{ github.repository }}\n*Branch:* ${{ github.ref }}\n*Commit:* ${{ github.sha }}"
            }
          }
        ]
      }
```

---

## SUMMARY: YOUR CI/CD PIPELINE

✅ **Automated Testing**
- Unit tests on every push/PR
- Code coverage reporting
- Lint and analysis checks

✅ **Automated Building**
- APK builds on every commit
- Release builds on tags
- Artifact storage (5-day retention)

✅ **Code Quality**
- Codecov integration
- Coverage threshold enforcement
- Security scanning nightly

✅ **Pull Request Workflow**
- Branch protection rules
- Required reviews
- Status checks required

✅ **Release Management**
- Automated release builds
- GitHub release creation
- Version tagging

✅ **Developer Experience**
- Pre-commit hooks
- Conventional commits
- Clear error messages

---

## Next Steps:

1. Copy all `.github/workflows/*.yml` files
2. Copy `.githooks/` directory
3. Update GitHub branch protection rules
4. Setup Codecov (free tier)
5. Configure secrets (if needed)
6. Commit and push

**Your CI/CD pipeline is now ready!** 🚀

