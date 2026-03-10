---
title: Synthesis PR review
description: Runbook for reviewing pull requests in synthesis-coded projects. Covers delta review methodology, regression risk assessment, and integration with the adopt-and-adapt workflow.
author: Rajiv Pant
date: 2026-03-10
categories:
  - Synthesis Engineering
  - Synthesis Coding
  - Code Review
related:
  - codebase-review.md
  - multi-contributor-synthesis-coding.md
---

# Synthesis PR review

A pull request in a synthesis-coded project is not just "does the code work?" It's "does this change make the system better without making it worse?" This runbook defines how to evaluate that.

---

## Where this fits

Three related runbooks cover different scopes:

| Runbook | Scope | When to use |
|---------|-------|-------------|
| **Codebase review** | Full codebase audit (16 categories, tiered) | New engagement, periodic health check, or major milestone |
| **Multi-contributor synthesis coding** | Integration workflow (adopt-and-adapt pattern, quality gates) | When merging contributor work into main |
| **PR review** (this one) | Delta review of a single change | Every PR, before peer approval or lead integration |

PR review is the **most frequent** of the three. It happens on every change. The quality gates from the multi-contributor runbook apply here, but this runbook operationalizes them as specific review steps.

---

## The delta review mindset

A PR review is a delta review — you're evaluating a change against the current state of the codebase, not evaluating the codebase itself.

**Key questions:**

1. **Does this change do what it claims?** — Read the PR description. Read the code. Do they match?
2. **Does it introduce regressions?** — What worked before that might break now?
3. **Is it the right fix?** — Does it address root cause, or a symptom?
4. **Is it complete?** — Or does it need companion changes to actually solve the problem?
5. **Is it consistent?** — Does it follow existing patterns, or does it diverge without justification?

---

## Review checklist

### 1. Scope and separation of concerns

- [ ] PR does one thing (not multiple unrelated fixes bundled together)
- [ ] PR description accurately describes the change and its motivation
- [ ] If the PR bundles fixes, each fix is clearly identified and could stand alone

**Red flag:** A PR titled "fix X" that also quietly changes Y. Bundled changes are harder to review, harder to revert, and harder to attribute when something breaks later.

### 2. Root cause analysis

- [ ] The fix addresses the actual root cause, not a downstream symptom
- [ ] If the root cause is complex, the PR explains why this specific approach was chosen
- [ ] The PR doesn't mask a deeper architectural issue

**How to evaluate:** Ask yourself — if the underlying condition that caused the bug occurs again in a slightly different way, does this fix still work? If not, it's a symptom fix.

**Example:** A UI polling loop appears stuck after retry. The fix restarts the polling timer (symptom fix). But the real cause is the backend task silently failing without updating status. The UI fix makes retries look responsive, but the backend still leaves records in a permanent "pending" state.

### 3. Regression risk

- [ ] No existing behavior is broken by the change
- [ ] Error handling paths are preserved (not accidentally removed)
- [ ] Edge cases still work (empty states, error states, concurrent access)
- [ ] If the PR modifies shared code, all callers are accounted for

**Technique:** Read the diff backward — look at what was REMOVED or CHANGED, not just what was added. Removed lines are where regressions hide.

### 4. Architectural consistency

- [ ] The pattern used matches how similar things are done elsewhere in the codebase
- [ ] If the pattern diverges from existing code, the divergence is justified
- [ ] No architectural debt is introduced without acknowledgment

**Technique:** Find the closest analog in the codebase. If component A handles retries one way and this PR makes component B handle retries a different way, ask why. Sometimes the divergence is intentional and better. Often it's accidental.

### 5. Completeness

