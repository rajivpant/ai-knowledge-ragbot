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

**Key principle:** The lead synthesist's standards ARE the project's standard