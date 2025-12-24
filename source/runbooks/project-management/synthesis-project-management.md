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
    │   ├── CONTEXT.md           # Living state for active projects
    │   ├── README.md            # Static documentation (sufficient for completed)
    │   ├── work-logs/           # Session logs for this project
    │   │   └── YYYY-MM-DD-*.md
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
| **Work-logs inside projects** | Session logs belong with their project. No centralized work-logs folder. |
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

### 2. CONTEXT.md (Living Project State)

**Required for:** Active, paused, and ongoing projects.
**Optional for:** Completed and archived projects (README.md is sufficient).

The most critical file. Contains everything needed to resume work.

```markdown
# Project: {Project Name}

## Overview

{1-2 sentence description of the project goal}

**Started:** YYYY-MM-DD
**Status:** {In Progress | Blocked | Complete}

## Current State

{What has been accomplished. Be specific about artifacts created.}

**Key artifacts:**
- `path/to/file1` - Description
- `path/to/file2` - Description

## Next Steps

1. [ ] First thing to do
2. [ ] Second thing to do
3. [ ] Third thing to do

## Blockers

{Any blockers preventing progress. Remove section if none.}

- Blocker 1: Description and what's needed to unblock

## Key Decisions

| Decision | Rationale | Date |
|----------|-----------|------|
| Chose approach A over B | Reason for choice | YYYY-MM-DD |

## Session History

| Date | Summary |
|------|---------|
| YYYY-MM-DD | What was accomplished in this session |
```

**Update when:** After EVERY significant task or phase completion. This is the most important update.

### 3. Work Logs (`{project-id}/work-logs/`)

Detailed session records for significant work sessions. Live inside project folders.

**File naming:** `YYYY-MM-DD-brief-summary.md`

```markdown
# Work Log: YYYY-MM-DD — {Brief Summary}

**Project:** {Project Name}
**Duration:** ~{X} hours
**Outcome:** {success | partial | blocked}

## Accomplished

- Completed {task 1}
- Fixed {issue}
- Created {artifact}

## Challenges

- {Challenge faced and how it was resolved}

## Next Session

- Continue with {next task}

## Artifacts Created

- `path/to/new/file` - Description
```

**Update when:** End of significant sessions (not every small task).

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
2. **Search _lessons/** — `grep` for relevant past experiences
3. **Check related projects** — Look at `related:` tags in index.yaml

### Session End

1. **Final CONTEXT.md update** — Ensure all sections current
2. **Update index.yaml** — Set `last_session` date
3. **Create work log** — If significant session, add to `{project-id}/work-logs/`
4. **Commit all changes** — Don't leave uncommitted work

## File Requirements by Project Status

| Status | CONTEXT.md | README.md | work-logs/ |
|--------|------------|-----------|------------|
| active | Required | Optional | As needed |
| paused | Required | Optional | As needed |
| ongoing | Required | Optional | As needed |
| completed | Optional | Sufficient | Historical |
| archived | Optional | Sufficient | Historical |

**Rationale:** Active projects need living state tracking. Completed projects are static documentation.

## AI Assistant Integration

### For Claude Code / CLAUDE.md

Add to your `~/.claude/CLAUDE.md`:

```markdown
## Synthesis Project Management

After completing ANY significant task:

1. **Update CONTEXT.md immediately** — Don't wait until session end
2. **Update index.yaml** — Set last_session date at session end
3. **Add to _lessons/** — If you made a mistake or learned something
4. **Create work-logs/ entry** — For significant sessions (in project folder)
5. **Commit to git** — At logical checkpoints

**Location:** `ai-knowledge-{workspace}/projects/`

**Where to find things:**
| What | Location |
|------|----------|
| All projects | `projects/{project-id}/` |
| Project index | `projects/index.yaml` |
| Work logs | `projects/{project-id}/work-logs/` |
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
3. **Single source of truth** — CONTEXT.md has everything needed
4. **Self-describing** — Date prefixes, front matter, naming conventions
5. **Searchable** — Agents grep, humans `ls -t`
6. **No maintenance overhead** — No indexes to update (except index.yaml for status)

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

This system evolved from a more complex structure:
- **Removed:** `active/` and `completed/` folders → status is in index.yaml
- **Removed:** `templates/` → agents examine existing and adapt
- **Removed:** `meta/patterns.md` → patterns are lessons with `type: pattern`
- **Removed:** `lessons-learned/index.md` → date prefixes enable discovery
- **Moved:** `work-logs/` → inside each project folder
- **Renamed:** `lessons-learned/` → `_lessons/` (underscore distinguishes from projects)

## See Also

- [Synthesis Project Management](https://synthesisengineering.org/articles/ai-native-project-management/) — conceptual article explaining the rationale

## License

This runbook is part of the ragbot project and is released under the MIT License.
