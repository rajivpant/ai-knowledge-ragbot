---
title: Context lifecycle management
description: >
  Managing AI working memory across long-running projects using the tiered
  context architecture. Covers the three-tier structure (CONTEXT.md /
  REFERENCE.md / sessions/), archival protocols, budget enforcement,
  migration strategies, and quality metrics.
author: Rajiv Pant
date: 2026-03-04
categories:
  - Synthesis Engineering
  - Project Management
---

# Context lifecycle management

## The problem

AI collaborators start every session with zero context. Their effectiveness depends entirely on the quality of the context they receive. For short-lived projects (2-3 sessions), a single context file works. For long-running projects spanning weeks or months, that file grows unboundedly — combining four types of information with fundamentally different lifecycles:

| Information type | Access pattern | Growth pattern | Ideal treatment |
|-----------------|----------------|----------------|-----------------|
| **Working memory** (current state, active tasks) | Every session | Constant | Keep lean, refresh often |
| **Episodic memory** (session logs) | Rarely after 1 week | Unbounded append | Archive monthly |
| **Semantic memory** (stable facts, reference) | Most sessions | Slow, update-in-place | Separate file |
| **Completed work records** | Almost never | Unbounded append | Delete after archiving |

Combining all four in one file means the file grows linearly with session count, with no mechanism for information to leave. This is the classic **hot/warm/cold data problem** from database engineering, manifesting in AI context management.

## The architecture

### Three tiers

```
project/
├── CONTEXT.md      # Working memory (budget: ≤150 lines)
├── REFERENCE.md    # Semantic memory (stable facts, update in place)
├── sessions/       # Episodic memory (archived session logs)
│   └── YYYY-MM.md  # Monthly files
└── [other files]   # Transcripts, artifacts, etc.
```

This maps to both cognitive science and systems engineering:

| Human memory | CPU cache | Synthesis equivalent | Properties |
|-------------|-----------|---------------------|------------|
| Working memory | L1 cache | CONTEXT.md | Small capacity, constantly refreshed, always loaded |
| Semantic memory | L2 cache | REFERENCE.md | Facts and relationships, updated in place, loaded on demand |
| Episodic memory | L3 cache | sessions/ | Chronological events, append-only, searched when needed |
| Procedural memory | Firmware | CLAUDE.md + _lessons/ | How to do things, rules, patterns |

These aren't metaphors — they're design principles. Each memory type has different storage, retrieval, and maintenance characteristics. Treating them identically is like a database that puts hot transactional data and cold analytics data in the same table with no partitioning.

### CONTEXT.md — working memory

**Purpose:** Everything the AI collaborator needs to be effective in THIS session.

**Budget:** ≤150 lines (hard). For completed projects: ≤80 lines.

**Contains ONLY:**
- Phase/status header (~5 lines)
- Current state (~15 lines)
- Active tasks with priorities (~50 lines)
- Recent session summaries — last 1-2 only (~30 lines)
- Links to REFERENCE.md and sessions/ (~5 lines)
- Budget footer (~2 lines)

**Does NOT contain:**
- Completed task checklists (archive to sessions/ first, verify, then remove)
- Session logs older than 1 week (move to sessions/)
- Stable reference facts (live in REFERENCE.md)
- Detailed historical narrative (live in session archive)

**Template — new project (day 1):**

```markdown
# [Project Name] — Working Context

**Phase:** Initial
**Status:** [description]
**Last session:** YYYY-MM-DD

---

## Current State

[What exists, what doesn't, starting conditions]

## What's Next

1. [ ] [First task]
2. [ ] [Second task]

---

*This file follows the Tiered Context Architecture. Budget: ≤150 lines.*
```

**Template — mature project (day 100):**

```markdown
# [Project Name] — Working Context

**Phase:** [Current phase]
**Status:** [Active/Paused]
**Last session:** YYYY-MM-DD

For stable reference facts: see [REFERENCE.md](REFERENCE.md)
For session history: see [sessions/](sessions/)

---

## Current State

- **Production:** [version, deployment status]
- **Blockers:** [if any]

## What's Next — Prioritized

**High:**
1. [ ] [Task with context]
2. [ ] [Task with context]

**Medium:**
3. [ ] [Task]

**Deferred:**
4. [ ] [Task — reason for deferral]

## Recent Session: YYYY-MM-DD

[Summary: what was done, decisions made, outcomes]

---

## Completed Milestones

- [Milestone 1]
- [Milestone 2]

---

*This file follows the Tiered Context Architecture. Budget: ≤150 lines.*
```

**Template — completed project:**

