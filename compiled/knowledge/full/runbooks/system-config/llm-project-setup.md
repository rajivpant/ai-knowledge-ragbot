# LLM Project Setup Guide

How to configure Claude Projects, ChatGPT GPTs, and Gemini Gems using compiled AI Knowledge content.

## Overview

The AI Knowledge Compiler produces platform-specific outputs optimized for each LLM's project/custom instruction system. This guide explains how to set up projects correctly.

## Compilation output structure

After running `ragbot compile --project {name}`, the `compiled/` folder contains:

```
compiled/
├── claude-projects/
│   ├── instructions/{name}.md      # Custom instructions for Claude
│   └── knowledge/knowledge.md      # Bundled knowledge file
├── chatgpt-projects/
│   ├── instructions/{name}.md      # System prompt for GPT
│   └── knowledge/knowledge.md      # Bundled knowledge file
├── gemini-projects/
│   ├── instructions/{name}.md      # Instructions for Gem
│   └── knowledge/knowledge.md      # Bundled knowledge file
├── knowledge/
│   ├── full/                       # Individual files for GitHub sync
│   │   ├── instructions/
│   │   ├── runbooks/
│   │   └── datasets/
│   └── by-context/                 # Context-filtered bundles
│       ├── writing-mode/
│       └── coding-mode/
├── vectors/
│   ├── chunks.jsonl               # For Qdrant/vector stores
│   └── chunks/                    # Individual chunk files
└── manifest.yaml                  # Compilation metadata
```

## Claude Projects setup

### Custom instructions

1. Create a new Claude Project (or open existing)
2. Go to Project Knowledge → Custom Instructions
3. Copy content from `compiled/claude-projects/instructions/{name}.md`
4. Paste into the custom instructions field

### Knowledge files

**Option A: GitHub sync (recommended)**
1. Connect your GitHub account to Claude
2. Sync the repository containing your ai-knowledge-{name} repo
3. Point to `compiled/knowledge/full/` directory
4. Claude will index individual files automatically

**Option B: Manual upload**
1. Go to Project Knowledge → Files
2. Upload `compiled/claude-projects/knowledge/knowledge.md`
3. Upload any additional files from `compiled/knowledge/full/` as needed

### What to include

For most projects, include:
- Custom instructions (always)
- Knowledge files from `full/` directory

For context-specific projects (writing-focused, coding-focused):
- Custom instructions
- Context-filtered bundle from `by-context/{context-name}/`

### Token budget considerations

Claude Projects have a context limit. The compiler tracks token counts in `manifest.yaml`. If over budget:
1. Use context filtering to reduce content
2. Exclude large datasets
3. Rely on instructions + runbooks, move datasets to RAG

## ChatGPT setup

### Creating a GPT

1. Go to https://chat.openai.com/gpts/editor
2. Click "Create a GPT"
3. Configure the GPT:
   - **Name**: Your project name
   - **Description**: Brief description
   - **Instructions**: Copy from `compiled/chatgpt-projects/instructions/{name}.md`

### Knowledge files

1. In GPT editor, go to Knowledge section
2. Upload files from `compiled/chatgpt-projects/knowledge/`
3. ChatGPT supports multiple file uploads

### Limitations

- GPTs have smaller context windows than Claude
- The compiler optimizes ChatGPT instructions for brevity
- Large knowledge bases may need trimming

## Gemini Gems setup

### Creating a Gem

1. Go to https://gemini.google.com/gems
2. Create new Gem
3. Paste instructions from `compiled/gemini-projects/instructions/{name}.md`

### Knowledge files

1. Upload files from `compiled/gemini-projects/knowledge/`
2. Gemini limits: max 10 files
3. The compiler bundles content to stay within limits

### Gemini-specific optimizations

- Instructions formatted for Gemini's style preferences
- Knowledge bundled to minimize file count
- Token counts optimized for Gemini's context window

## Inheritance and personalized compilation

### Baseline vs personalized

- **Baseline**: Compile a repo standalone (`ragbot compile --project example-client`)
- **Personalized**: Include inherited content (`ragbot compile --project example-client --personalized`)

Personalized compilation:
1. Resolves inheritance chain (example-client → example-company → personal → ragbot)
2. Merges content from all ancestors
3. Outputs to `compiled/projects/{project}/` for the requesting user

### When to use each

| Use Case | Approach |
|----------|----------|
| Shared team project | Baseline compilation |
| Personal Claude project | Personalized compilation |
| Client workspace | Baseline (client-specific content only) |
| Advisory work with personal identity | Personalized |

## Context-filtered outputs

### Available contexts

Contexts are defined in `source/contexts/` as YAML files:

```yaml
# writing-mode.yaml
name: Writing Mode
include:
  instructions:
    - identity.md
    - voice-and-style.md
  runbooks:
    - writing/**
    - content-creation/**
exclude:
  - runbooks/coding/**
token_budget: 80000
```

### Using context outputs

For a writing-focused Claude project:
1. Use standard instructions
2. Upload content from `compiled/knowledge/by-context/writing-mode/`
3. This excludes coding runbooks, engineering content

## Updating projects

### When to recompile

Recompile when:
- Source content changes
- Inheritance relationships change
- New contexts added
- Token budget adjustments needed

### Update workflow

```bash
# Recompile single project
ragbot compile --project {name}

# Recompile all projects
ragbot compile --all

# Force recompile (ignore cache)
ragbot compile --project {name} --force
```

### After recompilation

1. Update custom instructions in LLM project
2. Re-upload knowledge files (or trigger GitHub sync)
3. Verify token counts in manifest.yaml

## Troubleshooting

### "Instructions too long"

- Use context filtering to reduce scope
- Move detailed content to knowledge files
- Check manifest.yaml for token counts

### "Knowledge not being used"

- Verify files uploaded correctly
- Check if content is in instructions vs knowledge
- For Claude: ensure GitHub sync is active

### "Inheritance not working"

- Verify `my-projects.yaml` exists in personal repo
- Check inheritance chain: `inherits_from` in compile-config.yaml
- Run with `--verbose` to see inheritance resolution

### "Stale content"

- Use `--force` flag to ignore cache
- Check compile timestamps in manifest.yaml
- Verify source files are saved before compiling
