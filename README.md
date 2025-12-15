# ai-knowledge-ragbot

Open-source AI knowledge base for the [Ragbot](https://github.com/rajivpant/ragbot) project.

This repository contains reusable runbooks, templates, and guides that ship with Ragbot. It follows the AI Knowledge framework structure, making it compatible with the AI Knowledge Compiler and inheritable by personal ai-knowledge repos.

## What's Included

### Instructions (`source/instructions/`)

Custom instruction templates for configuring AI assistant behavior.

| Template | Purpose |
|----------|---------|
| [templates/default.md](source/instructions/templates/default.md) | General-purpose AI assistant |
| [templates/creative-writer.md](source/instructions/templates/creative-writer.md) | Creative writing coach |
| [templates/technical-advisor.md](source/instructions/templates/technical-advisor.md) | Senior technical advisor |

### Runbooks (`source/runbooks/`)

Step-by-step procedures for specific tasks.

**System Configuration:**
- [anti-watermarking.md](source/runbooks/system-config/anti-watermarking.md) - Request clean text output
- [code-generation.md](source/runbooks/system-config/code-generation.md) - Structured code task approach
- [response-synthesis.md](source/runbooks/system-config/response-synthesis.md) - Combine multiple LLM responses

**Communication:**
- [message-condensation.md](source/runbooks/communication/message-condensation.md) - 5-sentence message framework

**Content Creation:**
- [hyperlink-research.md](source/runbooks/content-creation/hyperlink-research.md) - Find authoritative links
- [social-media-post.md](source/runbooks/content-creation/social-media-post.md) - Social media templates
- [blog-promotion.md](source/runbooks/content-creation/blog-promotion.md) - Blog promotion templates
- [content-promotion-framework.md](source/runbooks/content-creation/content-promotion-framework.md) - Strategic content promotion

**Content Enhancement:**
- [blog-revitalization-framework.md](source/runbooks/content-enhancement/blog-revitalization-framework.md) - Update historical posts
- [blog-revitalization-prompt.md](source/runbooks/content-enhancement/blog-revitalization-prompt.md) - Quick revitalization prompt

**Engineering:**
- [codebase-review.md](source/runbooks/engineering/codebase-review.md) - Enterprise-grade codebase review checklist

**LLM Project Setup:**
- [llm-project-setup.md](source/runbooks/system-config/llm-project-setup.md) - Configure Claude Projects, ChatGPT GPTs, and Gemini Gems

### Datasets (`source/datasets/`)

Reference information and templates.

**Templates:**
- [about-me.md](source/datasets/templates/about-me.md) - Personal information template
- [professional.md](source/datasets/templates/professional.md) - Work history template
- [preferences.md](source/datasets/templates/preferences.md) - Communication preferences template

**Guides:**
- [ai-content-quality-guide.md](source/datasets/guides/ai-content-quality-guide.md) - Identifying and improving AI-assisted content

## Structure

```
ai-knowledge-ragbot/
├── source/
│   ├── instructions/       # WHO - Custom instruction templates
│   │   └── templates/
│   ├── runbooks/           # HOW - Procedures and workflows
│   │   ├── system-config/
│   │   ├── communication/
│   │   ├── content-creation/
│   │   ├── content-enhancement/
│   │   └── engineering/
│   ├── datasets/           # WHAT - Reference information
│   │   ├── templates/
│   │   └── guides/
│   └── contexts/           # Context definitions
├── compiled/               # AI-optimized output (generated)
└── compile-config.yaml
```

## Usage

### Direct Use

Copy any file to use directly in your AI workflows:
- Use instruction templates as starting points for custom instructions
- Reference runbooks when performing specific tasks
- Fill in dataset templates with your information

### Inheritance

Personal ai-knowledge repos can inherit from this repo. Add to your `compile-config.yaml`:

```yaml
inherits_from:
  - ai-knowledge-ragbot
```

This gives you access to all public runbooks and templates, which you can extend with your own voice profiles and personal content.

### Customization

Some runbooks (like message-condensation and content-promotion-framework) require a voice profile to produce ready-to-use output without placeholders. Create a voice profile in your personal repo that extends the framework with your specific preferences.

## Framework

This repository uses the AI Knowledge framework, part of [synthesis coding](https://synthesiscoding.com/) practices. The framework organizes knowledge into:

- **instructions/** - WHO: Identity and persona configuration
- **runbooks/** - HOW: Procedures and workflows
- **datasets/** - WHAT: Facts and reference information

## Related Repositories

| Repository | Purpose |
|------------|---------|
| [ragbot](https://github.com/rajivpant/ragbot) | Ragbot application code |
| [ragbot-site](https://github.com/rajivpant/ragbot-site) | Ragbot website (ragbot.ai) |

## Contributing

Contributions welcome! When adding content:
- Ensure no personal or sensitive information is included
- Follow the existing structure (instructions/runbooks/datasets)
- Test that content works as intended
- Update README tables if adding new files

## License

MIT License - See [ragbot LICENSE](https://github.com/rajivpant/ragbot/blob/main/LICENSE.md)
