# CLAUDE.md - Claude AI Development Guide with Milestones

**Project:** FinTrack Pro - SMS-Powered Intelligent Financial Manager  
**Duration:** 34 Weeks (6-8 Months)  
**Start Date:** September 2, 2026  
**Launch Date:** April 30, 2027  

---

## 📊 PROJECT OVERVIEW

### Timeline at a Glance

```
PHASE 1: FOUNDATION (Weeks 1-4)
├─ Sept 2-30, 2026
├─ Effort: 160 hours
├─ Focus: Project setup, Firebase, architecture, CI/CD
└─ Use Claude for: Code generation, configuration setup

PHASE 2: SMS PARSER (Weeks 5-9)
├─ Oct 1 - Nov 5, 2026
├─ Effort: 230 hours
├─ Focus: SMS reading, 20+ bank patterns, 92%+ accuracy
└─ Use Claude for: Regex patterns, parser logic, unit tests

PHASE 3: APPROVAL WORKFLOW (Weeks 10-13)
├─ Nov 8 - Dec 3, 2026
├─ Effort: 120 hours
├─ Focus: Approval screen, form validation, database
└─ Use Claude for: Widget code, state management, tests

PHASE 4: BIOMETRIC & SECURITY (Weeks 14-17)
├─ Dec 4-31, 2026
├─ Effort: 155 hours
├─ Focus: Biometric auth, AES-256 encryption, audit logging
└─ Use Claude for: Security implementations, crypto patterns

PHASE 5: REPORTS & FEATURES (Weeks 18-22)
├─ Jan 1 - Feb 5, 2027
├─ Effort: 180 hours
├─ Focus: Reports, charts, bills, analytics, private transactions
└─ Use Claude for: Data queries, chart generation, analytics

PHASE 6: TESTING & LAUNCH (Weeks 23-34)
├─ Feb 6 - Apr 30, 2027
├─ Effort: 395 hours
├─ Focus: Integration testing, beta, security audit, launch
├─ Use Claude for: Test case generation, documentation
└─ 2 MILESTONES:
   - MILESTONE 1: MVP Ready (Week 26, Mar 1)
   - MILESTONE 2: LIVE (Week 34, Apr 30)
```

---

## 🎯 HOW TO USE CLAUDE BY PHASE

### PHASE 1: Foundation (Weeks 1-4)

**What You're Building:** Project infrastructure  
**Claude's Role:** Generate boilerplate, configuration, architecture  

**Claude for Week 1: Project Setup**
```
✅ Generate pubspec.yaml with all dependencies
✅ Generate Flutter project structure
✅ Generate Firebase configuration code
✅ Generate GitHub Actions workflows
```

**Sample Prompts:**

```
Prompt 1: "Generate a complete pubspec.yaml for FinTrack Pro with:
- Flutter 3.13+
- Firebase (Auth, Firestore, Cloud Functions)
- Riverpod for state management
- SQLite + Hive for storage
- Encryption (encrypt package) & biometric (local_auth)
- Testing (flutter_test, mockito, integration_test)

Include all versions pinned."

Prompt 2: "Generate the base Clean Architecture structure for FinTrack Pro:
- lib/presentation/ (pages, controllers, widgets, providers)
- lib/domain/ (entities, repositories, use cases)
- lib/data/ (datasources, models, repositories)
- lib/sms_parser/ (parser engine)
- lib/security/ (encryption, biometric)
- lib/sync/ (offline queue, sync manager)

Include base classes and interfaces for each layer."

Prompt 3: "Generate GitHub Actions workflows for Flutter CI/CD:
1. flutter-ci.yml (on every push: lint, analyze, test, coverage)
2. security-scan.yml (nightly: dependency check, vulnerabilities)
3. release-build.yml (on version tags: APK + AAB)
4. coverage-report.yml (on PRs: codecov integration)

Include branch protection rules for main branch."
```

**Claude for Weeks 2-4: Architecture & Testing Setup**
```
✅ Generate base classes (BaseUseCase, BaseRepository, BaseException)
✅ Generate Riverpod provider setup
✅ Generate first widget & unit tests
✅ Generate README, SETUP, CONTRIBUTING docs
```

---

### PHASE 2: SMS Parser Engine (Weeks 5-9)

**What You're Building:** Core parser that extracts transaction details  
**Claude's Role:** Generate regex patterns, parser logic, comprehensive tests  