```markdown
# [Project Name] — Context

**Status:** Completed
**Completed:** YYYY-MM-DD
**Outcome:** [1-2 sentence summary]

---

## Summary

[What was built/accomplished, 5-10 lines]

## Key Decisions

[Notable decisions that might matter if revisited, 5-10 lines]

## Related Projects

[Links to successor or related projects]

---

*Completed project. For historical sessions, see [sessions/](sessions/).*
```

### REFERENCE.md — semantic memory

**Purpose:** Stable facts that don't change session-to-session.

**Budget:** ≤300 lines (soft). Exceeding 300 lines is a signal that the project scope may be too broad.

**Contains:**
- Project overview and goals (if not obvious from name)
- Team roster with roles
- URLs, repos, remotes, deployment configuration
- Architecture decisions and conventions
- File indexes (transcript logs, artifact locations)
- Setup and cleanup instructions

**Key property:** Updated IN PLACE, not appended to. When a team member leaves, update the roster — don't add a dated note. When a URL changes, change the URL. This is a living reference document, not a log.

**Template:**

```markdown
# [Project Name] — Reference

Stable facts for this project. Updated in place when facts change.

---

## Quick Reference

| Resource | Location |
|----------|----------|
| [Key URL] | [value] |
| [Key command] | [value] |

## Team

| Name | Role | Notes |
|------|------|-------|
| [Name] | [Role] | [Status] |

## Architecture

[Key decisions, conventions, patterns]

## Related Files

[Index of transcripts, artifacts, external documents]
```

### sessions/ — episodic archive

**Purpose:** Historical record of what happened and when. Rarely read, but searchable when historical context is needed.

**Organization:** Monthly files named `YYYY-MM.md`.

**Template:**

```markdown
# Session Archive — [Month] [Year]

Archived from CONTEXT.md on YYYY-MM-DD. See REFERENCE.md for stable project facts.

---

### YYYY-MM-DD: [Session title — what was accomplished]

[Summary: 5-15 lines per session. What was done, decisions made, outcomes.]

### YYYY-MM-DD: [Next session]

[...]
```

## The archival protocol

### When to archive

Archive when ANY of these conditions are true:
- CONTEXT.md exceeds 120 lines (approaching 150-line budget)
- Session logs in CONTEXT.md are older than 1 week
- A project phase transition occurs
- The user explicitly requests cleanup

### Step by step

1. **Read** CONTEXT.md and count lines
2. **Identify cold content:**
   - Completed task items
   - Session summaries older than 1 week
   - Stable facts that belong in REFERENCE.md
   - Detailed narratives that belong in sessions/
3. **Create files if needed:**
   - REFERENCE.md (if stable facts exist and no REFERENCE.md yet)
   - sessions/ directory
   - sessions/YYYY-MM.md for the relevant month
4. **Archive FIRST** (two-phase commit — write to destination before removing from source):
   - Session logs → sessions/YYYY-MM.md (append chronologically)
   - Stable facts → REFERENCE.md (organize by category)
   - Completed tasks → sessions/YYYY-MM.md (summarize, then remove from CONTEXT.md)
5. **Verify archives exist** — Confirm moved content is present in its destination file
6. **Only then rewrite CONTEXT.md** with archived content removed
7. **Verify:**
   - CONTEXT.md ≤150 lines
   - No information lost (everything archived before removal)
   - Cross-references updated (CONTEXT.md points to REFERENCE.md and sessions/)
8. **Commit** with message: "Maintain context: archive sessions, extract reference facts"

### Decision tree: where does this content belong?

```
Is this information needed for TODAY's work?
├── Yes → CONTEXT.md
└── No
    ├── Is it a stable fact (team, URL, architecture)?
    │   ├── Yes → REFERENCE.md (update in place)
    │   └── No
    │       ├── Is it a record of what happened during a session?
    │       │   ├── Yes → sessions/YYYY-MM.md
    │       │   └── No
    │       │       └── Is it a reusable lesson?
    │       │           ├── Yes → _lessons/
    │       │           └── No → delete it
    └── Exception: completed milestones (≤10 lines) stay in CONTEXT.md
```

## Migration guide

### For projects over 500 lines

Full restructuring. Do NOT mechanically split — each project needs judgment about what's working memory vs reference vs archive.

1. Read the entire CONTEXT.md
2. Identify the four content types
3. Create REFERENCE.md with semantic content
4. Create sessions/ with episodic content (grouped by month)
5. Rewrite CONTEXT.md as fresh working memory
6. Verify nothing was lost

### For projects 150-500 lines

Moderate restructuring:
1. Extract obvious semantic content (team, URLs, architecture) → REFERENCE.md
2. Move session logs → sessions/
3. Tighten CONTEXT.md to ≤150 lines

### For projects under 150 lines

