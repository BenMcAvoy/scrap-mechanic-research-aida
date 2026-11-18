# Scrap Mechanic Research - AiDAStandalone Codex plugin

This repository is both the installable Codex plugin and its small marketplace catalog. It contains Scrap Mechanic reverse-engineering skills written for the `aida-standalone-mcp` server and the Rust game-process MCP manager.

The reverse-engineering workflow uses the local AiDAStandalone HTTP MCP endpoint at `http://127.0.0.1:29117/mcp`, with tools in the `mcp__aida_standalone_mcp__*` namespace.

## Install from GitHub

```powershell
codex plugin marketplace add BenMcAvoy/scrap-mechanic-research-aida
codex plugin add scrap-mechanic-research-aida@benmcavoy-aida
```

To update later:

```powershell
codex plugin marketplace upgrade benmcavoy-aida
codex plugin remove scrap-mechanic-research-aida@benmcavoy-aida
codex plugin add scrap-mechanic-research-aida@benmcavoy-aida
```

The marketplace manifest is [.agents/plugins/marketplace.json](.agents/plugins/marketplace.json). The plugin manifest is [.codex-plugin/plugin.json](.codex-plugin/plugin.json), MCP configuration is [.mcp.json](.mcp.json), and installable skills are under [skills/](skills/).

## Local validation

```powershell
python C:\Users\Ben\.codex\skills\.system\plugin-creator\scripts\validate_plugin.py .
cargo build --release --manifest-path scrap-mechanic-mcp/Cargo.toml
```