**Claude for Week 5: Parser Engine**
```
✅ Generate SMS parser base classes
✅ Generate confidence scoring algorithm
✅ Generate 50+ test SMS samples
✅ Generate 30+ unit tests
```

**Sample Prompts:**

```
Prompt 1: "Generate the SMS parser engine for FinTrack Pro that:
- Takes SMS text as input
- Identifies bank using pattern matching
- Applies bank-specific regex patterns
- Extracts: amount, merchant, date, time, transaction type
- Calculates confidence score (0-100%)
- Returns ParsedTransaction object
- Handles edge cases (malformed SMS, special characters)

Target: 92%+ accuracy on test set
Use: Dart with standard regex (no external deps)

Include inline documentation."

Prompt 2: "Generate regex patterns + extractors for these 8 banks:
1. HDFC: 'Debit Card X1234 spent Rs 250 at Merchant on 01-Sep-2026'
2. ICICI: 'ICICI Debit Card debited Rs 1500 at MERCHANT'
3. Axis: 'Your Axis Bank Account has been debited by INR 500'
4-8: [Provide 5 more sample formats from your bank list]

For each:
- Provide regex pattern
- Show extraction logic
- Include 3 test cases
- Note edge cases"

Prompt 3: "Generate 100+ unit tests for SMS parser covering:
- Correct parsing per bank format (50+ tests)
- Confidence scoring accuracy (20+ tests)
- Duplicate detection (10+ tests)
- Malformed SMS handling (10+ tests)
- Edge cases (special chars, decimals, etc.) (10+ tests)

Use mockito. Target: 90%+ coverage of parser.dart"
```

**Claude for Weeks 6-9: Bank Patterns & Verification**
```
✅ Generate 20+ bank pattern implementations
✅ Generate learning system (correction tracking)
✅ Generate 200+ unit tests total
✅ Verify 92%+ accuracy on 500+ SMS samples
```

---

### PHASE 3: Approval Workflow (Weeks 10-13)

**What You're Building:** User-facing transaction approval screen  
**Claude's Role:** Widget code, form validation, state management  

**Claude for Week 10: Approval Screen UI**
```
✅ Generate ApprovalScreen widget
✅ Generate Riverpod providers
✅ Generate form validation logic
```

**Sample Prompts:**

```
Prompt 1: "Generate ApprovalScreen widget with these requirements:

DESIGN (Minimal Animations - CRITICAL):
- Page loads INSTANTLY (no fade-in)
- Button press: 100ms opacity only
- Error text: 200ms fade-in max
- NO page transitions, bounces, ripple, splash

FIELDS:
- Amount (large, editable, 36pt)
- Merchant (18pt, editable)
- Category (dropdown, pre-filled)
- Confidence (progress bar 0-100%)

BUTTONS:
- APPROVE (saves to DB, returns)
- REJECT (marks rejected, returns)

STATE: Use Riverpod StateNotifier
VALIDATION: Amount > 0, merchant required

Return complete widget code with all validation."

Prompt 2: "Generate Riverpod providers for approval workflow:
- transactionProvider (current transaction)
- categoryProvider (list of categories with icons)
- approvalControllerProvider (business logic)

Include:
- Success/failure states
- Loading states
- Error handling
- Clear separation of concerns"

Prompt 3: "Generate 50+ widget tests for ApprovalScreen testing:
- Screen appears instantly
- All fields display correctly
- Editing works (amount, merchant, category)
- Validation errors show without animation
- Approve button saves & returns
- Reject button marks & returns
- Error cases handled

Use WidgetTester, golden tests for layouts"
```

**Claude for Weeks 11-13: Database & Integration**
```
✅ Generate Transaction entity & model
✅ Generate TransactionRepository (CRUD)
✅ Generate SQLite schema & migrations
✅ Generate 20+ integration tests (E2E flows)
```

---

### PHASE 4: Biometric & Security (Weeks 14-17)

**What You're Building:** Authentication & encryption layer  
**Claude's Role:** Security implementations, crypto patterns, audit logging  

**Claude for Week 14: Biometric Service**
```
✅ Generate BiometricService class
✅ Generate fingerprint + face authentication
✅ Generate PIN fallback (6-digit)
✅ Generate failed attempt tracking & lockout
```

**Sample Prompts:**

