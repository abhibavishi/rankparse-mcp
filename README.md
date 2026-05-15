# RankParse MCP Server

SEO data over MCP — backlinks, domain authority, tech stack, referring domains, and 18+ tools for any domain or URL.

**MCP Server URL:** `https://mcp.rankparse.com/mcp`

## Setup

### Claude Desktop / claude.ai
Add to Settings → Connectors:
```
https://mcp.rankparse.com/?apiKey=rp_your_key_here
```

### Claude Code
```bash
claude mcp add rankparse --transport http https://mcp.rankparse.com/mcp --header "X-API-Key: rp_your_key_here"
```

### Cursor / Windsurf
```json
{
  "mcpServers": {
    "rankparse": {
      "url": "https://mcp.rankparse.com/mcp",
      "headers": { "X-API-Key": "rp_your_key_here" }
    }
  }
}
```

## Available Tools

| Tool | Description | Credits |
|---|---|---|
| `get_domain_authority` | DA score, referring domains, registered date, popularity rank | 1 |
| `get_backlinks` | Inbound links with anchor text and source pages | 2 |
| `get_referring_domains` | Unique domains linking to target | 2 |
| `get_anchor_text` | Anchor text distribution | 2 |
| `get_top_pages` | Pages with most inbound links | 2 |
| `get_outbound_links` | External links from a domain | 2 |
| `get_domain_rank` | Rank metrics and scores | 2 |
| `get_site_explorer` | Full domain overview | 10 |
| `get_page_meta` | Title, description, canonical, OG tags | 2 |
| `get_tech_stack` | Detected technologies | 2 |
| `get_url_index` | Indexed URLs for a domain | 2 |
| `get_crawl_history` | Crawl timestamps | 2 |
| `get_status_codes` | HTTP status distribution | 2 |
| `get_content_types` | Content type breakdown | 2 |
| `get_language` | Language distribution | 2 |
| `get_domain_overlap` | Domains linking to multiple targets | 5 |
| `get_link_intersect` | Shared linkers between two domains | 5 |
| `get_similar_domains` | Domains similar to target | 5 |

## Pricing

100 free credits on signup. Packs from $9 for 1,000 credits. Credits never expire.

Get your API key at [rankparse.com/signup](https://rankparse.com/signup).

## Docs

Full documentation at [rankparse.com/docs/mcp](https://rankparse.com/docs/mcp).