- [ ] The fix is sufficient to actually solve the stated problem
- [ ] If companion changes are needed (backend + frontend, migration + code), they're either included or explicitly tracked
- [ ] Tests cover the new behavior (or a clear reason why they don't)

**Red flag:** A frontend fix for a problem whose root cause is in the backend. The frontend fix may reduce user-visible symptoms, but the underlying data corruption or stuck state persists.

### 6. Security (from quality gates)

- [ ] No credentials or secrets in the diff
- [ ] Input validation at system boundaries
- [ ] Auth checks preserved for protected endpoints
- [ ] No new SQL injection, XSS, or command injection vectors

### 7. Data integrity

- [ ] Database schema changes are idempotent (safe to run multiple times)
- [ ] New fields have sensible defaults or are nullable
- [ ] No data loss scenarios (e.g., overwriting fields without preserving previous values)
- [ ] API contracts are backward-compatible (or breaking changes are intentional and documented)

---

## The review process

### For peer reviewers

Peer review catches issues early and spreads codebase knowledge. Focus on:

1. **Does the code make sense?** — Can you follow the logic without the author explaining it?
2. **Does it match the PR description?** — If not, which is wrong — the code or the description?
3. **Would you be comfortable debugging this at 2 AM?** — If the answer is no, the code needs to be clearer.
4. **Check the analog.** — Find the closest similar code in the codebase. Does this PR follow the same pattern?

Peer reviewers should feel empowered to request changes, not just approve. A rubber-stamp approval is worse than no review — it creates false confidence.

### For the lead synthesist

Lead review gates the merge to main. In addition to everything above, evaluate:

1. **Project-specific standards** — white-labeling compliance, UI terminology, deployment safety, whatever the project requires
2. **Architectural fit** — does this change move the codebase in the right direction?
3. **Integration complexity** — what will the adopt-and-adapt process look like? Is the change clean enough to merge directly, or does it need adaptation?
4. **Completeness of the solution** — does this fully solve the problem, or is it a partial fix that needs follow-up work tracked?

### Writing review feedback

- **Be specific.** "This has issues" is useless. "Line 47 removes the error recovery path — if the API call fails, polling never resumes" is actionable.
- **Explain the why.** Don't just say what's wrong; explain the consequence. "This could cause the UI to appear frozen if the retry fails" tells the author what to look for.
- **Distinguish severity.** Use clear labels:
  - **Must fix** — blocks merge, causes regression or data loss
  - **Should fix** — doesn't block merge, but should be addressed soon
  - **Consider** — suggestion for improvement, not blocking
  - **Nit** — style or preference, take it or leave it
- **Acknowledge what's good.** Name specific things done well. This is not politeness — it reinforces patterns you want to see again.

---

## Common anti-patterns

### The rubber stamp

Approving without actually reading the code. Usually happens when the team is busy and reviews feel like overhead. Worse than no review — it creates a false record of review.

**Fix:** If you don't have time to review properly, say so. "I can't review this today — can someone else take it?" is honest and professional.

### The bundled PR

Multiple unrelated changes in one PR. Makes review harder, makes git bisect useless, and makes reverts dangerous.

**Fix:** Request the author split the PR. "The source_url fix and the polling fix are separate concerns — could you split them so we can review and merge independently?"

### The symptom fix

A fix that makes the visible problem go away without addressing the underlying cause. The problem will resurface in a different form.

**Fix:** Ask "what happens if the underlying condition occurs again in a slightly different way?" If the fix doesn't help, it's a symptom fix.

### The untested assumption

"This should work" without verification. Especially dangerous for fixes to bugs that are hard to reproduce.

**Fix:** Ask for reproduction steps and verification. "Can you show me the before/after behavior?" If the bug can't be reproduced locally, that's important information — it may mean the fix is addressing the wrong thing.

### The divergent pattern

Implementing something one way in component A while the rest of the codebase does it another way. Creates maintenance burden and confusion for future developers.

**Fix:** Point to the existing pattern. "PlatformMatrixView handles this by calling pollStatus() immediately — should we align?"

---

## Integration with the adopt-and-adapt workflow

When a PR passes review and is ready for integration:

1. **If the PR is clean** — merge directly (rare for synthesis-coded projects, but possible as contributor quality improves)
2. **If the PR needs adaptation** — the lead synthesist creates an integration branch, applies the adopt-and-adapt pattern, and merges the adapted version
3. **If the PR needs follow-up work** — merge what's ready, create tickets for the remaining work, and document the dependency

The review findings feed directly into the integration plan. A review that says "the polling fix works but the backend error handler is still needed" tells the lead exactly what to do during integration.

---

## Using the codebase review runbook for PR review

The enterprise codebase review runbook (see `codebase-review.md`) has 16 categories with tiered checks. Not all are relevant to a single PR. For delta reviews, apply selectively:

| Always check (every PR) | Check if relevant |
|-------------------------|-------------------|
| Security (Gate 2) | Performance (if the change touches hot paths) |
| Architecture (Gate 3) | Database (if schema changes are involved) |
| Completeness (Gate 1) | API design (if endpoints are added/modified) |
| Error handling | Observability (if logging/monitoring is affected) |

The codebase review runbook is the reference catalog. This PR review runbook tells you which items to pull from it for a given change.
