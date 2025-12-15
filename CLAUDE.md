# Claude Code Context: ai-knowledge-ragbot

## Repository: ai-knowledge-ragbot (PUBLIC)

**⚠️ THIS IS A PUBLIC REPOSITORY - CONFIDENTIALITY IS CRITICAL ⚠️**

This repository contains open-source templates and runbooks that can be inherited by other ai-knowledge repos.

## Privacy Guidelines

- **NEVER** include client, company, or personal workspace names
- **ONLY** use generic placeholders: `personal`, `company`, `example-company`, `example-client`, `client-a`
- The list of confidential names is in `~/.claude/CLAUDE.md` (private, not in any repo)
- When in doubt, ask the user before committing

## Repository Purpose

This repo serves as the **root** of the inheritance hierarchy:

```
ai-knowledge-ragbot (PUBLIC - templates)
    └── ai-knowledge-{personal} (private)
        └── ai-knowledge-{company} (private)
            └── ai-knowledge-{client} (private)
```

Content here is inherited by ALL downstream repos, so it must be:
- Generic (no client-specific content)
- Reusable (templates, not implementations)
- Public-safe (nothing confidential)

## Compilation Model

**Key concept:** The output repo determines what content is included—not who runs the compiler.

- Anyone with write access to a repo can compile into it
- Content included depends solely on the output repo's position in the inheritance tree
- Same output regardless of who runs the compiler

See [ragbot/docs/compilation-guide.md](https://github.com/rajivpant/ragbot/blob/main/docs/compilation-guide.md) for full documentation.

## Structure

```
ai-knowledge-ragbot/
├── source/
│   ├── instructions/     # Generic instruction templates
│   ├── runbooks/         # Reusable procedure templates
│   └── datasets/         # Reference data templates
├── compiled/             # Auto-generated output
└── projects/             # Project documentation
```
