# RankParse API

SEO data API for developers and AI agents, powered by pre-processed [Common Crawl](https://commoncrawl.org/) data updated quarterly. 20 endpoints covering backlink graphs, domain authority, real-time page audits, and cross-domain intelligence — billed per call, no subscription, no monthly minimum.

- **Docs:** https://rankparse.com/docs
- **Dashboard:** https://rankparse.com/dashboard
- **Pricing:** https://rankparse.com/pricing
- **MCP server:** https://rankparse.com/docs/mcp

---

## Authentication

All endpoints require an `X-API-Key` header with a key in `rp_...` format. Get a key at [rankparse.com/dashboard](https://rankparse.com/dashboard).

On RapidAPI, authentication is handled by the gateway — include your `X-RapidAPI-Key` as usual.

---

## Quick Start

```bash
curl "https://api.rankparse.com/v1/domain-authority?domain=example.com" \
  -H "X-API-Key: rp_your_key_here"
```

```json
{
  "domain": "example.com",
  "data": {
    "score": 72,
    "referring_domains": 1103,
    "total_host_count": 4821,
    "registered_at": "1995-08-14",
    "registrar": "IANA",
    "popularity_rank": 3142,
    "health_score": 85,
    "risk_flags": []
  },
  "credits_used": 1,
  "credits_remaining": 9999
}
```

All responses use the same envelope: `domain` (or `url`), `data`, `credits_used`, `credits_remaining`. Paginated endpoints also include `total`, `limit`, and `offset`.

---

## Endpoints

### Link Graph

| Endpoint | Description | Credits |
|---|---|---|
| `GET /v1/backlinks` | Backlinks pointing to a domain, sorted by authority or recency | 2 |
| `GET /v1/referring-domains` | Domains linking to a target with dofollow/nofollow link counts | 2 |
| `GET /v1/outbound-links` | External links going out from a domain | 2 |
| `GET /v1/anchor-text` | Anchor text distribution across all inbound links | 2 |
| `GET /v1/top-pages` | Pages on the domain ranked by number of inbound links | 2 |

### Domain Metrics

| Endpoint | Description | Credits |
|---|---|---|
| `GET /v1/domain-authority` | 0–100 authority score, RDAP registration data, Tranco popularity rank | 1 |
| `GET /v1/domain-rank` | Inbound edge count, unique linking domains, avg linking host count | 2 |
| `GET /v1/crawl-history` | First/last seen dates and total URL count from Common Crawl index | 2 |

### Real-Time Page Analysis

These endpoints fetch the target URL live at request time.

| Endpoint | Description | Credits |
|---|---|---|
| `GET /v1/page-seo` | Full SEO audit: meta tags, Open Graph, JSON-LD, hreflang, headings, image alt coverage, internal/external links, word count | 3 |
| `GET /v1/site-health` | HTTPS enforcement, HSTS, security headers, robots.txt, sitemap availability | 2 |
| `GET /v1/sitemap` | Discover and parse a domain's sitemap — returns URLs with lastmod, changefreq, priority | 2 |
| `GET /v1/tech-stack` | Server software, CMS, and framework signals from HTTP headers and meta tags | 2 |
| `GET /v1/page-performance` | Google PageSpeed Insights — Lighthouse scores and Core Web Vitals (LCP, CLS, TBT, FCP) | 3 |

### Cross-Domain Intelligence

| Endpoint | Description | Credits |
|---|---|---|
| `GET /v1/domain-overlap` | Domains linking to 2–5 of your specified targets | 5 |
| `GET /v1/link-intersect` | Domains linking to both `domain_a` and `domain_b` | 5 |
| `GET /v1/competitor-gap` | Domains linking to a competitor but not to you | 5 |
| `GET /v1/similar-domains` | Domains sharing link sources — topical proximity signal | 5 |

### Power Endpoints

| Endpoint | Description | Credits |
|---|---|---|
| `GET /v1/link-audit` | Composite link quality score, anchor profile breakdown, risk flags, outbound concentration | 8 |
| `GET /v1/site-explorer` | Backlinks + authority + top pages + anchor text in a single call | 10 |
| `POST /v1/batch` | Backlinks for up to 50 domains in one request | 2 per domain |

---

## Common Parameters

| Parameter | Type | Required | Notes |
|---|---|---|---|
| `domain` | string | Yes (domain endpoints) | Bare domain only: `example.com`, not `https://example.com` |
| `url` | string | Yes (URL endpoints) | Full URL including scheme: `https://example.com/page` |
| `limit` | integer | No | Default 100, max 1000 (silently clamped) |
| `sort` | string | No | `importance` (default) or `recent` — backlinks endpoint only |
| `score` | boolean | No | Adds `from_domain_score`, `anchor_type`, `link_quality` to each row (+1 credit) — backlinks and referring-domains |

Domains with no data return `200` with `"data": []` — never `404`.

---

## Example Responses

### GET /v1/backlinks

```bash
curl "https://api.rankparse.com/v1/backlinks?domain=example.com&limit=2&sort=importance" \
  -H "X-API-Key: rp_your_key_here"
```

```json
{
  "domain": "example.com",
  "data": [
    {
      "from_domain": "ycombinator.com",
      "from_url": "https://news.ycombinator.com/item?id=39201847",
      "to_url": "https://example.com/blog/post",
      "anchor_text": "example.com",
      "rel": null,
      "link_type": "hyperlink",
      "crawled_at": "2026-01-14"
    },
    {
      "from_domain": "dev.to",
      "from_url": "https://dev.to/user/article",
      "to_url": "https://example.com/",
      "anchor_text": "check this tool",
      "rel": "nofollow",
      "link_type": "hyperlink",
      "crawled_at": "2026-02-03"
    }
  ],
  "credits_used": 2,
  "credits_remaining": 9998
}
```

### GET /v1/referring-domains

```bash
curl "https://api.rankparse.com/v1/referring-domains?domain=example.com&limit=2" \
  -H "X-API-Key: rp_your_key_here"
```

```json
{
  "domain": "example.com",
  "data": [
    {
      "from_domain": "ycombinator.com",
      "dofollow_links": 14,
      "nofollow_links": 2,
      "total_links": 16
    },
    {
      "from_domain": "dev.to",
      "dofollow_links": 0,
      "nofollow_links": 8,
      "total_links": 8
    }
  ],
  "credits_used": 2,
  "credits_remaining": 9996
}
```

### GET /v1/domain-authority

```bash
curl "https://api.rankparse.com/v1/domain-authority?domain=example.com" \
  -H "X-API-Key: rp_your_key_here"
```

```json
{
  "domain": "example.com",
  "data": {
    "score": 72,
    "referring_domains": 1103,
    "total_host_count": 4821,
    "registered_at": "1995-08-14",
    "registrar": "IANA",
    "popularity_rank": 3142,
    "health_score": 85,
    "risk_flags": []
  },
  "credits_used": 1,
  "credits_remaining": 9995
}
```

`score` is 0–100. `popularity_rank` is from the Tranco top-1M list — `null` if outside the top 100k. `risk_flags` values: `new_domain`, `low_authority`, `no_tranco_presence`, `high_authority_new_domain`.

### GET /v1/page-seo

```bash
curl "https://api.rankparse.com/v1/page-seo?url=https://example.com" \
  -H "X-API-Key: rp_your_key_here"
```

```json
{
  "url": "https://example.com",
  "data": {
    "url": "https://example.com",
    "final_url": "https://example.com/",
    "status_code": 200,
    "response_time_ms": 312,
    "title": { "text": "Example Domain", "length": 14, "optimal": false },
    "description": { "text": null, "length": 0, "optimal": false },
    "canonical": { "url": "https://example.com/", "self_referential": true },
    "robots": { "index": true, "follow": true, "raw": null },
    "viewport": "width=device-width, initial-scale=1",
    "language": { "primary": "en", "tag": "en-US", "source": "html_lang" },
    "hreflang": [],
    "headings": { "h1": ["Example Domain"], "h2": [], "h3_count": 0 },
    "og": {},
    "twitter": {},
    "json_ld": [],
    "images": { "total": 0, "missing_alt": 0 },
    "links": { "internal": 0, "external": 1 },
    "word_count": 28,
    "favicon": null
  },
  "credits_used": 3,
  "credits_remaining": 9992
}
```

`title.optimal` is `true` when length is 30–60 characters. `description.optimal` is `true` when 120–160 characters. Returns `503` with `code: fetch_failed` if the target URL is unreachable.

### GET /v1/site-health

```bash
curl "https://api.rankparse.com/v1/site-health?domain=example.com" \
  -H "X-API-Key: rp_your_key_here"
```

```json
{
  "domain": "example.com",
  "data": {
    "domain": "example.com",
    "https": { "enforced": true, "hsts": true, "hsts_max_age": 31536000 },
    "www_redirect": { "enabled": false, "target": null },
    "checks": [
      { "url": "https://example.com/", "status": 200, "content_type": "text/html", "response_time_ms": 180 },
      { "url": "https://example.com/robots.txt", "status": 200, "content_type": "text/plain", "response_time_ms": 95 },
      { "url": "https://example.com/sitemap.xml", "status": 404, "content_type": null, "response_time_ms": 88 }
    ],
    "security_headers": {
      "x_frame_options": "SAMEORIGIN",
      "x_content_type_options": "nosniff",
      "content_security_policy": false,
      "strict_transport_security": "max-age=31536000",
      "referrer_policy": null
    },
    "robots_txt": { "present": true, "disallow_all": false, "sitemap_declared": false }
  },
  "credits_used": 2,
  "credits_remaining": 9990
}
```

---

## Error Codes

All errors return JSON with `error`, `code`, and `message` fields.

| HTTP Status | Code | Meaning |
|---|---|---|
| 400 | `invalid_domain` | `domain` parameter missing or malformed |
| 400 | `invalid_url` | `url` parameter missing or invalid |
| 400 | `private_ip` | URL resolves to a private/reserved IP (page-seo only) |
| 401 | `unauthorized` | Missing or invalid API key |
| 402 | `insufficient_credits` | Credit balance too low |
| 429 | `rate_limited` | 60 requests/minute per API key exceeded |
| 429 | `quota_exceeded` | Global daily PageSpeed quota reached (page-performance only) |
| 429 | `user_quota_exceeded` | Per-user daily cap reached — max 50/day (page-performance only) |
| 503 | `fetch_failed` | Real-time fetch of target URL failed or timed out |
| 503 | `psi_unavailable` | PageSpeed Insights API unavailable |

---

## Pricing

Credits are the billing unit. Starting at **$0.009 per credit**. Credits never expire. No subscription, no monthly minimum.

Purchase at [rankparse.com/pricing](https://rankparse.com/pricing).

---

## MCP Server

All endpoints are also available as MCP tools via `mcp.rankparse.com`. This lets Claude, ChatGPT, and other MCP-compatible clients call RankParse mid-conversation without custom code.

**SSE (Claude, ChatGPT):**
```
https://mcp.rankparse.com/?apiKey=rp_your_key_here
```

**Streamable HTTP (Claude Code, Cursor, Windsurf):**
```
https://mcp.rankparse.com/mcp
Header: X-API-Key: rp_your_key_here
```

Setup guide: [rankparse.com/docs/mcp](https://rankparse.com/docs/mcp)

---

## Files in This Repository

| File | Description |
|---|---|
| `openapi.yaml` | OpenAPI 3.0 spec — import into RapidAPI, Postman, or any client generator |
| `rankparse.postman_collection.json` | Postman collection with all endpoints pre-configured |
| `server.json` | MCP registry entry (streamable HTTP transport) |