```
Prompt 1: "Generate BiometricService for FinTrack Pro with:

AUTHENTICATION:
- Fingerprint support (iOS + Android)
- Face ID/Face Unlock (iOS + Android)
- PIN fallback (6-digit minimum)
- Failed attempt tracking (5 failures → 15 min lockout)
- Auto-lock on background (5 min inactivity)

REQUIREMENTS:
- Use local_auth package
- Thread-safe implementation
- Detect device capability at runtime
- Graceful degradation if biometric unavailable

Include:
- Clear error messages
- Success/failure callbacks
- Testable (mockable)
- Complete documentation"

Prompt 2: "Generate EncryptionService for AES-256:

FUNCTIONALITY:
- Encrypt strings with AES-256
- Decrypt strings with AES-256
- Generate secure encryption keys
- Store keys in Keychain (iOS) / Keystore (Android)

REQUIREMENTS:
- Never store keys in plaintext
- Use encrypt package
- Thread-safe
- No external key exposure
- Complete error handling

Include unit testable design"

Prompt 3: "Generate 50+ security tests for:
- BiometricService (30+ tests)
- EncryptionService (20+ tests)

Cover:
- Authentication success/failure
- Lockout after 5 failures
- Encryption/decryption correctness
- Key storage verification
- No sensitive data in logs
- All error cases

Target: 90%+ security layer coverage"
```

**Claude for Weeks 15-17: Audit Logging & Data Protection**
```
✅ Generate AuditService (access logging)
✅ Generate sensitive data encryption (card #, CVV, bank account)
✅ Generate masked display logic
✅ Generate OWASP compliance checks
```

---

### PHASE 5: Reports & Features (Weeks 18-22)

**What You're Building:** Reporting, analytics, bills, private transactions  
**Claude's Role:** Data queries, chart generation, analytics logic  

**Claude for Week 18: Dual Reports**
```
✅ Generate comprehensive report logic
✅ Generate categorized report logic
✅ Generate filtering (date, category, merchant)
```

**Sample Prompts:**

```
Prompt 1: "Generate report logic for FinTrack Pro:

COMPREHENSIVE REPORT:
- All transactions (including private if biometric)
- Filter by date range, category, merchant
- Sort options (date, amount, merchant)
- Summary statistics (total, average, count)

CATEGORIZED REPORT:
- Approved transactions only (exclude private)
- Same filtering options
- Category breakdown
- Top merchants list

REQUIREMENTS:
- Generate < 2 seconds
- SQLite query optimization
- Clear separation of reports
- Unit testable

Include repository pattern, use cases, entities"

Prompt 2: "Generate chart implementations using fl_chart:
- Pie chart (category breakdown)
- Bar chart (top merchants)
- Line chart (spending trends over time)

Each should:
- Render < 500ms
- Handle empty data
- Show legend
- Be responsive on mobile/tablet
- Include error states

Provide complete widget code"

Prompt 3: "Generate analytics logic for:
- Daily spending totals
- Weekly spending totals
- Monthly spending totals
- Spending trends (increasing/decreasing)
- Anomaly detection (unusual transactions)
- Month-end forecasting
- Budget recommendations

Include:
- Calculation logic (testable functions)
- Chart generation
- Performance optimization
- 20+ unit tests"
```

**Claude for Weeks 19-22: Bills, Reminders, Private Transactions**
```
✅ Generate Bill entity & repository
✅ Generate Cloud Function for reminders
✅ Generate notification system
✅ Generate private transaction filtering
✅ Generate 30+ feature tests
```

---

### PHASE 6: Testing & Launch (Weeks 23-34)

**What You're Building:** Production-ready app with testing, security audit, deployment  
**Claude's Role:** Test generation, documentation, deployment guides  

**Claude for Weeks 23-26: Integration Testing → MVP Ready**
```
✅ Generate 20+ integration tests (E2E flows)
✅ Generate device compatibility tests
✅ Generate performance tests
✅ Generate security test suite (50+ tests)
```

**Sample Prompts:**

```
Prompt 1: "Generate comprehensive integration tests for:

SMS → Parser → Approval → DB Flow:
- SMS received (mock SMS input)
- Parser extracts details
- Approval screen shows
- User approves
- Transaction saved to SQLite
- Appears in transaction list

Include:
- Setup/teardown
- Mock SMS data
- Database verification
- Error cases (parse failure, save failure)
- 5+ test scenarios"

Prompt 2: "Generate security test suite (50+ tests) for:

Biometric Security:
- Authentication success/failure (10 tests)
- Failed attempt lockout (10 tests)
- Auto-lock on background (5 tests)

Encryption:
- Encrypt/decrypt round-trip (10 tests)
- No plaintext storage (10 tests)
- Key security (5 tests)

Audit Logging:
- Access logged (5 tests)
- No sensitive data in logs (5 tests)

Use mockito, verify all security requirements"

Prompt 3: "Generate release documentation:
- README for deployment
- Privacy policy (data handling, security)
- Terms of service
- Release notes (feature list, bug fixes)
- User guide (PDF)
- FAQ document

Each should be production-ready, legal-compliant"
```

