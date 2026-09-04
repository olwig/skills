# skills

My personal GitHub-hosted agent skills, use them if you like.

They are not limited to Copilot. Any chat/agent client that supports GitHub Skills and slash-command invocation can use them.

## Included skills

- `grok-build-version`: fetches the published Grok Build CLI/harness channel pointer (`stable`, `alpha`, or `enterprise`) from xAI install endpoints, with `install.sh` inspection as a fallback when pointer resolution breaks. This is the shipped binary version, not a GitHub tag.
- `grok-build-commit`: maps a Grok Build version number (for example `1.0.13`) to the oldest commit where `crates/codegen/xai-grok-version/Cargo.toml` changes to that version. Later commits that still contain the same version are not valid pins for that version bump. This helps because Grok Build upstream is internal at x.ai and the public GitHub mirror does not provide release tags or an official channel-to-commit mapping.
- `pastefix`: cleans pasted text while preserving language and meaning, with default spelling/grammar cleanup plus lowercase output and `formal`, `funny`, `short`, and `help` modes.

## Install and manage with `gh skill`

`gh skill` support is currently in preview.

```bash
# See available commands
gh skill --help

# Search skills on GitHub
gh skill search grok-build

# Preview a skill from this repository
gh skill preview olwig/skills grok-build-version

# Install skills from this repository
gh skill install olwig/skills grok-build-version
gh skill install olwig/skills grok-build-commit
gh skill install olwig/skills pastefix

# List installed skills
gh skill list

# Update installed skills
gh skill update --all
```

## Agent/chat install locations

Install location depends on your target client. Use `--agent` and `--scope` so `gh skill` writes into the correct folder for that chat/agent.

```bash
# Project-scoped install for Claude Code
gh skill install olwig/skills grok-build-version --agent claude-code --scope project

# User-scoped install for Cursor
gh skill install olwig/skills grok-build-version --agent cursor --scope user
```

## Other tool compatibility

`gh skill` is the CLI in this workflow that installs and updates GitHub-hosted skills. It can target many clients via `--agent` (for example `github-copilot`, `claude-code`, `cursor`, `codex`, `gemini-cli`, `grok`, `opencode`, `warp`).

GitLab CLI (`glab`) does not provide an equivalent `skill` install/update workflow for GitHub-hosted skills, so use `gh skill` for management and then invoke from your chosen client.

## Use from chats/agents

After installation, invoke the skills in your compatible chat/agent client with:

- `/grok-build-version`
- `/grok-build-commit`
- `/pastefix`

Then follow your client’s slash-command flow and tool output panel for results.
