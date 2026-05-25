# grepo

Persistent, incrementally-updated knowledge graph for token-efficient, context-aware code reviews with Claude Code.

## What it does

Parses your codebase with Tree-sitter, stores the structural graph (files, classes, functions, imports, calls) in a local SQLite database, and exposes it to AI coding assistants via MCP tools — so reviews use ~10% of the tokens a raw-file dump would cost.

## Install

```bash
# Core install — structural graph only
uv pip install grepo

# With optional extras (pick what you need):
uv pip install "grepo[embeddings]"   # local semantic search via sentence-transformers
uv pip install "grepo[communities]"  # Leiden community detection (igraph)
uv pip install "grepo[wiki]"         # markdown wiki generation
uv pip install "grepo[eval]"         # benchmark suite

# Everything
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
| `grepo build` | Full parse + graph build (parser → nodes/edges → flows → communities → FTS5) |
| `grepo update` | Incremental update (git-aware) |
| `grepo postprocess` | Re-run flows/communities/FTS on an existing graph without re-parsing |
| `grepo embed` | Compute vector embeddings (enables semantic search) — requires `grepo[embeddings]` |
| `grepo watch` | Watch filesystem and update on save |
| `grepo status` | Show graph stats |
| `grepo serve` | Run MCP server (stdio) |
| `grepo visualize` | Generate an interactive D3.js HTML view |
| `grepo wiki` | Generate a markdown wiki from communities |
| `grepo detect-changes` | Risk-scored impact analysis |
| `grepo register <path>` | Add repo to multi-repo registry |
| `grepo repos` | List registered repos |
| `grepo eval` | Run evaluation benchmarks |

## Semantic search (optional)

`grepo build` populates the **structural** layer: nodes, edges (CALLS, IMPORTS_FROM, INHERITS, CONTAINS, TESTED_BY), flows, communities, and an FTS5 keyword index. That's enough for blast-radius, untested-code, and most code-review queries — no ML dependency.

To also enable **semantic search** (find code by intent, not exact words), compute vector embeddings for every node:

```bash
grepo embed                                  # local model (default: all-MiniLM-L6-v2)
grepo embed --model sentence-transformers/all-mpnet-base-v2   # pick a different model
```

`grepo embed` is incremental — it only embeds nodes that are new or whose content has changed since the last run. Switching the model (via `--model` or `CRG_EMBEDDING_MODEL`) re-embeds everything automatically.

The same operation is also exposed as an MCP tool (`embed_graph`), so you can ask Claude Code / Cursor to run it without leaving the editor:

```
You: Please run embed_graph on this repo.
Claude: [calls the embed_graph MCP tool — embeds new/changed nodes]
```

After embeddings exist, the `search_code` MCP tool returns hybrid results (FTS5 keyword score + cosine similarity), so `search_code("user authentication")` will surface `verify_credentials()` even though the words don't match.

### Embedding providers

Pick one — default is local (offline, no API key needed):

| Provider | Setup | Dim | Notes |
|---|---|---|---|
| **Local** (default) | `pip install grepo[embeddings]` | 384 | `sentence-transformers/all-MiniLM-L6-v2`. Override with `CRG_EMBEDDING_MODEL=<HF model id>`. |
| **Google Gemini** | `pip install grepo[google-embeddings]` + `export GOOGLE_API_KEY=...` | dynamic | `gemini-embedding-001`. |
| **MiniMax** | `export MINIMAX_API_KEY=...` | 1536 | `embo-01`. |

Switching providers (or the local model) auto-invalidates and re-embeds — the embeddings table tracks `provider_model` per row.

## License

MIT — see [LICENSE](LICENSE).