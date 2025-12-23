# Synthesis Project Management System

A lightweight project management system designed for AI-assisted development workflows. Optimized for context preservation across conversation sessions and context compaction events.

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
    ├── active/                    # Current projects
    │   ├── index.yaml             # Project index with metadata
    │   └── {project-name}/
    │       └── CONTEXT.md         # Project state file
    │
    ├── completed/                 # Archived projects
    │   ├── index.yaml             # Completed projects index
    │   └── {project-name}/
    │       └── CONTEXT.md
    │
    ├── work-logs/                 # Session history
    │   ├── YYYY-MM-DD-summary.md  # Individual session logs
    │   └── summaries/
    │       ├── weekly/
    │       ├── monthly/
    │       └── quarterly/
    │
    ├── lessons-learned/           # Cross-project insights
    │   └── YYYY-MM-DD-topic.md
    │
    ├── meta/                      # System-level documentation
    │   └── patterns.md            # Recurring patterns
    │
    └── templates/                 # Reusable templates
```

## Components

### 1. Project Index (`active/index.yaml`)

Lists all active projects with metadata for discovery.

```yaml
# Active Projects Index
# Last updated: YYYY-MM-DD

projects:
  - id: my-project
    name: My Project Name
    description: Brief description of what this project accomplishes
    tags:
      - tag1
      - tag2
    context: my-project/CONTEXT.md
    last_session: YYYY-MM-DD

  - id: another-project
    name: Another Project
    description: Another description
    related:
      - my-project  # Link related projects
    context: another-project/CONTEXT.md
    last_session: YYYY-MM-DD
```

**Update when:** Session end, project status changes, new project added.

### 2. CONTEXT.md (Per-Project State)

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
- Blocker 2: Description

## Key Decisions

| Decision | Rationale | Date |
|----------|-----------|------|
| Chose approach A over B | Reason for choice | YYYY-MM-DD |
| Used library X | Reason for choice | YYYY-MM-DD |

## Related

- [Related Project](../related-project/CONTEXT.md)
- [External doc](https://...)

## Session History

| Date | Summary |
|------|---------|
| YYYY-MM-DD | What was accomplished in this session |
| YYYY-MM-DD | What was accomplished in this session |
```

**Update when:** After EVERY significant task or phase completion. This is the most important update.

### 3. Work Logs (`work-logs/`)

Detailed session records for significant work sessions.

```markdown
# Work Log: YYYY-MM-DD

**Project:** {Project Name}
**Duration:** ~{X} hours
**Focus:** {Brief description}

## Accomplished

- Completed {task 1}
- Fixed {issue}
- Created {artifact}

## Challenges

- {Challenge faced and how it was resolved}

## Next Session

- Continue with {next task}
- Need to {follow-up item}

## Artifacts Created

- `path/to/new/file` - Description
```

**Update when:** End of significant sessions (not every small task).

### 4. Lessons Learned (`lessons-learned/`)

Mistakes and insights that apply across projects.

```markdown
# {Topic}: {Brief Title}

**Date:** YYYY-MM-DD
**Project:** {Where this was learned}
**Severity:** {Minor | Moderate | Serious}

## What Happened

{Factual description of the situation}

## Root Cause

{Why this happened}

## Impact

{What harm or inefficiency resulted}

## Lesson

{The takeaway that applies to future work}

## Prevention

{Specific steps to prevent recurrence}

- [ ] Add to checklist
- [ ] Update process
- [ ] Add automation
```

**Update when:** Immediately when you learn something reusable.

### 5. Patterns (`meta/patterns.md`)

Recurring patterns extracted from work.

```markdown
# Patterns

## {Pattern Category}

### {Pattern Name}

**Context:** When this pattern applies

**Pattern:** Description of what to do

**Example:**
{Code or process example}

**Anti-pattern:** What NOT to do
```

**Update when:** When you notice something recurring.

### 6. Completed Projects (`completed/`)

Archived projects, moved from `active/` when done.

**Update when:** Project reaches completion. Move entire folder, update both index files.

## The Protocol

### During Work

```
Complete task → Update CONTEXT.md → Update other components as needed → Commit → Next task
```

**NOT:**
```
Complete task → Complete task → Complete task → (context compaction) → Lost details
```

### Session Start

1. **Read CONTEXT.md** - Understand current state
2. **Check lessons-learned/** - Relevant past experiences for this type of work
3. **Check related projects** - Look at `related:` tags

### Session End

1. **Final CONTEXT.md update** - Ensure all sections current
2. **Update index.yaml** - Set `last_session` date
3. **Create work log** - If significant session
4. **Commit all changes** - Don't leave uncommitted work

## AI Assistant Integration

### For Claude Code / CLAUDE.md

Add to your `~/.claude/CLAUDE.md`:

```markdown
## Synthesis Project Management

After completing ANY significant task:

1. **Update CONTEXT.md immediately** - Don't wait until session end
2. **Update index.yaml** - Set last_session date at session end
3. **Add to lessons-learned/** - If you made a mistake or learned something
4. **Add to meta/patterns.md** - If you noticed a recurring pattern
5. **Create work-logs/** entry - For significant sessions
6. **Commit to git** - At logical checkpoints

**Location:** `ai-knowledge-{workspace}/projects/`

The user should NEVER have to remind you to do this.
```

### Project Discovery

When a user mentions a project:

1. Read `projects/active/index.yaml`
2. Match user's phrase against project `name`, `description`, `id`, `tags`
3. If match found, read the project's `CONTEXT.md`
4. Summarize current state and next steps
5. Begin work from where it left off

## Why This Works

1. **Filesystem is persistent** - Survives context compaction
2. **Structured format** - Easy to parse and update
3. **Single source of truth** - CONTEXT.md has everything needed
4. **Searchable history** - Work logs and lessons compound over time
5. **Project discovery** - index.yaml enables semantic matching

## Common Mistakes

| Mistake | Consequence | Prevention |
|---------|-------------|------------|
| Not updating CONTEXT.md | Lost progress after compaction | Update after EVERY task |
| Deferring updates to "session end" | Forget to update | Update immediately |
| Putting management files in project repos | Exposes internal process | Keep in ai-knowledge-{workspace} |
| Not checking lessons-learned | Repeat mistakes | Check at session start |
| Incomplete CONTEXT.md updates | Missing artifacts, wrong next steps | Use the template checklist |

## License

This runbook is part of the ragbot project and is released under the MIT License.
