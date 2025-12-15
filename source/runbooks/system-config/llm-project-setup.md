# LLM Project Setup Guide

How to configure Claude Projects, ChatGPT GPTs, and Gemini Gems using compiled AI Knowledge content.

## Overview

The AI Knowledge Compiler produces platform-specific outputs optimized for each LLM's project/custom instruction system. This guide explains how to set up projects correctly.

## Compilation output structure

After running `ragbot compile --project {name}`, the `compiled/` folder contains:

```
compiled/
└── {project}/                    # One folder per compiled project
    ├── instructions/             # LLM-specific custom instructions
    │   ├── claude.md
    │   ├── chatgpt.md
    │   └── gemini.md
    ├── knowledge/                # Individual knowledge files
    │   ├── runbooks-*.md
    │   └── datasets-*.md
    ├── all-knowledge.md          # Consolidated (same level as knowledge/)
    └── vectors/                  # For RAG systems
        └── chunks.jsonl
```

**Key principle:** All compilations live at `compiled/{project}/` - same structure whether baseline or personalized.

**Knowledge file strategy:**
- Claude: GitHub sync `knowledge/` folder (all-knowledge.md is NOT inside, so won't be synced)
- ChatGPT: Upload individual files from `knowledge/`
- LLMs with file limits (Gemini, etc.): Upload `all-knowledge.md` (at project level)

## Claude Projects setup

### Custom instructions

1. Create a new Claude Project (or open existing)
2. Go to Project Knowledge → Custom Instructions
3. Copy content from `compiled/{project}/instructions/claude.md`
4. Paste into the custom instructions field

### Knowledge files

**Option A: GitHub sync (recommended)**
1. Connect your GitHub account to Claude
2. Sync the repository containing your ai-knowledge repo
3. Point to `compiled/{project}/knowledge/` directory
4. Claude will index individual files automatically

**Option B: Manual upload**
1. Go to Project Knowledge → Files
2. Upload all files from `compiled/{project}/knowledge/`
3. Claude supports multiple file uploads

### Token budget considerations

Claude Projects have a context limit. The compiler tracks token counts in `manifest.yaml`. If over budget:
1. Exclude large datasets
2. Rely on instructions + key runbooks
3. Move verbose content to RAG (Ragbot)

## ChatGPT setup

### Creating a GPT

1. Go to https://chat.openai.com/gpts/editor
2. Click "Create a GPT"
3. Configure the GPT:
   - **Name**: Your project name
   - **Description**: Brief description
   - **Instructions**: Copy from `compiled/{project}/instructions/chatgpt.md`

### Knowledge files

1. In GPT editor, go to Knowledge section
2. Upload files from `compiled/{project}/knowledge/`
3. ChatGPT supports multiple file uploads

### Limitations

- GPTs have smaller context windows than Claude
- The compiler optimizes ChatGPT instructions for brevity
- Large knowledge bases may need trimming

## Gemini Gems setup

### Creating a Gem

1. Go to https://gemini.google.com/gems
2. Create new Gem
3. Paste instructions from `compiled/{project}/instructions/gemini.md`

### Knowledge files

**Recommended: Use consolidated file**
1. Upload `compiled/{project}/all-knowledge.md`
2. This single file contains all runbooks and datasets merged together

**Alternative: Select individual files**
1. Gemini has a **10-file limit** per Gem
2. If you prefer granularity, manually select up to 10 important files from `knowledge/`
3. Prioritize: instructions > key runbooks > datasets

### Gemini-specific considerations

- Instructions formatted for Gemini's style preferences
- The compiler generates `all-knowledge.md` for LLMs with file count limits
- Token counts optimized for Gemini's context window

## Other LLMs (Grok, etc.)

For LLMs with file count limits:
1. Use `all-knowledge.md` (consolidated) if the LLM has file count restrictions
2. Use individual files if the LLM supports many files

For LLMs with large file size limits:
1. Upload individual files from `knowledge/`
2. The compiler generates flat files with category prefixes (e.g., `runbooks-code-generation.md`)

## Compilation and inheritance

### How it works

Each user compiles projects in their own repo. What content gets included depends on inheritance.

**Example: Compiling in ai-knowledge-personal:**
```
compiled/
├── personal/                     # Baseline (ragbot + personal)
├── company/                      # personal + company merged
├── client-a/                     # personal + company + client-a merged
└── client-b/                     # personal + client-b merged
```

**Example: Compiling in ai-knowledge-company (team member without access to personal):**
```
compiled/
├── company/                      # Baseline (ragbot + company, NO personal)
├── client-a/                     # company + client-a (NO personal)
└── client-c/                     # company + client-c
```

### Privacy model

Content is only included if the user has access to the source repo:
- Your private content (ai-knowledge-{personal}) only appears in YOUR compilations
- Team members get team content but not your personal content
- Clients only get client-specific content

### Running compilation

```bash
# Compile specific project
ragbot compile --project {name}

# Compile all projects you have access to
ragbot compile --all

# Force recompile (ignore cache)
ragbot compile --project {name} --force

# Verbose output (see inheritance resolution)
ragbot compile --project {name} --verbose
```

## Setting up your LLM projects

### Step-by-step workflow

1. **Run compilation** in your personal ai-knowledge repo
   ```bash
   cd ~/projects/my-projects/ai-knowledge/ai-knowledge-{personal}
   ragbot compile --all
   ```

2. **For each project** (e.g., client-a):
   - **Claude**:
     - Create project "Client A"
     - Copy `compiled/client-a/instructions/claude.md` to custom instructions
     - GitHub sync `compiled/client-a/knowledge/`
   - **ChatGPT**:
     - Create GPT "Client A"
     - Copy `compiled/client-a/instructions/chatgpt.md` to instructions
     - Upload files from `compiled/client-a/knowledge/`
   - **Gemini**:
     - Create Gem "Client A"
     - Copy `compiled/client-a/instructions/gemini.md` to instructions
     - Upload `compiled/client-a/all-knowledge.md`

3. **Verify** by testing each project with a representative query

## Updating projects

### When to recompile

Recompile when:
- Source content changes
- Inheritance relationships change
- New projects added

### Update workflow

```bash
# Recompile changed project
ragbot compile --project {name} --force

# Re-sync with LLM
# - Claude: GitHub sync will auto-update
# - ChatGPT: Re-upload knowledge files
# - Gemini: Re-upload knowledge files
```

## Troubleshooting

### "Instructions too long"

- Move detailed content to knowledge files
- Check manifest.yaml for token counts
- Keep instructions focused on identity and behavior

### "Knowledge not being used"

- Verify files uploaded correctly
- Check if content is in instructions vs knowledge
- For Claude: ensure GitHub sync is active and pointing to correct folder

### "Inheritance not working"

- Verify `my-projects.yaml` exists in personal repo
- Check inheritance chain in compile-config.yaml
- Run with `--verbose` to see inheritance resolution

### "Content from wrong repo appearing"

- Check which repo you're compiling in
- Verify you have access to expected repos
- Remember: content only comes from repos you can access
