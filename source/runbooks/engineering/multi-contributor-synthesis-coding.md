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
4. **Test the integrated result.** Run the full test suite. Test the feature manually. Verify nothing regressed.
5. **Merge to canonical `main`.** This is the source of truth.
6. **Sync mirrors/forks.** Push the updated `main` to any mirrors so contributors have the latest code for their next contribution.

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

## Applying this to new projects

Any synthesis-coded project that expects external contributions should:

1. **Create a contributor guide** in the project's docs. Doesn't need to be comprehensive on day one — it will grow through integration cycles.
2. **Establish the canonical repository** as the single source of truth. Contributors work on forks or mirrors; integration flows through the lead.
3. **Define quality gates** appropriate to the project. Security, architecture, and completeness are universal; project-specific standards vary.
4. **Use adopt-and-adapt from the first contribution.** Don't start with blind merges and try to add quality gates later. The precedent you set with the first contribution defines the culture.

---

*This runbook is part of the synthesis engineering practice. It was developed from real integration experience on a production multi-contributor project.*
