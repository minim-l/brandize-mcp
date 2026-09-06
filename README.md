# Brandize MCP server

Hosted MCP server for [Brandize](https://brandize.me), an AI logo generator.
It generates logos, brand color palettes, SEO meta tags, and JSON-LD schema
markup, and searches the Brandize design guides.

There is nothing to install. The server is remote and runs over streamable HTTP:

| | |
| --- | --- |
| Endpoint | `https://brandize.me/api/mcp` |
| Transport | streamable-HTTP |
| Auth | none |
| Discovery manifest | <https://brandize.me/.well-known/mcp> |
| Docs | <https://brandize.me/mcp> |
| Machine-readable site docs | <https://brandize.me/llms.txt> |

This repository is documentation only — the server itself is part of the
Brandize application. Issues and questions about the MCP surface belong here.

## Connect

**Claude Code**

```bash
claude mcp add --transport http brandize https://brandize.me/api/mcp
```

**Clients that take a JSON config** (Claude Desktop, Cursor, Windsurf)

```json
{
  "mcpServers": {
    "brandize": {
      "type": "http",
      "url": "https://brandize.me/api/mcp"
    }
  }
}
```

**MCP Inspector**

```bash
npx @modelcontextprotocol/inspector
# transport: Streamable HTTP → https://brandize.me/api/mcp
```

Call `echo` first to confirm the endpoint is reachable.

## Tools

Free, no account, rate-limited:

| Tool | What it does |
| --- | --- |
| `generate_color_palette` | Five-color brand palette from a base hex color and a harmony rule. Returns CSS custom properties, a Tailwind snippet, or plain hex. |
| `generate_meta_tags` | An HTML `<head>` block — title, description, canonical, robots, Open Graph, Twitter Card. |
| `generate_schema_markup` | A JSON-LD `<script>` block for LocalBusiness, Organization, WebSite, Article, FAQPage, or Product. |
| `search_blog` | Keyword search over the Brandize logo design and branding guides. |
| `get_pricing_tiers` | Current service tiers, prices, and included features. Read-only. |
| `generate_logo_variations` | Alternate style directions for an existing logo job, within that job's free preview credits. |
| `echo` | Health check. Returns the message unchanged. |

Paid — each mints a Stripe checkout link the user opens and pays in a browser.
No card is stored and no call charges anyone on its own:

| Tool | What it does |
| --- | --- |
| `generate_logo` | Generates a watermarked, low-resolution preview from a design brief, plus a checkout link priced at the Brandize service tiers (from $4.99). |
| `buy_logo_variation` | Buys one of the alternates from `generate_logo_variations` instead of the original design. |
| `get_logo_result` | Polls a job by its `jobToken`. Once payment settles, returns a download URL for the full-resolution, watermark-free deliverable (PNG, vector SVG, commercial license). |

`generate_logo` asks the calling agent to collect a real brief — tier, style,
colors, layout, surface, industry — and to take the user's explicit acceptance of
the [Terms](https://brandize.me/legal/terms) and
[Privacy Policy](https://brandize.me/legal/privacy) before it will generate.

### A paid run, end to end

1. `generate_logo` → watermarked preview, a `jobToken`, a `checkoutUrl`, and the
   higher tiers on offer.
2. The user opens `checkoutUrl` and pays.
3. `get_logo_result` with that `jobToken`, polled every ~5 seconds — payment
   confirmation is not instant — until the status is `paid`.
4. Download the returned asset URL.

A refunded purchase reverts the job: `get_logo_result` reports
`pending_payment` again and the asset URL stops resolving.

## Registry listing

The official MCP Registry entry is `me.brandize/brandize`, published from
`server.json` in the Brandize application repo and verified against the
`brandize.me` domain.
