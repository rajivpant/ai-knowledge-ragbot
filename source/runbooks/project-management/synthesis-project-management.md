# Synthesis Project Management System

A lightweight project management system designed for human-agent collaboration. Optimized for context preservation across conversation sessions and context compaction events.

## Design Principles

These principles guide all system decisions:

1. **Discoverability over documentation** — Agents can search/grep; humans need quick orientation. Prefer consistent naming conventions over maintained indexes.

2. **Convention over configuration** — Consistent structure means less cognitive load. When everything follows the same pattern, both humans and agents know where to look.

3. **Single source of truth** — No duplicate indexes to maintain. Files should be self-describing through front matter and naming conventions.

4. **Self-describing files** — Date prefixes, status in index.yaml, front matter metadata. No separate documentation that can get stale.

5. **Agents do the work** — Templates are obsolete. To create something new, examine an existing example and adapt it. Agents excel at this.

## Problem This Solves

When working with AI assistants on multi-session projects:
- **Context compaction** (conversation summarization) loses detailed progress
- **Session boundaries** create information gaps
- **Multiple projects** create confusion about current state
- **Lessons learned** get lost instead of compounding

This system provides persistent state that survives context loss.

## System Architecture

All project management lives in one location within your ai-knowledge workspace:

```
ai-knowledge-{workspace}/
└── projects/
    ├── index.yaml               # Single index for ALL projects (status field, not folders)
    │
    ├── {project-id}/            # Project folders (flat structure)
    │   ├── CONTEXT.md           # Working memory — active state (budget: ≤150 lines)
    │   ├── REFERENCE.md         # Semantic memory — stable facts (updated in place)
    │   ├── sessions/            # Episodic memory — archived session logs
    │   │   └── YYYY-MM.md       #   Monthly files
    │   ├── README.md            # Static documentation (optional)
    │   └── resources/           # Project data and artifacts (optional)
    │       ├── in/              # Inputs
    │       ├── artifacts/       # Working data
    │       ├── out/             # Outputs
    │       └── scripts/         # One-off scripts
    │
    └── _lessons/                # Cross-project lessons and patterns
        └── YYYY-MM-DD-*.md      # Date-prefixed for discoverability
```

### Key Structural Decisions

| Decision | Rationale |
|----------|-----------|
| **Flat project folders** | Status is in `index.yaml`, not folder names. No moving folders when status changes. |
| **`_lessons/` underscore prefix** | Distinguishes from project folders. Sorts to top. Visible, not hidden. |
| **Three-tier context** | CONTEXT.md (working memory), REFERENCE.md (stable facts), sessions/ (history). See `context-lifecycle.md`. |
| **Date-prefixed lesson files** | Enables time-based discovery. `ls -t` shows recent. No index needed. |
| **No templates folder** | Agents examine existing examples and adapt. Templates are a pre-AI pattern. |
| **No patterns.md** | Patterns are lessons with `type: pattern` in front matter. One folder to search. |

## Components

### 1. Project Index (`index.yaml`)

Single source of truth for all projects. Status is a field, not a folder.

```yaml
# Projects Index
# Last updated: YYYY-MM-DD

# Status values:
#   active    - Currently being worked on
#   paused    - Started but on hold
#   ongoing   - Continuous/maintenance work, no defined end state
#   completed - Has defined deliverables that are done
#   archived  - Old/obsolete, kept for reference only

projects:
  # ============================================================================
  # ACTIVE PROJECTS
  # ============================================================================

  - id: my-project
    name: My Project Name
    status: active
    description: Brief description of what this project accomplishes
    tags:
      - tag1
      - tag2
    last_session: YYYY-MM-DD

  # ============================================================================
  # COMPLETED PROJECTS
  # ============================================================================

  - id: finished-project
    name: Finished Project
    status: completed
    completed_date: YYYY-MM-DD
    description: What was accomplished
    tags:
      - tag1
    outcome: success
    key_result: Brief summary of what was delivered

  # ============================================================================
  # ARCHIVED PROJECTS
  # ============================================================================

  - id: old-project
    name: Old Project
    status: archived
    archived_date: YYYY-MM-DD
    description: Why it was archived
    superseded_by: newer-project  # If applicable
```