**Claude for Weeks 27-34: Beta Testing → Launch → Live**
```
✅ Generate beta testing plan
✅ Generate bug report templates
✅ Generate launch checklist
✅ Generate monitoring setup guide
✅ Generate support documentation
```

---

## 🎯 MILESTONE GUIDES

### 📍 MILESTONE 1: MVP Beta Ready (Week 26, March 1, 2027)

**Status After Week 26:**
```
✅ All SMS parser tests passing (92%+ accuracy verified)
✅ Approval workflow fast & smooth (< 500ms)
✅ Biometric security working (iOS + Android)
✅ All 80%+ test coverage maintained
✅ Real device testing complete (10+ devices)
✅ No critical bugs remaining
✅ Security audit scheduled
✅ 10 beta users recruited & ready
```

**Use Claude for MVP Preparation:**
```
✅ Generate "MVP Ready Checklist" verification script
✅ Generate beta testing plan document
✅ Generate beta user onboarding guide
✅ Generate feedback collection template
```

### 📍 MILESTONE 2: Full Launch (Week 34, April 30, 2027)

**Status After Week 34:**
```
✅ App approved by App Store
✅ App approved by Play Store
✅ App live on both stores
✅ 100+ downloads in Week 1
✅ Monitoring system active
✅ Support system active
✅ Documentation published
✅ Team celebrating 🎉
```

**Use Claude for Launch:**
```
✅ Generate launch announcement
✅ Generate marketing materials
✅ Generate monitoring setup
✅ Generate support runbook
```

---

## 💡 CLAUDE BEST PRACTICES BY PHASE

### Phase 1 (Foundation): Setup & Architecture
```
✅ Ask Claude for complete boilerplate
✅ Request configuration files (pubspec.yaml, GitHub Actions)
✅ Ask for architecture documentation
✅ Generate CI/CD workflows

⚠️ Review carefully - verify all dependencies
⚠️ Test setup before proceeding
⚠️ Adapt to your specific needs
```

### Phase 2 (Parser): Complex Logic & Patterns
```
✅ Ask Claude for regex patterns (provide sample SMS)
✅ Request parser logic with confidence scoring
✅ Ask for test case generation (100+ cases)
✅ Generate learning system logic

⚠️ Test patterns thoroughly on real SMS
⚠️ Verify 92%+ accuracy before moving on
⚠️ Review edge case handling
⚠️ Profile performance (< 1s per SMS)
```

### Phase 3 (Approval): UI & Forms
```
✅ Ask Claude for widget code (complete implementation)
✅ Request form validation logic
✅ Ask for state management setup
✅ Generate comprehensive widget tests

⚠️ Verify minimal animations constraint
⚠️ Test on multiple devices
⚠️ Verify < 500ms submission
⚠️ Review accessibility (WCAG 2.1 AA)
```

### Phase 4 (Security): Crypto & Auth
```
✅ Ask Claude for security service implementations
✅ Request encryption patterns
✅ Ask for audit logging
✅ Generate security tests (50+)

⚠️ Code review by security expert
⚠️ Never store plaintext secrets
⚠️ Test on real biometric devices
⚠️ Verify no sensitive data in logs
```

### Phase 5 (Features): Data & Reporting
```
✅ Ask Claude for data query optimization
✅ Request chart generation code
✅ Ask for analytics calculations
✅ Generate feature tests (30+)

⚠️ Verify performance (< 2s reports)
⚠️ Test on low-end devices
⚠️ Verify data accuracy
⚠️ Test edge cases (empty data, etc.)
```

### Phase 6 (Launch): Testing & Deployment
```
✅ Ask Claude for test generation
✅ Request deployment documentation
✅ Ask for monitoring setup guides
✅ Generate support documentation

⚠️ Test on real devices (10+)
⚠️ Run security audit (3rd party)
⚠️ Verify app store requirements
⚠️ Plan launch communication
```

---

## 📋 PHASE-BY-PHASE PROMPT TEMPLATES

### Template 1: Code Generation