Lightweight touch:
1. Add budget footer
2. If >20 lines of reference material exist, consider extracting to REFERENCE.md
3. If completed, simplify to completion summary format

## Project status transitions

| Transition | CONTEXT.md Action | Other Actions |
|-----------|-------------------|---------------|
| active → completed | Rewrite as completion summary (≤80 lines) | Simplify REFERENCE.md |
| active → paused | Add "Paused State" header with reason | Archive session logs |
| paused → active | Remove "Paused State" header, refresh | Update last_session |
| completed → archived | Freeze all files | Set status in index.yaml |
| active → spawned | Remove spawned scope | Create new project |

## Project spawning

When a sub-scope exceeds the parent project's boundaries:

1. Create new project directory
2. Seed CONTEXT.md with fresh working memory (not a copy)
3. Add to index.yaml with `related:` linking to parent
4. Remove spawned scope from parent's CONTEXT.md
5. Cross-reference both projects

**The test:** Would a new team member reading only the parent's CONTEXT.md be confused by the spawned work? If yes, spawn it.

## Measuring context quality

### Quantitative

| Metric | Target |
|--------|--------|
| CONTEXT.md line count | ≤150 (active) / ≤80 (completed) |
| REFERENCE.md line count | ≤300 |
| Stale session logs (>1 week old in CONTEXT.md) | 0 |
| Completed tasks remaining in CONTEXT.md | 0 |
| Budget footer present | Yes |

### Qualitative

After reading CONTEXT.md, the AI collaborator should be able to answer:
1. What is the current state of this project?
2. What should I work on next?
3. What was done in the last session?
4. Where do I find stable reference information?

If any question can't be answered from CONTEXT.md alone (with a pointer to REFERENCE.md), the working memory is incomplete.

## Comparison with flat memory files

Many AI tools use a single flat memory file (append-only, no lifecycle). The tiered architecture differs in fundamental ways:

| Dimension | Flat memory file | Tiered context architecture |
|-----------|-----------------|----------------------------|
| **Structure** | Single file | Three tiers by information lifecycle |
| **Growth** | Unbounded append | Budgeted working memory, overflow to archive |
| **Lifecycle** | None — facts accumulate forever | Garbage collection at session boundaries |
| **Staleness** | High — no removal mechanism | Low — working memory refreshed, reference updated in place |
| **Cognitive load** | Grows linearly with project duration | Constant (150-line cap on always-loaded content) |
| **Scale** | Degrades with project count and duration | Designed for 60+ projects over months |
| **Self-documenting** | No — file could contain anything | Yes — file names declare purpose |

The fundamental difference: a flat file is a **log** (append-only, no lifecycle). The tiered architecture is a **managed system** (budgets, garbage collection, lifecycle transitions). The same distinction that separates `console.log()` debugging from structured logging with rotation and retention policies.

## When to close vs. archive sessions

**Close and start a new project** when:
- The project's **goal** has changed (not just the tasks)
- The team or stakeholder set has fundamentally shifted
- There's a natural "shipped it" milestone marking a real boundary
- REFERENCE.md itself would need a complete rewrite

**Archive sessions and continue** when:
- The work is ongoing but CONTEXT.md is too long
- You're in a new phase of the same project
- Team, reference facts, and goals are substantially the same

## Context as infrastructure

In traditional engineering, code is managed as infrastructure — version control, CI/CD, testing, deployment. In synthesis engineering (human-AI collaborative development), there is a third infrastructure layer: **context infrastructure** — the structured information that enables an AI collaborator to be effective across sessions.

The three infrastructure layers:

1. **Code infrastructure** — git, CI/CD, deployment (solved by traditional engineering)
2. **Knowledge infrastructure** — lessons, runbooks, compiled knowledge bases (the organizational learning layer)
3. **Context infrastructure** — working memory, reference facts, session history (the novel contribution — no equivalent in traditional engineering because human engineers carry context in their heads)

The quality of context has three dimensions:
- **Relevance** — Is the information needed for today's work?
- **Accuracy** — Is the information still true?
- **Accessibility** — Can historical context be found when needed?

The tiered architecture optimizes all three: CONTEXT.md is relevant (lean, current), REFERENCE.md is accurate (updated in place), and sessions/ is accessible (organized, searchable).

## Evolution stages

1. **Ad hoc** — Re-explain everything each session (most AI users today)
2. **Monolithic** — Single context file that grows forever (common early approach)
3. **Tiered** — Working memory + reference + archive with lifecycle management (this runbook)
4. **Compiled** — Context automatically assembled from project state, code, and history (future vision)

Stage 3 is the 80/20 solution that makes long-running AI-assisted projects sustainable. Stage 4 is the long-term vision where context at session start is compiled from live project state rather than manually maintained.
