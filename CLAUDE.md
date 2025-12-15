# Claude Code Context: ai-knowledge-ragbot

## Repository: ai-knowledge-ragbot (PUBLIC)

**⚠️ THIS IS A PUBLIC REPOSITORY - CONFIDENTIALITY IS CRITICAL ⚠️**

This repository contains open-source templates and runbooks that can be inherited by other ai-knowledge repos.

## Privacy Guidelines

### NEVER include in this repo

These names are CONFIDENTIAL and must NEVER appear here:

- **Client names**: example-client, example-client, example-client, example-client, example-client
- **Company names**: example-company
- **Personal name**: personal (use "personal" in generic examples)
- **GitHub orgs**: tgorg-ai
- Any workspace name from `my-projects.yaml` except "ragbot"

### Safe to use

- `ragbot` (this repo's name, public)
- Generic placeholders: `personal`, `company`, `example-company`, `example-client`, `client-a`

### Before ANY edit or commit

1. **Search for confidential names**: `grep -ri "example-client\|example-client\|example-company\|example-client\|example-client\|example-client\|personal" .`
2. **Replace with generic names** if any found
3. **Ask the user** if unsure whether a name is confidential

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