```
Generate [COMPONENT] for [PROJECT] with:

REQUIREMENTS:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

IMPLEMENTATION:
- [Implementation detail 1]
- [Implementation detail 2]

TESTING:
- Unit test [X] cases
- Edge cases: [specific cases]

DOCUMENTATION:
- Include inline comments
- Document public API
- Provide usage example
```

### Template 2: Test Generation

```
Generate [NUMBER]+ tests for [COMPONENT]:

COVERAGE:
- [Test scenario 1]
- [Test scenario 2]
- [Test scenario 3]

EDGE CASES:
- [Edge case 1]
- [Edge case 2]

REQUIREMENTS:
- Framework: [flutter_test/mockito/integration_test]
- Target coverage: [X%]
- Use mocks for: [dependencies]

Include test setup, teardown, and data fixtures.
```

### Template 3: Documentation

```
Generate documentation for [COMPONENT]:

SECTIONS:
- Overview (what it does)
- Requirements (dependencies, versions)
- Installation (how to set up)
- Usage (code examples)
- API reference (all public methods)
- Troubleshooting (common issues)

FORMAT:
- Markdown with code blocks
- Include diagrams if helpful
- Real code examples (not pseudo-code)
```

---

## 🚀 CLAUDE USAGE WORKFLOW

### Daily Development Cycle

```
MORNING (30 min):
1. Open MILESTONE_PHASE#.md for current phase
2. See what's scheduled this week/day
3. Read SPEC.md for feature requirements
4. Plan tasks with Claude

DURING CODING (3-6 hours):
1. Write failing test (TDD)
2. Ask Claude to generate implementation
3. Review generated code
4. Run tests locally
5. Fix any issues
6. Commit to GitHub
7. GitHub Actions runs CI/CD

EVENING (30 min):
1. Review test coverage
2. Ask Claude for improvements/optimizations
3. Ask Claude for documentation
4. Update progress in MILESTONE_PHASE#.md
5. Plan next day

WEEKLY (Friday):
1. Review week's progress
2. Verify all deliverables
3. Check quality gates
4. Update MILESTONE_PHASE#.md
5. Prepare for next phase
```

### Types of Prompts to Use Claude For

```
✅ Code Generation (80% of prompts)
   - Complete implementations
   - Widget code
   - Business logic
   - Database operations
   - API integrations

✅ Test Generation (10% of prompts)
   - Unit test cases
   - Widget tests
   - Integration tests
   - Security tests
   - Performance tests

✅ Documentation (8% of prompts)
   - API documentation
   - User guides
   - Architecture docs
   - Troubleshooting guides

✅ Optimization (2% of prompts)
   - Performance improvements
   - Code cleanup
   - Refactoring suggestions
```

---

## ⚠️ WHEN NOT TO USE CLAUDE

```
❌ Don't ask Claude for:
   - Architecture decisions (already in SPEC.md)
   - Product decisions (already in SPEC.md)
   - Security recommendations (do security audit)
   - Sensitive data handling (follow compliance)
   - Exact app store procedures (follow official docs)

✅ Always verify:
   - Generated code compiles without errors
   - All tests pass
   - Code follows project style
   - No security issues
   - Performance meets requirements
```

---

## 📊 EFFORT & PROGRESS TRACKING

### Phase Completion Checklist

For each phase, verify:
```
□ All tasks marked DONE in MILESTONE_PHASE#.md
□ Actual hours tracked (vs estimated)
□ All deliverables checklist verified
□ Code review completed
□ Test coverage ≥ 80% (or phase target)
□ All quality gates passing
□ Documentation complete
□ Team sign-off obtained
□ Ready to proceed to next phase
```

### Using Claude for Progress Updates

```
Prompt: "Generate a progress report for [PHASE_NAME]:

What we completed this week:
- [Task 1]
- [Task 2]
- [Task 3]

What we planned vs actual:
- Estimated: [X] hours
- Actual: [Y] hours
- [Variance explanation]

Blockers/Issues:
- [Issue 1]
- [Issue 2]

Next week plan:
- [Plan 1]
- [Plan 2]

Format as markdown, include metrics"
```

---

## 🎯 SUCCESS METRICS

### By Phase

| Phase | Success Metrics |
|-------|-----------------|
| Phase 1 | ✅ CI/CD working, team ready |
| Phase 2 | ✅ 92%+ accuracy, 200+ tests |
| Phase 3 | ✅ E2E flow working, 80%+ coverage |
| Phase 4 | ✅ 0 security issues, 90%+ coverage |
| Phase 5 | ✅ All features working, 80%+ coverage |
| Phase 6 | ✅ Live on stores, 100+ users |

