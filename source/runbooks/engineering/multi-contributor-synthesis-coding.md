---
title: Multi-contributor synthesis coding
description: Runbook for integrating external contributions into a synthesis-coded project. Covers the adopt-and-adapt pattern, quality gates, and the lead synthesist role.
author: Rajiv Pant
date: 2026-02-13
categories:
  - Synthesis Engineering
  - Synthesis Coding
  - Project Management
---

# Multi-contributor synthesis coding

When multiple people contribute to a project built through synthesis coding, integration is fundamentally different from a standard open source merge workflow. This runbook defines how it works.

---

## The core problem

In synthesis coding, the lead developer (the "lead synthesist") builds and evolves the system through continuous, context-rich collaboration with AI. The result is a codebase with:

- Deep architectural consistency — decisions compound across sessions
- Implicit conventions — not all standards are documented yet because one person held them all in their head
- Rapid evolution — the codebase may change substantially between the time an external contributor branches off and the time they submit a PR

When an external contributor forks or branches, they get a snapshot. They build against that snapshot. Meanwhile, the lead may have evolved the architecture, introduced new patterns, improved output quality, or refactored entire subsystems. The contributor's code reflects the old state.

A blind merge risks:

- **Regression** — undoing improvements the lead made after the contributor branched
- **Inconsistency** — introducing patterns that conflict with the codebase's evolved conventions
- **Security gaps** — missing safeguards the lead added (audit logging, rate limiting, input validation)
- **Quality drift** — code that works but doesn't meet the project's current bar

The standard open source answer — "submit a PR, we'll merge it" — doesn't account for this. What's needed is a disciplined integration methodology.

---

## Adopt-and-adapt: the integration pattern

The lead synthesist does not merge external contributions directly. Instead:

1. **Adopt** the intent, the design, and the valuable implementation work
2. **Adapt** the code to meet current standards, architecture, and quality bar

This is neither a merge nor a rewrite. It's selective integration with improvement. The contributor's work is the foundation; the lead brings it up to production standard.

### Why this works

- **Respects the contributor's work.** Their design thinking, feature concept, and implementation effort are preserved.
- **Maintains quality.** The lead synthesist is the quality gate. Nothing ships that doesn't meet the bar.
- **Avoids regression.** By starting from current `main` and selectively pulling in changes, the lead never risks overwriting recent improvements.
- **Educates through feedback.** The review process teaches contributors the project's standards, making future contributions smoother.

### Why direct merge doesn't work

- The contributor didn't have the latest context. Their code is correct for a codebase that no longer exists in that exact form.
- Standards evolve faster than documentation. The lead synthesist holds conventions that aren't yet written down. Integration is when those conventions get documented.
- AI-accelerated development means the codebase moves fast. A branch that's a week old may be dozens of commits behind.

---

## Roles

### Lead synthesist

The person who holds the architectural vision, maintains the quality bar, and has the deepest context on the system. In a synthesis-coded project, this is typically the person who built the system through sustained AI collaboration.

**Responsibilities:**
- Defines and evolves project standards
- Reviews all external contributions
- Performs adopt-and-adapt integration
- Maintains the canonical repository
- Controls production deployments
- Documents standards as they emerge through the integration process

**Key principle:** The lead synthesist's standards ARE the project's standards. Integration is the forcing function that makes those standards explicit and documented.

### Contributors

Developers who build features on branches or forks. They may or may not use synthesis coding themselves.

**Responsibilities:**
- Understand existing standards before building (read the contributor guide)
- Submit complete features (both frontend and backend, with tests)
- Maintain clean branch hygiene (one feature per branch, meaningful commits)
- Respond to review feedback and iterate

**Key principle:** The goal is to make integration easy. The closer the contribution is to the project's standards, the less adaptation is needed.

---

## Contribution workflow

### Before building

1. **Read the project's contributor guide.** Every synthesis-coded project with external contributors should maintain one. It documents the standards that would otherwise live only in the lead's head.
2. **Sync to latest main.** Don't build on stale code.
3. **Discuss the approach for significant features.** Especially anything touching auth, security, the data model, or user-facing architecture. A 10-minute discussion prevents days of rework.

### While building

1. **One feature per branch.** Never bundle unrelated work. If you start a second feature while working on the first, create a new branch.
2. **Complete features only.** A frontend modal that calls an API endpoint that doesn't exist is not a shippable unit. Backend + frontend + tests = complete.
3. **Follow existing patterns.** Before creating a new component, search the codebase for similar ones. Match the style, structure, naming conventions, and error handling patterns.
4. **Meaningful commits.** Use conventional commit format (`feat:`, `fix:`, `docs:`, `test:`, `chore:`). The lead should be able to understand the intent of each commit from its message.

