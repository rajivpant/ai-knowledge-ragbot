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

## Inheritance

This workspace inherits from `ai-knowledge-personal` (personal context).

## Related Repositories

- [ragbot](https://github.com/personalpant/ragbot) - Application code
- [ragbot-site](https://github.com/personalpant/ragbot-site) - Website (ragbot.ai)
- [ai-knowledge-personal](https://github.com/personalpant/ai-knowledge-personal) - Parent workspace
