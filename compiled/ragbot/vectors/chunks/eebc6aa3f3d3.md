projects added

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
