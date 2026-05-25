# Grepo (VS Code)

Visualize code dependencies, blast radius, and review context from your grepo database directly in VS Code.

Reads from `.grepo/graph.db` produced by the [grepo CLI](../README.md).

## Development

```bash
npm install
npm run compile        # one-off build with esbuild
npm run watch          # rebuild on change
npm run lint           # tsc --noEmit
npm run package        # produce a .vsix
```

## Commands

All commands are namespaced under `Grepo:` in the command palette. See `package.json` → `contributes.commands` for the full list.