# Compiled Output

**DO NOT EDIT FILES IN THIS FOLDER**

This folder contains auto-generated content.

## What Gets Generated

| Output | How | When |
|--------|-----|------|
| `instructions/` | Local: `ragbot compile instructions` | When instructions change (rare) |
| `all-knowledge.md` | CI/CD: GitHub Actions | Every push to source/ |

## Current Status

Knowledge concatenation runs via CI/CD (GitHub Actions). The `knowledge/` flat files
and `vectors/` have been removed (RAG indexes from source directly; nothing consumed these intermediate files).

- **Instructions:** Compile locally with `ragbot compile instructions --project {name}`
- **Knowledge:** Edit source/ files directly. all-knowledge.md is auto-generated at repo root by CI/CD.
- **RAG indexing:** Run `ragbot index --workspace {name}` (reads source directly, no intermediate files)
- **Do NOT run the full compiler** — it is being deprecated.

## Usage

- **Claude Code**: Reads source/ files directly — no compilation needed
- **Claude Projects / ChatGPT / Gemini**: Upload all-knowledge.md (one per repo in chain), copy compiled instructions
- **Ragbot.AI**: Index with `ragbot index --workspace {name}`

## Compilation Model

The output repo determines what content is included — not who runs the compiler.
See the compiler-simplification project for architecture details.