### Submitting

1. **Rebase onto latest main.** Minimize divergence.
2. **Clean diff.** No debugging artifacts, no commented-out experiments, no unrelated changes.
3. **Share early if uncertain.** A draft PR with a question is better than a finished PR that needs fundamental rework.

---

## The integration process

When the lead synthesist receives a contribution, the process is:

### Step 1: Assess

- Fetch the branch and review the diff
- Identify what the contribution does, what it changes, and what assumptions it makes
- Check: how far has `main` moved since the contributor branched off?
- Check: does the contribution touch sensitive areas (auth, security, data model, user-facing text)?

### Step 2: Review

Evaluate against the project's quality gates (see next section). Produce written feedback covering:

- **What's strong** — acknowledge good work. Contributors who feel respected produce better work.
- **What must change** — security issues, broken functionality, standards violations. Be specific: name the issue, explain why it matters, and suggest the fix.
- **What should change** — code quality improvements, performance concerns, architectural suggestions. Distinguish "must fix" from "should fix."

### Step 3: Integrate (adopt-and-adapt)

1. **Create a fresh branch off current `main`.** Never merge the contributor's branch directly.
2. **Selectively bring in changes.** File by file, function by function. Cherry-pick the implementation, not the entire branch.
3. **Fix identified issues during integration.** Don't merge first and fix later. The adapted code should be production-ready when it hits `main`.
4. **Test the integrated result.** Run the full test suite — not just the tests for the PRs being merged. Cross-PR conflicts hide in tests that none of the individual PRs touched. Test the feature manually. Verify nothing regressed.
5. **Squash merge to canonical `main`.** This is the source of truth. Use contributor attribution (see below).
6. **Sync mirrors/forks.** Push the updated `main` to any mirrors so contributors have the latest code for their next contribution.

#### Contributor attribution in squash merge commits

GitHub's contributor graph counts commits where you are the **author**. Custom text like `Contributor: Name (PR #5)` in the commit body is human-readable but GitHub does not parse it — the contributor will not appear on the repository's contributor graph or their own profile contribution history.

Use `Co-authored-by` trailers, which GitHub officially recognizes:

```
feat: add product description field to content pipeline

Integrates product description generation with writer guidance support.

Co-authored-by: Contributor Name <contributor@example.com>
```

The attribution model should match the integration intensity:

- **Full adopt-and-adapt** (substantial rework): Lead as commit author, contributor as `Co-authored-by`. Both did meaningful work; the lead did more of the final implementation.
- **Lighter-touch integration** (minor adjustments): Contributor as commit `--author`, lead as `Co-authored-by`. The code is predominantly the contributor's; the lead refined it.
- **Direct merge** (zero-adjustment): Standard PR merge flow. The contributor is automatically the commit author. The lead's review is recorded in the PR, not the commit.

To set the contributor as primary author on a squash merge:

```bash
git commit --author="Contributor Name <contributor@example.com>" -m "feat: description

Co-authored-by: Lead Name <lead@example.com>"
```

Attribution is not decoration. Developers use contribution graphs for career advancement. A workflow that funnels all commits through the lead's name effectively erases contributors from the project's visible history, which undermines the trust that the integration process depends on.

### Step 4: Communicate

- Share the integration review with the contributor
- Explain what was changed and why — this is how standards transfer
- Acknowledge their contribution's value
- Note lessons that should go into the contributor guide to prevent recurrence

---

## Quality gates

Every contribution must pass these gates before integration. The lead synthesist evaluates these; they're not automated checks (though some could be).

### Gate 1: Completeness

- Feature is fully implemented (not half-frontend, half-backend)
- No dead code, no references to methods that don't exist
- No dependency on unreleased or unmerged work
- Tests exist for new backend logic

### Gate 2: Security

- Privileged operations produce audit log entries
- Auth tokens are handled correctly (claims propagated through refresh, appropriate expiry)
- Rate limiting on sensitive endpoints
- No credentials or secrets hardcoded in code
- Input validation at system boundaries
- User data exposure reviewed (no unnecessary information leakage)

### Gate 3: Architecture

- One feature per branch (no bundled unrelated changes)
- Follows existing codebase patterns (component structure, API client usage, error handling)
- Uses framework features properly (not fighting the framework with workarounds)
- No regression of existing functionality
- No unnecessary complexity (solves the problem without over-engineering)

### Gate 4: Project-specific standards

