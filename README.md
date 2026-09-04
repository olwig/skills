# skills

My personal Copilot skills, use them if you like.

## Included skills

- `grok-build-version`: reads the published Grok Build CLI channel version.
- `grok-build-commit`: finds the best-effort GitHub commit for a Grok Build version.

## Use with `gh skill`

`gh skill` support is currently in preview.

```bash
# See available commands
gh skill --help

# Preview a skill from this repository
gh skill preview olwig/skills grok-build-version

# Install a skill from this repository
gh skill install olwig/skills grok-build-version

# List installed skills
gh skill list

# Update installed skills
gh skill update --all
```

## Use in Copilot Chat

After installation, invoke the skills in chat with:

- `/grok-build-version`
- `/grok-build-commit`
