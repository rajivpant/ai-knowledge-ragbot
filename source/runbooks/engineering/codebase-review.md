# Enterprise-Grade Codebase Review Runbook

> **Version**: 2.1
> **Purpose**: Comprehensive, practical codebase audit for projects of any size
> **Usage**: Use with Claude Code, Cursor, or any AI-assisted development environment
> **License**: Open source under MIT License
> **Repository**: Part of the [Ragbot.AI](https://ragbot.ai) project
> **Methodology**: Based on [Synthesis Coding](https://synthesiscoding.org) principles
> **Guide & Prompts**: [Code Review That Scales](https://synthesiscoding.org/articles/code-review-that-scales/) — introduction, usage guide, and example prompts for agentic workflows

---

## HOW TO USE THIS RUNBOOK

This runbook is designed to be **both comprehensive AND practical**. Not every project needs every check. 

### Step 1: Assess Your Project Tier

Start by completing the **Project Complexity Assessment** below. This determines which tier your project falls into and which sections apply.

### Step 2: Review Applicable Sections

Each section and many individual items are marked with tier indicators:
- 🟢 **Essential** (Tier 1) - Apply to ALL projects, even weekend hacks
- 🔵 **Standard** (Tier 2) - Apply to team projects and production apps  
- 🟣 **Enterprise** (Tier 3) - Apply to large-scale, multi-team, or regulated systems
- ⚫ **Mission-Critical** (Tier 4) - Apply to financial, healthcare, infrastructure, or high-stakes systems

### Step 3: Skip What Doesn't Apply

- If you're Tier 1, focus only on 🟢 items (~50 checks)
- If you're Tier 2, include 🟢 and 🔵 items (~150 checks)
- If you're Tier 3, include 🟢, 🔵, and 🟣 items (~400 checks)
- If you're Tier 4, include everything (~900+ checks)

### Step 4: Use the Quick Start (Optional)

If you want a rapid assessment, use the **Minimum Viable Review** section for a 15-minute health check.

---

## PRE-FLIGHT CHECKLIST

> Complete these checks before starting any review. Skipping pre-flight has caused real wasted effort on real engagements.

### Branch Selection 🟢

- [ ] 🟢 **Correct Branch Identified**: Confirm which branch represents the current working state — do NOT assume `main` is current
- [ ] 🟢 **Branch Freshness Verified**: Check the most recent commit date on the target branch. If `main` hasn't been updated in weeks and there's an active branch with many commits ahead, you're likely reviewing a stale snapshot
- [ ] 🔵 **Git Flow Awareness**: Ask whether the team uses git-flow, trunk-based, or another model. In git-flow, `develop` is often the correct review target, not `main`

### Review Scope 🟢

- [ ] 🟢 **Excluded Paths Identified**: Confirm which directories to exclude (vendor/, node_modules/, generated/, etc.)
- [ ] 🔵 **Prior Review Exists?**: Ask if a previous review has been conducted. If yes, obtain the prior findings to enable delta review mode (see Output Format section)
- [ ] 🔵 **Deliverable Format Confirmed**: Confirm the expected output format (markdown, PDF, etc.) and audience (engineering team, leadership, both)

---

## PROJECT COMPLEXITY ASSESSMENT

> Complete this assessment first to determine your project tier.

### Project Characteristics

Score each characteristic (0 = No, 1 = Yes):

**Scale & Users**
| Question | Score |
|----------|-------|
| Does the project have >1 developer? | |
| Does the project have >5 developers? | |
| Does the project have >20 developers? | |
| Will there be >100 users? | |
| Will there be >10,000 users? | |
| Will there be >1,000,000 users? | |

**Business Criticality**
| Question | Score |
|----------|-------|
| Is this a production system (not a prototype/experiment)? | |
| Would downtime cost money directly? | |
| Would downtime cost >$10,000/hour? | |
| Would a security breach make the news? | |
| Are there contractual SLAs? | |

**Data Sensitivity**
| Question | Score |
|----------|-------|
| Does the system handle user accounts? | |
| Does the system handle PII (names, emails, addresses)? | |
| Does the system handle financial data (payments, banking)? | |
| Does the system handle health data (PHI/HIPAA)? | |
| Does the system handle data subject to regulations (GDPR, SOX, etc.)? | |

**Architecture Complexity**
| Question | Score |
|----------|-------|
| Is there more than one deployable service? | |
| Are there more than 5 services? | |
| Is there a database? | |
| Are there multiple databases or data stores? | |
| Are there third-party integrations? | |
| Are there more than 5 third-party integrations? | |

**Operational Requirements**
| Question | Score |
|----------|-------|
| Is 99% uptime required? | |
| Is 99.9% uptime required? | |
| Is 99.99% uptime required? | |
| Is there a dedicated ops/SRE team? | |
| Is there 24/7 on-call? | |

### Calculate Your Tier

**Total Score: ____**

| Score Range | Tier | Description |
|-------------|------|-------------|
| 0-4 | **Tier 1 - Essential** 🟢 | Solo/hobby projects, prototypes, internal tools |
| 5-10 | **Tier 2 - Standard** 🔵 | Small team projects, production apps, startups |
| 11-18 | **Tier 3 - Enterprise** 🟣 | Large teams, regulated industries, enterprise customers |
| 19+ | **Tier 4 - Mission-Critical** ⚫ | Financial systems, healthcare, critical infrastructure |

**Your Tier: ____**

---

## MINIMUM VIABLE REVIEW (15-Minute Quick Check)

> Use this for a rapid health assessment. These are the absolute essentials that apply to ANY project.

### 🟢 Security Essentials (5 minutes)
- [ ] **No secrets in code**: Run `git log -p | grep -i "password\|secret\|api_key\|token"` - should return nothing
- [ ] **Dependencies not ancient**: Check for critical vulnerabilities (`npm audit`, `pip-audit`, etc.)
- [ ] **HTTPS only**: All external communication uses TLS
- [ ] **Input validated**: User input is validated before use
- [ ] **Auth exists**: If there are users, authentication is implemented properly

### 🟢 Code Health (5 minutes)
- [ ] **It builds**: Clean build with no errors
- [ ] **Tests exist**: There are some automated tests
- [ ] **Tests pass**: All tests pass
- [ ] **No obvious duplication**: No copy-pasted files or massive repeated blocks
- [ ] **Readable**: A new developer could understand the main flow

### 🟢 Operations Essentials (5 minutes)
- [ ] **README exists**: There's documentation on how to run it
- [ ] **Can be deployed**: There's a documented or automated deployment process
- [ ] **Logs exist**: The application produces logs
- [ ] **Errors tracked**: Errors are logged or sent somewhere visible
- [ ] **Config externalized**: No hardcoded environment-specific values

**Quick Score: ____ / 15**

If you score <12, address the gaps before proceeding. If you score 12+, continue to the full review based on your tier.

---

## CUSTOMIZATION SECTION

> Customize these settings before running the full review.

### Organization Context

```yaml
organization_name: "[Your Organization]"
project_tier: "[1-Essential | 2-Standard | 3-Enterprise | 4-Mission-Critical]"
industry: "[e.g., Financial Services, Healthcare, Media, E-commerce, SaaS, Open Source]"
codebase_name: "[Project/Product Name]"
primary_language: "[e.g., Python, TypeScript, Go, Java]"
framework: "[e.g., Django, Next.js, Spring Boot]"
deployment_target: "[e.g., AWS, GCP, Azure, On-premise, Hybrid]"
is_open_source: "[yes | no]"
```

### Compliance Requirements (Tier 3-4)

> Check all that apply to your organization:

- [ ] SOC 2 Type II
- [ ] GDPR
- [ ] CCPA/CPRA
- [ ] HIPAA
- [ ] PCI-DSS
- [ ] FedRAMP
- [ ] ISO 27001
- [ ] NIST Cybersecurity Framework
- [ ] WCAG 2.1 AA (Accessibility)
- [ ] Industry-specific: ________________

### Review Scope

```yaml
review_type: "[full | security-focused | scalability-focused | pre-launch]"
priority_areas: "[comma-separated list of focus areas]"
excluded_paths: "[paths to exclude from review, e.g., vendor/, generated/]"
```

---

## MAIN REVIEW PROMPT

You are conducting a codebase review using the Enterprise-Grade Codebase Review Runbook. 

**Project Tier: [INSERT TIER]**

Review ONLY the sections marked for this tier or lower:
- Tier 1 (🟢): Review only 🟢 items
- Tier 2 (🔵): Review 🟢 and 🔵 items
- Tier 3 (🟣): Review 🟢, 🔵, and 🟣 items
- Tier 4 (⚫): Review all items

For each finding, document:
1. Specific file paths and line numbers
2. Severity (Critical/High/Medium/Low)
3. Concrete remediation steps

---

## 1. ARCHITECTURE & SYSTEM DESIGN

### 1.1 Architectural Foundation

- [ ] 🟢 **Code Organization**: Is there a logical folder/module structure?
- [ ] 🟢 **Separation of Concerns**: Is business logic separated from I/O and presentation?
- [ ] 🔵 **Pattern Identification**: What architectural pattern is implemented (MVC, microservices, modular monolith, etc.)?
- [ ] 🔵 **Pattern Appropriateness**: Is the chosen architecture suitable for the scale and team size?
- [ ] 🟣 **Architecture Documentation**: Is there an Architecture Decision Record (ADR)?
- [ ] 🟣 **Dependency Graph**: Are there circular dependencies between modules/services?
- [ ] 🟣 **Domain Boundaries**: Are bounded contexts clearly defined?
- [ ] ⚫ **Layer Separation**: Is there strict separation between layers with enforced boundaries?

### 1.2 API Design & Contracts

- [ ] 🟢 **Consistent Endpoints**: Are API endpoints named consistently?
- [ ] 🔵 **API Documentation**: Is there basic API documentation (README, comments, or OpenAPI)?
- [ ] 🔵 **Error Format Consistency**: Are error responses formatted consistently?
- [ ] 🟣 **API Versioning**: Are APIs versioned?
- [ ] 🟣 **Contract Documentation**: Is there OpenAPI/Swagger or GraphQL schema?
- [ ] 🟣 **Deprecation Strategy**: Is there a documented process for deprecating APIs?
- [ ] ⚫ **API Gateway**: Is there proper gateway implementation with routing, auth, rate limiting?
- [ ] ⚫ **Contract Testing**: Are API contracts tested for backward compatibility?

### 1.3 Service Communication (Tier 3+)

- [ ] 🟣 **Sync vs Async**: Are synchronous and asynchronous patterns used appropriately?
- [ ] 🟣 **Timeout Configuration**: Are timeouts configured for all network calls?
- [ ] 🟣 **Resilience Patterns**: Are circuit breakers and retries implemented?
- [ ] ⚫ **Message Contracts**: For event-driven systems, are message schemas versioned?
- [ ] ⚫ **Service Discovery**: How do services find each other? Is it robust?
- [ ] ⚫ **Data Consistency**: How is eventual consistency handled across services?

### 1.4 Data Architecture

- [ ] 🟢 **Data Store Exists**: Is there a proper database (not just files)?
- [ ] 🔵 **Schema Design**: Is the database schema reasonably normalized?
- [ ] 🔵 **Indexes Present**: Are there indexes on frequently queried columns?
- [ ] 🟣 **Data Store Selection**: Are the right databases used for the right purposes?
- [ ] 🟣 **Caching Strategy**: Is there a caching strategy?
- [ ] ⚫ **Data Flow Documentation**: Is data flow through the system documented?

---

## 2. SECRETS, CREDENTIALS & SENSITIVE DATA

> **🔴 This section is CRITICAL for all tiers.** Secrets in code are one of the most common and dangerous vulnerabilities.

### 2.1 Active Secret Scanning 🟢

- [ ] 🟢 **No Hardcoded Secrets**: Search for passwords, API keys, tokens in code
- [ ] 🟢 **Environment Files Not Committed**: `.env` files are in `.gitignore`
- [ ] 🔵 **Secret Scanner Run**: Run tools like `truffleHog`, `gitleaks`, or `detect-secrets`
- [ ] 🟣 **Git History Clean**: No secrets in git history (even if removed from current code)
- [ ] 🟣 **CI/CD Configs Clean**: No secrets in workflow files, Dockerfiles, or IaC

### 2.2 Secret Types to Search For 🟢

- [ ] 🟢 Passwords and passphrases
- [ ] 🟢 API keys (AWS, GCP, Stripe, etc.)
- [ ] 🟢 Database connection strings with credentials
- [ ] 🔵 Private keys (RSA, SSH, PGP)
- [ ] 🔵 OAuth client secrets
- [ ] 🔵 JWT signing secrets
- [ ] 🟣 Encryption keys and salts
- [ ] 🟣 Webhook secrets
- [ ] 🟣 Service account credentials

### 2.2a AI Tool Configuration Files 🟢

> AI coding tool context files often contain rich infrastructure details to help the AI work effectively. That same rich context makes them an attacker's reconnaissance dossier if committed to git.

- [ ] 🟢 **AI Tool Files Checked**: Search for `.cursorrules`, `.cursor/`, `.aider*`, `.github/copilot-instructions.md`, and similar AI tool configuration files
- [ ] 🟢 **No Infrastructure in AI Config**: AI tool files do not contain server IPs, instance IDs, security group IDs, SSH key paths, deployment directories, or environment-specific paths
- [ ] 🔵 **AI Config in .gitignore**: AI tool configuration files are listed in `.gitignore` (or their sensitive content has been removed)

### 2.2b Comment-Aware Credential Scanning 🔵

> Developers treat comments as "not real code" and relax their caution about what goes there. Git tracks comments the same as executable code.

- [ ] 🔵 **Comments Scanned for Secrets**: Code comments, TODOs, and commented-out blocks are included in credential scanning — not just executable code
- [ ] 🔵 **No Secrets in Commented-Out Code**: Commented-out sections (e.g., old deploy configs, migration plans) do not contain real API keys, passwords, or infrastructure details
- [ ] 🟣 **CI/CD Comments Clean**: Workflow file comments do not contain credentials (even "example" values that are actually real)

### 2.3 Secret Management 🔵

- [ ] 🔵 **Environment Variables**: Secrets loaded from environment variables
- [ ] 🔵 **Not in Logs**: Secrets are not logged
- [ ] 🟣 **Secrets Manager**: Using Vault, AWS Secrets Manager, or equivalent
- [ ] 🟣 **Secret Rotation**: Secrets can be rotated without code changes
- [ ] ⚫ **Secret Access Audit**: Access to secrets is logged
- [ ] ⚫ **Least Privilege**: Services only access secrets they need

### 2.4 Preventive Controls 🔵

> Fixing existing secrets is necessary but insufficient. Without prevention, every new feature is an opportunity for a developer to commit a credential. Check for prevention mechanisms, not just absence of current secrets.

- [ ] 🔵 **`.gitignore` Coverage**: Sensitive file patterns in `.gitignore`
- [ ] 🔵 **Pre-Commit Secret Scanning**: Pre-commit hooks using tools like `gitleaks`, `truffleHog`, or `detect-secrets` to block secret commits before they enter git history
- [ ] 🟣 **CI/CD Scanning**: Secret scanning in the pipeline (GitHub secret scanning, or tools like gitleaks in CI)
- [ ] 🟣 **GitHub Secret Scanning Enabled**: If using GitHub, built-in secret scanning is enabled for the repository
- [ ] ⚫ **PR Checks**: Automated PR checks for secrets

---

## 3. CODE DUPLICATION & REUSABILITY

### 3.1 Duplication Analysis 🔵

- [ ] 🔵 **No Copied Files**: No nearly-identical files
- [ ] 🔵 **No Large Repeated Blocks**: No blocks of 20+ lines repeated
- [ ] 🟣 **Duplication Scanner Run**: Run jscpd, PMD CPD, or SonarQube
- [ ] 🟣 **Duplication Under 5%**: Total duplication is under 5% of codebase
- [ ] ⚫ **Cross-Module Duplication**: No significant duplication across services

### 3.2 Shared Code & Libraries 🔵

- [ ] 🔵 **Utility Functions Centralized**: Common utilities in one place
- [ ] 🔵 **No Copy-Paste Coding**: Similar problems solved the same way
- [ ] 🟣 **Internal Libraries**: Shared code in proper internal packages
- [ ] 🟣 **Library Versioning**: Internal packages are versioned
- [ ] ⚫ **Library Documentation**: Shared libraries are documented

### 3.3 Abstraction Quality 🟣

- [ ] 🟣 **Appropriate Abstraction**: Not over-abstracted or under-abstracted
- [ ] 🟣 **DRY Applied Sensibly**: DRY used where it reduces complexity
- [ ] 🟣 **Rule of Three**: Abstraction after 3+ occurrences
- [ ] ⚫ **Cross-Cutting Concerns**: Logging, auth, validation handled consistently

---

## 4. CODE QUALITY, EFFICIENCY & OPTIMIZATION

### 4.1 Basic Code Quality 🟢

- [ ] 🟢 **No Obvious Bugs**: No clearly broken code paths
- [ ] 🟢 **No Dead Code**: No large blocks of commented-out or unreachable code
- [ ] 🟢 **Reasonable Function Size**: Functions generally under 50 lines
- [ ] 🔵 **Consistent Style**: Code style is consistent throughout
- [ ] 🔵 **Linting Passes**: Linting configured and passing

### 4.2 Algorithmic Efficiency 🔵

- [ ] 🔵 **No O(n²) in Hot Paths**: No nested loops over large collections
- [ ] 🔵 **Appropriate Data Structures**: Using maps/sets instead of array searches
- [ ] 🟣 **Hot Path Optimization**: Performance-critical paths identified and optimized
- [ ] ⚫ **Complexity Documented**: Complex algorithms have documented complexity

### 4.3 Database Efficiency 🔵

- [ ] 🔵 **No N+1 Queries**: No loops that execute queries
- [ ] 🔵 **Pagination**: Large datasets are paginated
- [ ] 🟣 **Indexes Appropriate**: Queries use indexes effectively
- [ ] 🟣 **Connection Pooling**: Database connections are pooled
- [ ] ⚫ **Query Analysis**: Slow queries identified and optimized

### 4.4 Memory & Resource Efficiency 🟣

- [ ] 🟣 **No Memory Leaks**: Event listeners removed, no circular references
- [ ] 🟣 **Streaming for Large Data**: Large files/datasets streamed
- [ ] 🟣 **Resources Released**: Connections and handles properly closed
- [ ] ⚫ **Resource Limits**: Timeouts and limits configured
- [ ] ⚫ **Graceful Shutdown**: Resources released on shutdown

### 4.5 Concurrency & Thread Safety 🟣

- [ ] 🟣 **Race Conditions Addressed**: Shared mutable state is synchronized
- [ ] 🟣 **Async Patterns Correct**: async/await used correctly
- [ ] ⚫ **Deadlock Prevention**: Lock ordering is consistent
- [ ] ⚫ **Atomic Operations**: Used where needed for counters, flags

---

## 5. CLEAN CODE & SOFTWARE ENGINEERING PRINCIPLES

### 5.1 Naming & Readability 🟢

- [ ] 🟢 **Meaningful Names**: Variables and functions have descriptive names
- [ ] 🟢 **No Magic Numbers**: Named constants instead of unexplained literals
- [ ] 🔵 **Consistent Naming**: Naming conventions applied consistently
- [ ] 🔵 **Self-Documenting**: Code intent is clear from reading it

### 5.2 Function Design 🔵

- [ ] 🔵 **Single Purpose**: Functions do one thing
- [ ] 🔵 **Few Arguments**: Functions have ≤4 arguments typically
- [ ] 🔵 **No Side Effects**: Side effects are explicit and minimized
- [ ] 🟣 **Command-Query Separation**: Functions either do or return, not both

### 5.3 SOLID Principles 🟣

- [ ] 🟣 **Single Responsibility**: Classes have one reason to change
- [ ] 🟣 **Open/Closed**: Open for extension, closed for modification
- [ ] 🟣 **Liskov Substitution**: Derived classes substitute for base
- [ ] 🟣 **Interface Segregation**: Interfaces are focused
- [ ] 🟣 **Dependency Inversion**: Depend on abstractions

### 5.4 Error Handling 🟢

- [ ] 🟢 **Errors Not Swallowed**: Errors are logged or propagated
- [ ] 🟢 **User-Friendly Messages**: End users see helpful messages
- [ ] 🔵 **Specific Exceptions**: Using specific error types, not generic
- [ ] 🔵 **Error Context**: Errors include context for debugging
- [ ] 🟣 **Exception Hierarchy**: Clear exception/error type hierarchy
- [ ] 🟣 **Recovery Where Possible**: Graceful recovery when appropriate
- [ ] ⚫ **Circuit Breakers**: For external dependencies

### 5.5 Defensive Programming 🔵

- [ ] 🔵 **Input Validation**: User input validated at boundaries
- [ ] 🔵 **Null Safety**: Null/undefined handled safely
- [ ] 🟣 **Bounds Checking**: Array/collection bounds checked
- [ ] 🟣 **Type Safety**: Type system used effectively
- [ ] ⚫ **Assertions**: Invariants checked with assertions

---

## 6. CODE READABILITY & AI/HUMAN MAINTAINABILITY

> Code should be easily understood by both human engineers AND AI assistants.

### 6.1 Human Readability 🟢

- [ ] 🟢 **Scannable**: Code structure is clear at a glance
- [ ] 🟢 **Reasonable File Length**: Files generally under 500 lines
- [ ] 🔵 **Low Nesting**: Nesting depth ≤3-4 levels
- [ ] 🔵 **Early Returns**: Guard clauses reduce nesting
- [ ] 🟣 **Cyclomatic Complexity**: Under 10 per function

### 6.2 Documentation 🟢

- [ ] 🟢 **README Exists**: Project has a README with setup instructions
- [ ] 🔵 **Complex Logic Explained**: Non-obvious code has comments
- [ ] 🔵 **Why Comments**: Comments explain WHY, not WHAT
- [ ] 🟣 **API Documentation**: Public interfaces documented
- [ ] 🟣 **Architecture Documented**: System design is documented
- [ ] ⚫ **Runbooks Exist**: Operational procedures documented

### 6.3 AI & Automation Friendliness 🔵

> These characteristics help AI coding assistants understand and modify code effectively.

- [ ] 🔵 **Clear Intent**: Purpose of each module/function is obvious
- [ ] 🔵 **Modular Design**: Discrete, understandable modules
- [ ] 🔵 **Consistent Patterns**: Similar problems solved the same way
- [ ] 🟣 **Explicit Over Implicit**: Behaviors don't rely on hidden conventions
- [ ] 🟣 **Searchable Names**: Names are unique and searchable
- [ ] 🟣 **Context Independence**: Functions understandable without reading whole file
- [ ] 🟣 **Type Information**: Types available (TypeScript, type hints, etc.)
- [ ] 🟣 **Tests as Examples**: Tests demonstrate correct usage
- [ ] ⚫ **Contract Clarity**: Function contracts (input/output) are explicit

---

## 7. TESTING

### 7.1 Test Existence 🟢

- [ ] 🟢 **Tests Exist**: There are automated tests
- [ ] 🟢 **Tests Pass**: All tests pass
- [ ] 🟢 **Tests Run in CI**: Tests run automatically on commits

### 7.2 Test Coverage 🔵

- [ ] 🔵 **Happy Path Covered**: Main functionality is tested
- [ ] 🔵 **Edge Cases**: Some edge cases tested
- [ ] 🔵 **Error Paths**: Error conditions tested
- [ ] 🟣 **Coverage Measured**: Coverage >70% for critical paths
- [ ] 🟣 **No Flaky Tests**: Tests are deterministic
- [ ] ⚫ **Mutation Testing**: Test quality validated

### 7.3 Test Types 🟣

- [ ] 🟣 **Unit Tests**: Isolated unit tests exist
- [ ] 🟣 **Integration Tests**: Integration points tested
- [ ] 🟣 **API Tests**: API endpoints tested
- [ ] ⚫ **E2E Tests**: Critical user journeys tested
- [ ] ⚫ **Performance Tests**: Load testing performed
- [ ] ⚫ **Security Tests**: Security scanning integrated

### 7.4 Test Quality 🔵

> "Tests exist" and "tests pass" are insufficient checks. Read 3-5 actual test files and verify they test real behavior — not just imports, not just mock infrastructure.

- [ ] 🔵 **Tests Verify Behavior**: Read 3-5 test files. Tests assert on actual behavior and outputs, not just that modules are importable or that mocks return expected values
- [ ] 🔵 **Tests Would Catch Regressions**: For each test read, ask: would this test fail if the underlying logic broke? If the answer is no, the test is not providing value
- [ ] 🟣 **Tests Are Readable**: Tests serve as documentation
- [ ] 🟣 **Tests Are Maintainable**: Tests don't break on refactors
- [ ] 🟣 **Test Data Managed**: Test fixtures are managed properly — no silent skips when test data is missing
- [ ] ⚫ **Contract Tests**: For microservices, contracts tested

---

## 8. SECURITY

### 8.1 Authentication 🔵

- [ ] 🔵 **Auth Exists**: Authentication is implemented (if users exist)
- [ ] 🔵 **Passwords Hashed**: Passwords use bcrypt/argon2/scrypt
- [ ] 🔵 **Session Security**: Sessions are secure (HttpOnly, Secure cookies)
- [ ] 🟣 **MFA Available**: Multi-factor authentication available
- [ ] 🟣 **OAuth/OIDC**: Using standard protocols
- [ ] ⚫ **SSO Support**: Enterprise SSO (SAML, OIDC) supported

### 8.2 Authorization 🔵

- [ ] 🔵 **Authz Exists**: Authorization checks exist
- [ ] 🔵 **Authz Enforced**: Checks happen on backend, not just UI
- [ ] 🟣 **Role-Based Access**: RBAC or similar model
- [ ] 🟣 **Resource-Level**: Per-resource authorization
- [ ] ⚫ **Audit Trail**: Permission changes logged

### 8.3 Input Validation 🟢

- [ ] 🟢 **Input Validated**: User input is validated
- [ ] 🟢 **SQL Injection Prevented**: Parameterized queries used
- [ ] 🔵 **XSS Prevented**: Output encoding applied
- [ ] 🔵 **CSRF Protected**: State-changing operations protected
- [ ] 🟣 **File Upload Validated**: Uploads validated (type, size)

### 8.4 Data Protection 🔵

- [ ] 🔵 **HTTPS Only**: TLS for all external communication
- [ ] 🔵 **Sensitive Data Identified**: PII is identified
- [ ] 🟣 **Encryption at Rest**: Sensitive data encrypted
- [ ] 🟣 **Data Masked in Logs**: Sensitive data not logged
- [ ] ⚫ **Key Management**: Encryption keys properly managed

### 8.5 Dependency Security 🔵

- [ ] 🔵 **No Critical Vulnerabilities**: No known critical CVEs
- [ ] 🔵 **Dependencies Updated**: Dependencies reasonably current
- [ ] 🟣 **Automated Scanning**: Vulnerability scanning in CI
- [ ] 🟣 **Update Process**: Process for regular updates

---

## 9. MULTI-TENANCY (Tier 3+)

> Skip this section if building single-tenant software.

### 9.1 Tenant Isolation 🟣

- [ ] 🟣 **Data Segregation**: Tenant data cannot leak across tenants
- [ ] 🟣 **Query Scoping**: All queries scoped to tenant
- [ ] 🟣 **Cache Isolation**: Cached data segregated by tenant
- [ ] ⚫ **File Isolation**: Uploaded files isolated by tenant
- [ ] ⚫ **Background Job Isolation**: Jobs scoped to tenant

### 9.2 Tenant Configuration 🟣

- [ ] 🟣 **Per-Tenant Settings**: Tenants can customize settings
- [ ] 🟣 **Feature Flags**: Features can be toggled per tenant
- [ ] ⚫ **Custom Domains**: Tenants can use own domains
- [ ] ⚫ **Branding**: White-label support

### 9.3 Tenant Lifecycle ⚫

- [ ] ⚫ **Provisioning**: Automated tenant provisioning
- [ ] ⚫ **Data Export**: Tenants can export their data
- [ ] ⚫ **Data Deletion**: Complete tenant deletion supported
- [ ] ⚫ **Tenant Admin**: Tenant self-service administration

---

## 10. IDENTITY & SSO (Tier 3+)

### 10.1 SSO Support 🟣

- [ ] 🟣 **SAML 2.0**: SAML SSO supported
- [ ] 🟣 **OIDC**: OpenID Connect supported
- [ ] 🟣 **IdP Tested**: Tested with major IdPs (Okta, Azure AD, etc.)
- [ ] ⚫ **SCIM**: SCIM provisioning supported
- [ ] ⚫ **SSO Enforcement**: SSO can be enforced (disable password)

### 10.2 Session Management 🟣

- [ ] 🟣 **Session Timeout**: Appropriate idle and absolute timeouts
- [ ] 🟣 **Concurrent Sessions**: Control over concurrent sessions
- [ ] ⚫ **Session Revocation**: Can revoke all sessions
- [ ] ⚫ **Session Visibility**: Users can see active sessions

---

## 11. SCALABILITY & PERFORMANCE (Tier 2+)

### 11.1 Scalability 🔵

- [ ] 🔵 **Stateless Design**: Application state externalized
- [ ] 🔵 **Database Not Bottleneck**: Database can handle expected load
- [ ] 🟣 **Horizontal Scaling**: Can scale horizontally
- [ ] 🟣 **Auto-Scaling**: Auto-scaling configured
- [ ] ⚫ **No Single Points of Failure**: Redundancy in place

### 11.2 Performance 🔵

- [ ] 🔵 **Acceptable Response Time**: API responses <500ms typical
- [ ] 🔵 **Caching Used**: Caching where beneficial
- [ ] 🟣 **Background Jobs**: Expensive operations offloaded
- [ ] 🟣 **CDN for Static Assets**: Static assets served via CDN
- [ ] ⚫ **Performance Baselines**: SLOs defined and monitored

---

## 12. RELIABILITY (Tier 2+)

### 12.1 Fault Tolerance 🔵

- [ ] 🔵 **Errors Handled Gracefully**: App doesn't crash on errors
- [ ] 🔵 **External Calls Have Timeouts**: All network calls timeout
- [ ] 🟣 **Retries with Backoff**: Retries use exponential backoff
- [ ] 🟣 **Circuit Breakers**: Circuit breakers for external deps
- [ ] ⚫ **Graceful Degradation**: Fallbacks when dependencies fail

### 12.2 Data Durability 🔵

- [ ] 🔵 **Backups Exist**: Database is backed up
- [ ] 🟣 **Backups Tested**: Backups verified for recoverability
- [ ] 🟣 **Transactions Used**: Database transactions used correctly
- [ ] ⚫ **Point-in-Time Recovery**: PITR available
- [ ] ⚫ **DR Plan**: Disaster recovery plan documented and tested

---

## 13. OBSERVABILITY (Tier 2+)

### 13.1 Logging 🔵

- [ ] 🔵 **Logs Exist**: Application produces logs
- [ ] 🔵 **Log Levels Used**: Appropriate use of DEBUG, INFO, WARN, ERROR
- [ ] 🟣 **Structured Logging**: Logs are structured (JSON)
- [ ] 🟣 **Correlation IDs**: Request tracing across components
- [ ] 🟣 **Centralized Logs**: Logs aggregated centrally
- [ ] ⚫ **Sensitive Data Excluded**: No secrets/PII in logs

### 13.2 Monitoring 🟣

- [ ] 🟣 **Health Checks**: Health check endpoints exist
- [ ] 🟣 **Metrics Collected**: Key metrics instrumented
- [ ] 🟣 **Dashboards Exist**: Operational dashboards available
- [ ] ⚫ **SLI/SLO Defined**: Service levels defined and tracked
- [ ] ⚫ **Distributed Tracing**: Traces across service boundaries

### 13.3 Alerting 🟣

- [ ] 🟣 **Alerts Configured**: Alerts for critical failures
- [ ] 🟣 **Alerts Actionable**: Alerts are not noisy
- [ ] ⚫ **Runbooks Linked**: Alerts link to runbooks
- [ ] ⚫ **On-Call Rotation**: Proper on-call process

---

## 14. DEPLOYMENT & OPERATIONS

### 14.1 Build & Deploy 🟢

- [ ] 🟢 **Build Documented**: How to build is documented
- [ ] 🟢 **Deploy Documented**: How to deploy is documented
- [ ] 🔵 **Automated Build**: CI builds on every commit
- [ ] 🔵 **Automated Deploy**: Deployment is automated
- [ ] 🟣 **Infrastructure as Code**: IaC for infrastructure
- [ ] 🟣 **Environment Parity**: Environments are similar

### 14.2 Deployment Strategy 🟣

- [ ] 🟣 **Zero-Downtime**: Deployments don't cause downtime
- [ ] 🟣 **Rollback Capability**: Can rollback quickly
- [ ] ⚫ **Canary/Blue-Green**: Gradual rollout supported
- [ ] ⚫ **Feature Flags**: Feature flags for releases

### 14.3 Configuration 🔵

- [ ] 🔵 **Config Externalized**: Config not hardcoded
- [ ] 🔵 **Env-Specific Config**: Different config per environment
- [ ] 🟣 **Config Validated**: Config validated at startup
- [ ] 🟣 **Config Documented**: All config options documented

---

## 15. LICENSING & LEGAL

### 15.1 Dependencies 🔵

- [ ] 🔵 **Licenses Known**: Dependencies' licenses are known
- [ ] 🔵 **No Problematic Licenses**: No GPL/AGPL if incompatible with use
- [ ] 🟣 **License Inventory**: Complete license inventory exists
- [ ] 🟣 **Attribution Met**: Attribution requirements satisfied

### 15.2 Intellectual Property 🟣

- [ ] 🟣 **Copyright Notices**: Appropriate copyright notices
- [ ] 🟣 **Code Provenance Clear**: Origin of all code is clear
- [ ] ⚫ **CLA if Needed**: Contributor agreement if accepting contributions

---

## 16. DEVELOPER EXPERIENCE

### 16.1 Getting Started 🟢

- [ ] 🟢 **Setup Documented**: README has setup instructions
- [ ] 🟢 **Setup Works**: Following docs actually works
- [ ] 🔵 **Setup Time <30min**: New dev productive in 30 minutes
- [ ] 🔵 **Local Dev Easy**: Can run locally without cloud access

### 16.2 Development Workflow 🔵

- [ ] 🔵 **PR Process Clear**: How to contribute is documented
- [ ] 🔵 **CI Fast**: CI feedback in <10 minutes
- [ ] 🟣 **Hot Reload**: Fast iteration with hot reload
- [ ] 🟣 **Debugging Easy**: Debug configurations available

---

## OPEN SOURCE SOFTWARE ADDENDUM

> **Include this section if the project is open source.** Skip for proprietary software.

### OSS-1. License & Legal 🟢

- [ ] 🟢 **License File Exists**: LICENSE file in repository root
- [ ] 🟢 **License Choice Appropriate**: License matches project goals (MIT, Apache 2.0, GPL, etc.)
- [ ] 🔵 **License Headers**: Source files have license headers (if required by license)
- [ ] 🔵 **SPDX Identifier**: License identified with SPDX identifier
- [ ] 🟣 **Patent Grant**: License includes patent grant if needed (Apache 2.0 has this)
- [ ] 🟣 **DCO/CLA**: Developer Certificate of Origin or CLA for contributions
- [ ] 🟣 **Third-Party Licenses**: All third-party licenses documented and compatible
- [ ] ⚫ **REUSE Compliant**: Follows REUSE specification for license clarity

### OSS-2. Community Documentation 🟢

- [ ] 🟢 **README Quality**: Comprehensive README with:
  - [ ] Project description and purpose
  - [ ] Installation instructions
  - [ ] Quick start / basic usage
  - [ ] Link to documentation
- [ ] 🔵 **CONTRIBUTING.md**: Guide for contributors including:
  - [ ] How to submit issues
  - [ ] How to submit pull requests
  - [ ] Code style requirements
  - [ ] Testing requirements
- [ ] 🔵 **CODE_OF_CONDUCT.md**: Community code of conduct
- [ ] 🔵 **Issue Templates**: Templates for bugs and feature requests
- [ ] 🔵 **PR Template**: Template for pull requests
- [ ] 🟣 **GOVERNANCE.md**: Project governance documentation
- [ ] 🟣 **ROADMAP.md**: Public roadmap or link to project board
- [ ] 🟣 **CHANGELOG.md**: Maintained changelog (Keep a Changelog format)

### OSS-3. Security for Open Source 🔵

- [ ] 🔵 **SECURITY.md**: Security policy with:
  - [ ] How to report vulnerabilities
  - [ ] Security contact (email or form)
  - [ ] Disclosure timeline expectations
- [ ] 🔵 **No Secrets in History**: Git history has never contained secrets
- [ ] 🟣 **Security Advisories**: Process for publishing security advisories
- [ ] 🟣 **CVE Process**: Process for requesting CVEs for vulnerabilities
- [ ] 🟣 **Dependency Scanning Public**: Security scanning results visible
- [ ] ⚫ **Bug Bounty**: Bug bounty program (for larger projects)

### OSS-4. Versioning & Releases 🔵

- [ ] 🔵 **Semantic Versioning**: Following SemVer (MAJOR.MINOR.PATCH)
- [ ] 🔵 **Git Tags**: Releases have git tags
- [ ] 🔵 **Release Notes**: Releases have notes describing changes
- [ ] 🟣 **Stable API**: Public API stability commitments documented
- [ ] 🟣 **Deprecation Policy**: How deprecations are communicated
- [ ] 🟣 **LTS Policy**: Long-term support policy (if applicable)
- [ ] ⚫ **Release Signing**: Releases are cryptographically signed

### OSS-5. Distribution & Packaging 🔵

- [ ] 🔵 **Package Registry**: Published to appropriate registry (npm, PyPI, etc.)
- [ ] 🔵 **Install Works**: `npm install` / `pip install` works
- [ ] 🟣 **Multiple Formats**: Available in formats users expect
- [ ] 🟣 **Minimal Dependencies**: Runtime dependencies minimized
- [ ] ⚫ **Reproducible Builds**: Builds are reproducible

### OSS-6. Contribution Workflow 🔵

- [ ] 🔵 **Easy to Contribute**: First contribution is straightforward
- [ ] 🔵 **CI on PRs**: CI runs on pull requests
- [ ] 🔵 **Review Process**: PRs are reviewed before merge
- [ ] 🟣 **Good First Issues**: Issues labeled for newcomers
- [ ] 🟣 **Timely Responses**: Issues/PRs get responses within reasonable time
- [ ] 🟣 **Recognition**: Contributors recognized (CONTRIBUTORS file, release notes)
- [ ] ⚫ **Maintainer Guide**: Guide for maintainers

### OSS-7. Project Health & Sustainability 🟣

- [ ] 🟣 **Multiple Maintainers**: More than one active maintainer
- [ ] 🟣 **Succession Plan**: Plan if primary maintainer steps down
- [ ] 🟣 **Bus Factor >1**: Project survives if one person leaves
- [ ] 🟣 **Funding Documented**: Funding model documented (if applicable)
- [ ] ⚫ **Foundation/Org Backing**: Under an OSS foundation (for large projects)

### OSS-8. Testing & Quality for OSS 🔵

- [ ] 🔵 **Tests Run Publicly**: CI is public (GitHub Actions, etc.)
- [ ] 🔵 **Coverage Visible**: Test coverage badge/report
- [ ] 🟣 **Matrix Testing**: Tested against multiple versions (Node versions, Python versions)
- [ ] 🟣 **Platform Testing**: Tested on multiple platforms (Linux, macOS, Windows)
- [ ] ⚫ **Performance Benchmarks**: Public performance benchmarks

### OSS-9. Documentation for OSS 🔵

- [ ] 🔵 **API Reference**: Complete API documentation
- [ ] 🔵 **Examples**: Working code examples
- [ ] 🟣 **Tutorials**: Step-by-step tutorials
- [ ] 🟣 **Migration Guides**: Guides for major version upgrades
- [ ] 🟣 **Documentation Versioned**: Docs for each major version
- [ ] ⚫ **Translations**: Documentation available in multiple languages

### OSS-10. Things That DON'T Apply to Open Source

The following items from the main runbook typically don't apply to open source projects:

- ❌ **Proprietary License Concerns**: N/A for OSS
- ❌ **Trade Secrets in Code**: OSS code is public
- ❌ **Internal-Only Documentation**: Documentation should be public
- ❌ **SSO/Enterprise Auth (sometimes)**: Unless targeting enterprise users
- ❌ **Multi-Tenancy (usually)**: Unless it's a SaaS platform
- ❌ **SOC 2/Compliance Certifications**: Typically for commercial offerings
- ❌ **On-Call/Pager Duty**: Community support model is different
- ❌ **SLAs**: No contractual SLAs for community OSS

---

## PROPRIETARY SOFTWARE ADDENDUM

> **Include this section for proprietary/closed-source software.** These items don't apply to open source.

### PROP-1. Trade Secret Protection 🟣

- [ ] 🟣 **No Proprietary Algorithms Exposed**: Sensitive algorithms protected
- [ ] 🟣 **License Keys/Activation**: License enforcement if needed
- [ ] 🟣 **Obfuscation**: Code obfuscation if distributing binaries
- [ ] ⚫ **Audit Logging for IP**: Access to proprietary code logged

### PROP-2. Vendor Management 🟣

- [ ] 🟣 **Vendor Licenses Tracked**: Commercial library licenses managed
- [ ] 🟣 **License Compliance**: Within licensed seat/usage limits
- [ ] ⚫ **License Renewal Process**: Process for tracking renewals

### PROP-3. Customer Data Protection 🟣

- [ ] 🟣 **Data Isolation**: Customer data strictly isolated
- [ ] 🟣 **DPA Compliance**: Data Processing Agreements honored
- [ ] ⚫ **Data Residency**: Data stored in contracted regions
- [ ] ⚫ **Customer Audit Support**: Can support customer audits

---

## INDUSTRY-SPECIFIC ADDENDA

> Include the relevant addendum for your industry.

### Addendum: Financial Services 🟣

- [ ] 🟣 **Audit Logging**: Complete audit trail for transactions
- [ ] 🟣 **PCI-DSS**: Payment card compliance (if handling cards)
- [ ] ⚫ **Reconciliation**: Automated reconciliation
- [ ] ⚫ **Regulatory Reporting**: Regulatory report generation
- [ ] ⚫ **AML/KYC**: Anti-money laundering integration

### Addendum: Healthcare 🟣

- [ ] 🟣 **HIPAA Safeguards**: Technical safeguards implemented
- [ ] 🟣 **PHI Identified**: Protected Health Information tagged
- [ ] 🟣 **Minimum Necessary**: Only needed PHI accessed
- [ ] ⚫ **BAA Compliance**: Business Associate requirements met
- [ ] ⚫ **Emergency Access**: Break-glass procedures

### Addendum: E-Commerce 🔵

- [ ] 🔵 **PCI Compliance**: Payment security (or use PCI-compliant processor)
- [ ] 🔵 **Inventory Accuracy**: Real-time inventory
- [ ] 🟣 **Fraud Detection**: Fraud prevention integration
- [ ] 🟣 **Tax Calculation**: Accurate tax calculation

### Addendum: Government / Public Sector ⚫

- [ ] ⚫ **FedRAMP**: FedRAMP controls (if applicable)
- [ ] ⚫ **Section 508**: Accessibility compliance
- [ ] ⚫ **FIPS 140-2**: Cryptographic requirements
- [ ] ⚫ **Data Sovereignty**: Data residency requirements

---

## OUTPUT FORMAT

Generate a report appropriate to the project tier. **All reports should lead with strengths before findings.** Demonstrating that you understand what the team built well makes critical findings land as constructive guidance rather than an attack.

### Tier 1-2: Simplified Report

```markdown
## Codebase Review Summary

**Project**: [Name]
**Tier**: [1-Essential / 2-Standard]
**Date**: YYYY-MM-DD

### Quick Health Check: ✅ Pass / ⚠️ Issues / ❌ Fail

### Strengths
1. [What the codebase does well]
2. [Notable good practices]

### Key Findings

| # | Finding | Severity | Location | Fix |
|---|---------|----------|----------|-----|
| 1 | [Description] | 🔴/🟠/🟡/🟢 | `path:line` | [Action] |

### Recommended Actions
1. [Top priority action]
2. [Second priority]
3. [Third priority]
```

### Tier 3-4: Full Report

```markdown
## Enterprise Codebase Review

**Project**: [Name]
**Tier**: [3-Enterprise / 4-Mission-Critical]
**Date**: YYYY-MM-DD
**Reviewer**: Claude / [Name]

### Executive Summary

**Overall Score**: X/10

| Category | Score | Status |
|----------|-------|--------|
| [Category] | X/10 | 🟢/🟡/🔴 |

### Top Strengths
1. [Strength — demonstrate deep understanding of what the team built well]

### Top Critical Findings
1. [Finding with location and fix]

### Detailed Findings

#### [CAT-NUM] Finding Title
**Severity**: 🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low
**Location**: `path/to/file:line`
**Description**: [Details]
**Evidence**: `[code snippet]`
**Recommendation**: [Specific fix]
**Effort**: X hours/days

### Action Plan

**Immediate (Week 1)**:
- [ ] [Action]

**Short-Term (Month 1)**:
- [ ] [Action]

**Medium-Term (Quarter)**:
- [ ] [Action]
```

### Delta Review Mode

> When a prior review exists, use delta mode instead of a standalone report. A standalone review says "here are your problems." A delta review says "here's your trajectory." The second is far more useful for engineering leadership.

For each finding from the prior review, classify its current status:

| Status | Meaning |
|--------|---------|
| ✅ **Fixed** | Finding fully resolved |
| 🔄 **Partially Fixed** | Improvement made but not complete |
| ⏸️ **Still Present** | No change — deferred or not yet addressed |
| 📈 **Worse** | Finding has regressed or expanded in scope |
| 🆕 **New** | Finding not present in prior review |

```markdown
## Delta Review: [Project Name]

**Current Review Date**: YYYY-MM-DD
**Prior Review Date**: YYYY-MM-DD
**Tier**: [Tier]

### Trajectory Summary

| Status | Count |
|--------|-------|
| ✅ Fixed | X |
| 🔄 Partially Fixed | X |
| ⏸️ Still Present | X |
| 📈 Worse | X |
| 🆕 New | X |

**Overall Direction**: Improving / Stable / Declining

### Findings Detail

| # | Finding | Prior Status | Current Status | Notes |
|---|---------|-------------|----------------|-------|
| 1 | [Description] | 🔴 Critical | ✅ Fixed | [How it was resolved] |
| 2 | [Description] | 🟠 High | ⏸️ Still Present | [Context] |
| 3 | [Description] | — | 🆕 New | [New finding detail] |
```

### Deliverable Organization

> Date-stamp review deliverables in folders. When the next review happens, you'll thank yourself for having the prior review organized and accessible.

```
reviews/
├── 2025-01-15/
│   ├── review-summary.md
│   ├── detailed-findings.md
│   └── executive-report.pdf
├── 2025-04-15/
│   ├── delta-review.md         ← compares against 2025-01-15
│   ├── detailed-findings.md
│   └── executive-report.pdf
```

---

## CONTRIBUTING TO THIS RUNBOOK

This runbook is open source. Contributions are welcome!

### How to Contribute

1. **Report Issues**: File issues for unclear items, missing checks, or errors
2. **Suggest Additions**: Propose new checklist items with tier recommendations
3. **Submit PRs**: Fix typos, improve wording, add industry addenda
4. **Share Feedback**: Let us know how you're using this in your workflow

### Principles for Contributions

- **Practical over Theoretical**: Every item should be actionable
- **Tier-Appropriate**: Consider which tier(s) an item applies to
- **Evidence-Based**: Items should catch real issues found in real codebases
- **AI-Friendly**: Wording should be clear enough for AI assistants to evaluate

---

## CHANGELOG

### Version 2.1
- Added Pre-Flight Checklist with branch selection verification
- Added AI Tool Configuration File checks (Section 2.2a) — .cursorrules, .cursor/, .aider*, etc.
- Added Comment-Aware Credential Scanning (Section 2.2b) — comments, TODOs, and commented-out blocks in scope
- Strengthened Preventive Controls (Section 2.4) — specific tools (gitleaks, truffleHog), GitHub secret scanning
- Enhanced Test Quality (Section 7.4) — lowered to Tier 2, added behavioral verification: read actual tests, not just check they exist
- Added Delta Review Mode to Output Format — five-point status tracking (fixed, partially fixed, still present, worse, new)
- Added Strengths-First output format for all tiers — strengths before findings in both simplified and full reports
- Added Deliverable Organization guidance — longitudinal folder structure for date-stamped review artifacts
- Added link to usage guide and example prompts: [Code Review That Scales](https://synthesiscoding.org/articles/code-review-that-scales/)

### Version 2.0
- Added tiered system (Essential, Standard, Enterprise, Mission-Critical)
- Added Project Complexity Assessment
- Added Minimum Viable Review quick check
- Added Open Source Software Addendum
- Added Proprietary Software Addendum
- Added tier markers (🟢🔵🟣⚫) to all items
- Restructured for practical, incremental adoption

### Version 1.2
- Expanded secrets detection section
- Added code duplication & reusability section
- Added code efficiency & optimization section
- Added clean code & software engineering principles
- Added AI/human maintainability considerations
- Added database & data layer section
- Added version control & git hygiene section

### Version 1.1
- Added multi-tenancy section
- Added SSO and identity management
- Added concurrent users handling
- Added internationalization and accessibility
- Added rate limiting and feature flags
- Added industry-specific addenda

### Version 1.0
- Initial release with comprehensive enterprise checklist

---

*Enterprise-Grade Codebase Review Runbook v2.1*
*Part of the [Ragbot.AI](https://ragbot.ai) project*
*Based on [Synthesis Coding](https://synthesiscoding.org) methodology*
*Guide & prompts: [Code Review That Scales](https://synthesiscoding.org/articles/code-review-that-scales/)*
*Licensed under MIT License*
