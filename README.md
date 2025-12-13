# ai-knowledge-ragbot

AI knowledge base for the Ragbot open source project.

## Structure

```
ai-knowledge-ragbot/
├── source/           # Human-edited content
│   ├── instructions/ # WHO - Identity & persona
│   ├── runbooks/     # HOW - Procedures
│   ├── datasets/     # WHAT - Knowledge & facts
│   ├── contexts/     # Context definitions
│   └── project-plans/
├── compiled/         # AI-optimized output (generated)
└── compile-config.yaml
```

## Framework

This repository uses the AI Knowledge framework, part of [synthesis coding](https://synthesiscoding.com/) practices within the broader synthesis engineering methodology. The framework organizes knowledge into:

- **instructions/** - WHO: Identity and persona
- **runbooks/** - HOW: Procedures and workflows
- **datasets/** - WHAT: Facts and reference information

This structure is also used in the open source [Ragbot](https://github.com/personalpant/ragbot) and [RaGenie](https://github.com/personalpant/ragenie) projects, and works with Claude, ChatGPT, Cursor, and other AI platforms.

## Related Repositories

- [ragbot](https://github.com/personalpant/ragbot) - Application code
- [ragbot-site](https://github.com/personalpant/ragbot-site) - Website (ragbot.ai)
- [ai-knowledge-personal](https://github.com/personalpant/ai-knowledge-personal) - Parent workspace
