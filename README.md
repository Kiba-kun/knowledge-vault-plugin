# Vault Plugin

A Claude Code plugin that scaffolds personal knowledge base vaults with a full wiki workflow.

## What it provides

One skill: **`/vault-init`**

Run it in any empty directory to scaffold a complete vault:

- `CLAUDE.md` — customized for your topic
- `raw/` — immutable source materials
- `wiki/` — synthesized knowledge (index + log stubs)
- `output-artifacts/` — consulting artifacts produced by `/wiki-output`
- `.claude/skills/` — all wiki workflow skills (project-scoped, not global)

Skills installed into each vault:

| Skill | Purpose |
|-------|---------|
| `/wiki-ingest` | Synthesize a raw source file into wiki pages |
| `/wiki-query` | Answer questions grounded in wiki content |
| `/wiki-lint` | Structural integrity check |
| `/wiki-output` | Generate consulting artifacts (brief, one-pager, playbook, comparison, slides) |
| `obsidian-markdown` | Obsidian Flavored Markdown conventions |
| `obsidian-cli` | Interact with Obsidian via CLI |
| `defuddle` | Extract clean markdown from web pages |

## Installation

Install via the Claude Code marketplace:

1. Open Claude Code → **Settings** → **Plugins**
2. Search for `knowledge-vault-plugin`
3. Click **Install**

For local development, clone the repo and load it directly:

```bash
claude --plugin-dir /path/to/vault-plugin
```

## Usage

```
mkdir my-research-vault
cd my-research-vault
/vault-init
```

Claude will ask for your vault topic and directory confirmation, then scaffold everything.

## Architecture

`/vault-init` is the only global skill this plugin installs. All other vault skills are
written as project-scoped files into `.claude/skills/` of the new vault — they only activate
inside that vault, not in your other projects.