These vary by project. For any synthesis-coded project, the lead synthesist defines what matters. Examples:

- Brand/white-labeling compliance
- UI terminology rules
- Deployment safety rules
- Database migration approach
- Performance expectations

The contributor guide should document these. If a standard isn't documented and a contributor violates it, that's the lead's responsibility to document it — not the contributor's fault.

---

## Communication and feedback

### Principles

- **Be specific, not vague.** "This has security issues" is useless. "The impersonation endpoint needs audit logging because the codebase uses `log_audit_event()` for all privileged operations — see `access_control.py` for the pattern" is actionable.
- **Explain the why.** Contributors who understand the reasoning behind a standard will follow it naturally in future work. Contributors who receive rules without context will keep violating them.
- **Acknowledge good work.** Name the things that were well done. People do more of what gets recognized.
- **Distinguish severity levels.** "Must fix before production" vs. "should fix" vs. "consider for future." Not everything is critical.

### The integration review document

For each set of contributions, the lead should produce a written review that covers:

1. **Project standards the contributor needs to know** — extracted from the lead's implicit knowledge and made explicit
2. **Specific feedback on each PR** — strengths, issues, recommendations
3. **The integration plan** — what the lead will do with the code and in what order
4. **Contribution workflow for next time** — how to submit work that integrates more smoothly

This document serves double duty: it's feedback for the contributor AND it's documentation for the project. Standards that exist only in the lead's head are standards that will be violated.

---

## Integrating multiple PRs

When integrating more than two or three PRs in a session, ordering becomes a design decision.

### Dependency-aware ordering

Before starting integration, map the file overlaps across all pending PRs. Group PRs by subsystem. Then:

1. **Independent PRs first.** Zero-overlap PRs (bug fixes, isolated features) validate that the integration pipeline is working before you tackle complex merges. Start with the simplest.
2. **Within a subsystem, simpler PR first.** When multiple PRs modify the same files, integrate the smaller/simpler one first. This makes subsequent conflicts predictable and additive rather than interleaving.
3. **Read the merged result fresh.** After auto-merge resolves conflicts, read the merged code as if reviewing it for the first time. Git handles textual conflicts; you handle semantic conflicts. Auto-merged regions (no conflict markers) can still produce semantic errors — duplicate function calls, redundant parameters, broken assumptions — that no tool will flag.

### Cross-PR test failures

Each PR may pass its own CI independently. The synthesis merge still catches failures caused by cross-PR interactions:

- A PR adds a size threshold; existing tests use mock data below that threshold
- A PR constrains a mock with `spec=`; another PR's tests rely on unconstrained attribute access
- A PR adds a new code path; an "all paths fail" test only mocks the original paths

Run the **full** test suite on the integration branch — not just the tests for the PRs being merged. Cross-PR conflicts hide in tests that none of the individual PRs touched.

---

## Lessons and anti-patterns

### Anti-pattern: the blind merge

Merging a contribution without reviewing it against current standards. Even if CI passes, the code may introduce patterns that conflict with the project's architecture, miss security requirements, or regress quality.

**Prevention:** Every external contribution goes through adopt-and-adapt. No exceptions.

### Anti-pattern: bundled features

Multiple unrelated features in a single branch or PR. This makes review harder, creates unnecessary merge conflicts, and means you can't merge feature A without also merging unfinished feature B.

**Prevention:** One feature per branch. Enforce this as a contribution requirement.

### Anti-pattern: orphaned half-features

Frontend code that calls backend endpoints that don't exist (or vice versa). This ships dead code that confuses future readers and creates merge conflicts.

**Prevention:** Require complete features — both sides of the stack, tested together.

### Anti-pattern: auto-deploy without approval

CI/CD pipelines that deploy to production on push to main with no manual approval gate. This removes the lead's control over production and can ship unreviewed changes.

**Prevention:** Production deploys always require explicit human approval. Staging can be automatic.

### Anti-pattern: stale documentation

Integration documents, contributor guides, and design docs that describe the system as it was, not as it is. Stale docs are worse than no docs because they mislead contributors.

**Prevention:** Update the contributor guide as part of every integration cycle. When you adapt a contribution and find a standard that wasn't documented, document it.

### Lesson: integration is when standards get documented

The act of reviewing external contributions forces the lead synthesist to make implicit standards explicit. Embrace this. Every integration review should leave the contributor guide more comprehensive than before.

### Lesson: the contributor guide is a living document

It should grow with every integration. New standards discovered during review get added. Stale standards get updated. The guide is never "done" — it evolves with the project.

### Lesson: review the branches, not just the PRs