### Overall

```
✅ Code Quality: 80%+ test coverage
✅ Performance: < 2s startup, < 500ms pages
✅ Security: 0 critical vulnerabilities
✅ Delivery: On schedule (34 weeks)
✅ Quality: All 12 features working
✅ User: 4.0+ star rating
```

---

## 🔗 REFERENCE DOCUMENTS

### Related Files You Have

```
SPEC.md
├─ What to build (12 features, architecture, requirements)
├─ Read for: Feature requirements, architecture details
└─ When: Before implementing each feature

MILESTONE_PHASE#.md (x6)
├─ When to build what (week-by-week)
├─ Read for: Current week's tasks, deliverables, quality gates
└─ When: At start of each phase/week

MILESTONE.md
├─ Complete 34-week overview
├─ Read for: Full timeline view
└─ When: Planning, stakeholder communication

CLAUDE.md (this file)
├─ How to use Claude for each phase
├─ Read for: Prompt templates, best practices
└─ When: Before asking Claude for help
```

---

## ✅ GETTING STARTED

### Week 1 Setup

```
Step 1: Read SPEC.md (understand what to build)
Step 2: Read MILESTONE_PHASE1.md (understand Week 1 tasks)
Step 3: Read this CLAUDE.md (understand Claude usage)
Step 4: Use prompts from "PHASE 1: Foundation" section
Step 5: Follow Week 1 tasks in MILESTONE_PHASE1.md
Step 6: Update progress weekly
Step 7: Verify quality gates before Phase 2
```

### For Each Phase

```
1. Open MILESTONE_PHASE#.md
2. Review objectives & success criteria
3. Follow week-by-week breakdown
4. Use Claude prompts for code generation
5. Track actual hours & progress
6. Verify deliverables & quality gates
7. Move to next phase
```

### Using Claude Effectively

```
DO:
✅ Be specific in prompts (include requirements)
✅ Ask for complete implementations (not just ideas)
✅ Request comprehensive tests
✅ Ask for documentation
✅ Review generated code before using
✅ Test everything locally first

DON'T:
❌ Ask for too many variations at once
❌ Ignore generated code quality
❌ Skip testing Claude's output
❌ Use code without review
❌ Trust Claude 100% (verify everything)
```

---

## 🚀 NEXT STEPS

1. **Download all reference files:**
   - SPEC.md (what to build)
   - CLAUDE.md (this file - how to build with Claude)
   - MILESTONE_PHASE1.md (week 1 tasks)
   - MILESTONE.md (complete timeline)

2. **Start Week 1 (September 2, 2026):**
   - Use prompts from "PHASE 1: Foundation" section
   - Generate pubspec.yaml, project structure, Firebase config
   - Generate CI/CD workflows
   - Generate first tests

3. **Each Week:**
   - Update MILESTONE_PHASE#.md with progress
   - Use Claude prompts from appropriate phase
   - Track actual hours vs estimated
   - Verify quality gates

4. **Each Phase End:**
   - Verify all deliverables
   - Check test coverage ≥ 80%
   - Code review completed
   - Ready for next phase

---

## 📞 SUPPORT

**Questions about WHAT to build?**
→ Reference SPEC.md

**Questions about WHEN to build?**
→ Reference MILESTONE_PHASE#.md

**Questions about HOW to use Claude?**
→ Reference this CLAUDE.md file

**Questions about timeline?**
→ Reference MILESTONE.md

**Need to track progress?**
→ Update MILESTONE_PHASE#.md status column

---

## SIGN-OFF

```
CLAUDE.md - DEVELOPMENT GUIDE WITH MILESTONE SUMMARY

Project: FinTrack Pro
Duration: 34 weeks (6-8 months)
Start: September 2, 2026
Launch: April 30, 2027

Status: ✅ READY TO USE

Includes:
✅ Phase-by-phase Claude usage guide
✅ Prompt templates per phase
✅ Milestone summaries (MVP Week 26, Launch Week 34)
✅ Success metrics
✅ Best practices
✅ Effort tracking

This file + SPEC.md + MILESTONE_PHASE#.md = Complete development system
```

---

**Last Updated:** August 31, 2026  
**Next Review:** September 2, 2026 (Start of Phase 1)  

**🚀 Ready to build with Claude? Use the prompts above and reference MILESTONE_PHASE1.md for Week 1 tasks!**
