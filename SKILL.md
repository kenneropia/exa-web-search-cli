---
name: exa-web-search
description: "Search the web and fetch page contents using the Exa API via curl. Use when you need to search the internet, look up documentation, fetch webpage content, or find information — and your agent does not have MCP or built-in web search tools."
---

# Exa Web Search

Search the web and fetch page contents using the Exa REST API via `curl`. Mirrors exactly what the `exa-mcp-server` default tools do.

## Prerequisites

`EXA_API_KEY` must be set as an environment variable:

```bash
export EXA_API_KEY="your-api-key-here"
```

Get your API key at: https://dashboard.exa.ai/api-keys

---

## Tool 1: `web_search_exa`

Searches the web and returns highlighted snippets from results.

**Parameters:**
| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `query` | string | ✅ | — | Natural language search query. Describe the ideal page, not keywords. Supports inline `category:<type>` syntax (see below). |
| `numResults` | number | ❌ | 10 | Number of results to return (1–100) |

**Supported categories** (append to query as `category:<type>`):
`company`, `research paper`, `news`, `personal site`, `people`, `financial report`

### curl equivalent

```bash
curl -s -X POST 'https://api.exa.ai/search' \
  -H "x-api-key: $EXA_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
    "query": "your search query here",
    "type": "auto",
    "numResults": 10,
    "contents": {
      "highlights": {
        "query": "your search query here"
      }
    }
  }'
```

### With a category

Strip `category:<type>` from the query and pass it as a separate field:

```bash
curl -s -X POST 'https://api.exa.ai/search' \
  -H "x-api-key: $EXA_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
    "query": "AI startups focused on code generation",
    "type": "auto",
    "numResults": 10,
    "category": "company",
    "contents": {
      "highlights": {
        "query": "AI startups focused on code generation"
      }
    }
  }'
```

### Response format

```json
{
  "requestId": "abc123",
  "results": [
    {
      "title": "Page Title",
      "url": "https://example.com",
      "id": "https://example.com",
      "publishedDate": "2025-01-15T00:00:00.000Z",
      "author": "Author Name",
      "highlights": ["Relevant snippet 1...", "Relevant snippet 2..."],
      "highlightScores": [0.92, 0.87]
    }
  ]
}
```

---

## Tool 2: `web_fetch_exa`

Fetches full text content from one or more URLs.

**Parameters:**
| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `urls` | string[] | ✅ | — | Array of URLs to fetch content from |
| `maxCharacters` | number | ❌ | 3000 | Maximum characters to extract per page |

### curl equivalent

```bash
curl -s -X POST 'https://api.exa.ai/contents' \
  -H "x-api-key: $EXA_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
    "ids": ["https://example.com/page1", "https://example.com/page2"],
    "contents": {
      "text": {
        "maxCharacters": 3000
      }
    }
  }'
```

### Response format

```json
{
  "requestId": "abc123",
  "results": [
    {
      "title": "Page Title",
      "url": "https://example.com/page1",
      "id": "https://example.com/page1",
      "publishedDate": "2025-01-15T00:00:00.000Z",
      "author": "Author Name",
      "text": "Full page text content up to maxCharacters..."
    }
  ]
}
```

---

## Parsing results with jq

Extract titles and URLs:
```bash
curl -s ... | jq '.results[] | {title, url}'
```

Extract highlights:
```bash
curl -s ... | jq -r '.results[] | "\(.title)\n\(.url)\n\(.highlights // [] | join("\n"))\n---"'
```

Get full text of first result:
```bash
curl -s ... | jq -r '.results[0].text'
```

---

## Query tips

Exa uses semantic search. Describe the ideal page, not keywords.

| ❌ Bad | ✅ Good |
|---|---|
| `React vs Vue` | `blog post comparing React and Vue performance benchmarks` |
| `python async` | `tutorial explaining Python asyncio with practical examples` |
| `fastapi docs` | `FastAPI dependency injection documentation with examples` |