Contributors may have branches beyond what's in the PRs. Fetch all remote branches and understand the full scope of work before starting the review. Surprises during integration are costly.

---

## Evolution of integration intensity

Adopt-and-adapt at full intensity is a starting point, not a permanent state. As contributors learn the codebase and the quality bar rises, the integration process should get lighter. The goal is graduated evolution:

### Phase 1: Full adopt-and-adapt

The lead creates a fresh integration branch, selectively brings in changes, and fixes every issue during integration. The contributor's code is the input; the integrated code may look substantially different.

**When appropriate:** First contributions from a new contributor. Contributions that touch sensitive areas. Contributions built against a significantly stale `main`. Contributions that violate multiple project standards.

**Signals ready to advance:** Fewer issues in successive reviews. Issues are minor (style, naming) rather than structural (security, architecture). Contributor references the contributor guide unprompted. Branch hygiene improves.

### Phase 2: Lighter-touch integration

The lead merges the contribution with minor adjustments rather than selective file-by-file rework. The contributor's code structure is preserved; the lead cleans up edges.

**When appropriate:** Contributor has had at least one round of detailed review feedback. Current contribution follows established patterns. Issues are minor and localized. No security or architectural concerns.

### Phase 3: Direct merge with review

Standard pull request workflow. The contributor submits a PR, it gets peer review and lead review, and it merges directly (squash merge) to `main`.

**When appropriate:** Contributor consistently meets the quality bar across multiple contributions. Peer reviews consistently catch remaining issues. The lead's review adds no changes — just approval.

### Adjusting in both directions

The phases are not permanent promotions. If a contribution introduces a security gap or architectural regression, the intensity goes back up. Track the adjustment count per PR as the objective measure. When it consistently approaches zero, the team is ready for more autonomy.

**Upgrade signal:** Fewer than half the issues of the previous round, and those issues are cosmetic rather than structural.

**Downgrade signal:** A contribution introduces a security gap, architectural regression, or the contributor starts bundling unrelated changes again.

---

## Convention review checklist

Contributors using AI coding tools produce code that is functionally correct but drifts from project-specific conventions. AI tools follow general best practices but lose project-specific conventions (brand terminology, messaging rules, CSS framework patterns, role-based access conventions) when the context window gets long.

The fix is systematic, not behavioral. Make convention verification a formal step in the review process:

### Standard items (every project)

1. **Correctness** — edge cases, race conditions, error paths
2. **Existing pattern adherence** — matches codebase conventions for component structure, API usage, error handling
3. **Test coverage** — new backend logic has tests; existing tests still pass
4. **Security** — audit logging, auth handling, input validation

### Project-specific items (define per project)

These are the conventions that AI tools miss because they don't exist in training data. Examples:

- Brand terminology compliance (if the project has a terminology system)
- AI messaging rules (if the project governs how AI capabilities are described to users)
- CSS/UI framework conventions (if the project has a style guide or design system)
- Role-based access patterns (if the project has visibility/permission rules)

Add this checklist to the project's CLAUDE.md or contributor guide. Make it a formal gate in integration review, not an optional pass. The standard items are universal. The project-specific items are where convention drift actually happens.

---

## Staging branch management

When the project uses a staging branch (`develop`) that auto-deploys to a staging environment, the staging branch is a **convergence point**, not a downstream mirror of `main`.

Two independent streams converge on the staging branch:
1. The lead synthesist's curated work from `main` (squash-merged from integration branches)
2. Contributors' in-progress work (pushed to `develop` for staging validation)

**Never force-push `main` to the staging branch.** This destroys contributors' unintegrated work. Instead, merge `main` into the staging branch:

```bash
git fetch origin
git checkout -b temp-staging origin/develop
git merge main
git push origin temp-staging:develop
git checkout main
git branch -d temp-staging
```

Check for divergence before every push to the staging branch. If it has commits that aren't on `main`, merge rather than overwrite.

---

## Applying this to new projects

Any synthesis-coded project that expects external contributions should:

1. **Create a contributor guide** in the project's docs. Doesn't need to be comprehensive on day one — it will grow through integration cycles.
2. **Establish the canonical repository** as the single source of truth. Contributors work on forks or mirrors; integration flows through the lead.
3. **Define quality gates** appropriate to the project. Security, architecture, and completeness are universal; project-specific standards vary.
4. **Use adopt-and-adapt from the first contribution.** Don't start with blind merges and try to add quality gates later. The precedent you set with the first contribution defines the culture.

---

*This runbook is part of the synthesis engineering practice. It was developed from real integration experience on a production multi-contributor project.*
