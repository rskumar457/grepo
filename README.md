# grepo

Persistent, incrementally-updated knowledge graph for token-efficient, context-aware code reviews with Claude Code.

## What it does

Parses your codebase with Tree-sitter, stores the structural graph (files, classes, functions, imports, calls) in a local SQLite database, and exposes it to AI coding assistants via MCP tools — so reviews use ~10% of the tokens a raw-file dump would cost.

## Install

```bash
uv pip install grepo
# or, with all extras
uv pip install "grepo[all]"
```

## Quickstart

```bash
# 1. Build the graph for the current repo
grepo build

# 2. Start the MCP server (Claude Code/Cursor auto-attach via .mcp.json)
grepo serve

# 3. Incremental update on demand
grepo update
```

## CLI

| Command | What it does |
|---|---|
| `grepo build` | Full parse + graph build |
| `grepo update` | Incremental update (git-aware) |
| `grepo watch` | Watch filesystem and update on save |
| `grepo status` | Show graph stats |
| `grepo serve` | Run MCP server (stdio) |
| `grepo visualize` | Generate an interactive D3.js HTML view |
| `grepo wiki` | Generate a markdown wiki from communities |
| `grepo detect-changes` | Risk-scored impact analysis |
| `grepo register <path>` | Add repo to multi-repo registry |
| `grepo repos` | List registered repos |
| `grepo eval` | Run evaluation benchmarks |

## License

MIT — see [LICENSE](LICENSE).