**Update when:** Session end (update `last_session`), project status changes, new project added.

### 2. Tiered Context Architecture

Projects use a three-tier context system that separates information by lifecycle. This prevents unbounded growth of context files and keeps AI collaborators effective across long-running projects.

**Detailed documentation:** See `context-lifecycle.md` (companion runbook) for templates, migration guides, decision trees, and quality metrics.

**The three tiers:**

| Tier | File | Purpose | Budget | Update pattern |
|------|------|---------|--------|---------------|
| Working memory | CONTEXT.md | Current state, active tasks, recent sessions | ≤150 lines (hard) | Every session |
| Semantic memory | REFERENCE.md | Stable facts (team, URLs, architecture) | ≤300 lines (soft) | Updated in place when facts change |
| Episodic memory | sessions/YYYY-MM.md | Archived session logs | No budget | Append-only, monthly files |

**CONTEXT.md** is the most critical file — loaded every session, budgeted at 150 lines. Contains ONLY what's needed for today's work. Session logs older than 1 week and stable facts are archived to sessions/ and REFERENCE.md respectively.

**REFERENCE.md** stores facts that don't change session-to-session. Updated in place (not appended to). When a team member leaves, update the roster — don't add a dated note.

**sessions/** stores chronological session history, organized by month. Rarely loaded, but searchable when historical context is needed.

**Archival protocol:** At session start, if CONTEXT.md exceeds 120 lines: move completed tasks (delete), old session logs (→ sessions/), and stable facts (→ REFERENCE.md). This is garbage collection for context.

**Update when:** After EVERY significant task. This is the most important update.

### 4. Lessons (`_lessons/`)

Cross-project mistakes, insights, and patterns. All in one folder with date prefixes.

**File naming:** `YYYY-MM-DD-topic-slug.md`

**For incidents/mistakes:**
```markdown
---
type: incident
title: Brief Title
severity: minor | moderate | serious | critical
---

# {Topic}: {Brief Title}

## What Happened
## Root Cause
## Impact
## Lesson
## Prevention
```

**For patterns (generalized insights):**
```markdown
---
type: pattern
title: Pattern Name
---

# {Pattern Name}

## Context
{When this pattern applies}

## Problem
{What problem it solves}

## Solution
{The pattern itself}

## Examples
{Where it's been applied}
```

**Update when:** Immediately when you learn something reusable.

## The Protocol

### During Work

```
Complete task → Update CONTEXT.md → Commit → Next task
```

**NOT:**
```
Complete task → Complete task → Complete task → (context compaction) → Lost details
```

### Session Start

1. **Read CONTEXT.md** — Understand current state before touching code
2. **Check line count** — If CONTEXT.md >150 lines, archive before starting work
3. **Read REFERENCE.md** — If it exists and the task needs reference details
4. **Search _lessons/** — `grep` for relevant past experiences
5. **Check related projects** — Look at `related:` tags in index.yaml

### Session End

1. **Final CONTEXT.md update** — Ensure all sections current (≤150 lines)
2. **Archive if needed** — Move old sessions to sessions/, stable facts to REFERENCE.md
3. **Update index.yaml** — Set `last_session` date
4. **Commit all changes** — Don't leave uncommitted work

## File Requirements by Project Status

| Status | CONTEXT.md | REFERENCE.md | sessions/ | CONTEXT.md budget |
|--------|------------|-------------|-----------|------------------|
| active | Required | When needed | When needed | ≤150 lines |
| paused | Required | When needed | When needed | ≤150 lines |
| ongoing | Required | When needed | When needed | ≤150 lines |
| completed | Required (summary) | Optional | Optional | ≤80 lines |
| archived | Frozen | Frozen | Frozen | N/A |

**Rationale:** Active projects need lean working memory. Completed projects need concise summaries. Reference files and session archives are created when a project accumulates enough content to warrant them.

## AI Assistant Integration

### For Claude Code / CLAUDE.md

Add to your `~/.claude/CLAUDE.md`:

```markdown
## Context Lifecycle

After completing ANY significant task:

1. **Update CONTEXT.md immediately** — Don't wait until session end (budget: ≤150 lines)
2. **Move stable facts to REFERENCE.md** — Don't put them in CONTEXT.md
3. **Archive old sessions to sessions/** — When logs are >1 week old
4. **Update index.yaml** — Set last_session date
5. **Add to _lessons/** — If you learned something reusable
6. **Commit to git** — At logical checkpoints

**Location:** `ai-knowledge-{workspace}/projects/`

**Where to find things:**
| What | Location |
|------|----------|
| All projects | `projects/{project-id}/` |
| Project index | `projects/index.yaml` |
| Working memory | `projects/{project-id}/CONTEXT.md` |
| Reference facts | `projects/{project-id}/REFERENCE.md` |
| Session history | `projects/{project-id}/sessions/` |
| Lessons | `projects/_lessons/` |

The user should NEVER have to remind you to do this.
```

### Project Discovery

When a user mentions a project:

1. Read `projects/index.yaml`
2. Match user's phrase against project `name`, `description`, `id`, `tags`
3. If match found, read the project's `CONTEXT.md`
4. Summarize current state and next steps
5. Begin work from where it left off

## Why This Works

1. **Filesystem is persistent** — Survives context compaction
2. **Convention-based** — Same structure everywhere, easy to navigate
3. **Tiered by lifecycle** — Hot data in CONTEXT.md, warm in REFERENCE.md, cold in sessions/
4. **Budgeted** — 150-line cap prevents degradation over time
5. **Self-maintaining** — Archival protocol is garbage collection for context
6. **Searchable** — Agents grep, humans `ls -t`
7. **Scales** — Tested across 60+ projects over months of continuous use

## Common Mistakes

| Mistake | Consequence | Prevention |
|---------|-------------|------------|
| Not updating CONTEXT.md | Lost progress after compaction | Update after EVERY task |
| Deferring updates to "session end" | Forget to update | Update immediately |
| Putting management files in project repos | Exposes internal process | Keep in ai-knowledge-{workspace} |
| Not checking _lessons/ | Repeat mistakes | Grep at session start |
| Creating separate patterns.md | Duplicate, gets stale | Use `type: pattern` in _lessons/ |
| Maintaining index files for lessons | Gets stale | Use date prefixes, `ls -t` |

## Evolution Notes

This system has evolved through three stages:

**Stage 1 — Flat structure (early):**
- `active/` and `completed/` folders → replaced with status field in index.yaml
- `templates/` → removed (agents examine existing and adapt)
- `meta/patterns.md` → replaced with `type: pattern` in _lessons/

**Stage 2 — Monolithic CONTEXT.md (mid):**
- Single CONTEXT.md per project
- Worked well for short projects (2-5 sessions)
- Degraded for long-running projects (CONTEXT.md grew to 500-1000+ lines)

**Stage 3 — Tiered context architecture (current, March 2026):**
- CONTEXT.md split into three tiers: working memory / reference / archive
- Budget enforcement prevents degradation
- Archival protocol provides garbage collection
- Designed for projects spanning weeks or months

## See Also

- [Context lifecycle management](context-lifecycle.md) — companion runbook with templates, decision trees, and migration guide
- [Synthesis Project Management](https://synthesisengineering.org/articles/ai-native-project-management/) — conceptual article explaining the rationale

## License

This runbook is part of the ragbot project and is released under the MIT License.
