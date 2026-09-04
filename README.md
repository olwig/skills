# skills

My personal GitHub-hosted agent skills, use them if you like.

They are not limited to Copilot. Any chat/agent client that supports GitHub Skills and slash-command invocation can use them.

## Included skills

- `grok-build-version`: fetches the published Grok Build CLI/harness channel pointer (`stable`, `alpha`, or `enterprise`) from xAI install endpoints. This is the shipped binary version, not a GitHub tag.
- `grok-build-commit`: maps a Grok Build version number (for example `1.0.13`) to the first matching commit in `xai-org/grok-build` snapshots. This helps because Grok Build upstream is internal at x.ai and the public GitHub mirror does not provide release tags or an official channel-to-commit mapping.

## Install and manage with `gh skill` (or `gh skills`)

`gh skill` support is currently in preview. `gh skills` is an alias for the same commands.

```bash
# See available commands (both forms work)
gh skill --help
gh skills --help

# Search skills on GitHub
gh skill search grok-build

# Preview a skill from this repository
gh skill preview olwig/skills grok-build-version

# Install skills from this repository
gh skill install olwig/skills grok-build-version
gh skill install olwig/skills grok-build-commit

# List installed skills
gh skill list

# Update installed skills
gh skill update --all
```

## Use from chats/agents

After installation, invoke the skills in your compatible chat/agent client with:

- `/grok-build-version`
- `/grok-build-commit`

Examples of clients where this pattern applies when Skills are enabled include Copilot Chat on GitHub, VS Code, and JetBrains.
