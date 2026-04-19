# exa-web-search-cli

An [Amp](https://ampcode.com) agent skill that gives coding agents web search and page fetching via [Exa's](https://exa.ai) REST API using plain `curl`.

## Why?

Many coding agents (Letta, custom agents, self-hosted setups) **don't support MCP** (Model Context Protocol). The official [`exa-mcp-server`](https://github.com/exa-labs/exa-mcp-server) only works with MCP-compatible clients like Claude Desktop, Cursor, and VS Code.

This skill provides the same two default tools — `web_search_exa` and `web_fetch_exa` — as direct `curl` commands against the Exa REST API. No MCP required. Just `curl` and an API key.

## What's included

| Tool | Endpoint | Description |
|---|---|---|
| `web_search_exa` | `POST /search` | Semantic web search with highlighted snippets |
| `web_fetch_exa` | `POST /contents` | Fetch full text from known URLs |

The curl commands produce **identical requests** to what the MCP server sends internally.

## Installation

### Via git (global, all projects)

```bash
git clone https://github.com/kenneropia/exa-web-search-cli.git ~/.config/agents/skills/exa-web-search
```

### Via git (project-specific)

```bash
git clone https://github.com/kenneropia/exa-web-search-cli.git .agents/skills/exa-web-search
```

### Manual

Copy `SKILL.md` into either:
- `~/.config/agents/skills/exa-web-search/SKILL.md` (global)
- `.agents/skills/exa-web-search/SKILL.md` (project)

## Setup

1. Get your API key at https://dashboard.exa.ai/api-keys
2. Add to your shell profile (`~/.zshrc` or `~/.bashrc`):

```bash
export EXA_API_KEY="your-api-key-here"
```

3. Restart your terminal or run `source ~/.zshrc`

## Quick test

```bash
curl -s -X POST 'https://api.exa.ai/search' \
  -H "x-api-key: $EXA_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
    "query": "hello world",
    "type": "auto",
    "numResults": 1,
    "contents": { "highlights": { "query": "hello world" } }
  }' | jq '.results[] | {title, url}'
```

## License

MIT
