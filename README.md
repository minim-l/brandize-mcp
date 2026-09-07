# Brandize MCP server

Hosted MCP server for [Brandize](https://brandize.me), an AI logo generator.
It generates logos and full brand kits — lockups, favicons, social assets,
print collateral — plus brand color palettes, SEO meta tags, and JSON-LD schema
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

This repository holds the documentation and the Claude Code and Codex plugin
([`plugins/brandize`](./plugins/brandize)) — the server itself is part of the
Brandize application. Issues and questions about the MCP surface belong here.

## Connect

**Claude Code — plugin**

```shell
/plugin marketplace add minim-l/brandize-mcp
/plugin install brandize@brandize
```

**Codex — plugin**

```shell
codex plugin marketplace add minim-l/brandize-mcp
codex plugin add brandize@brandize
```

Or install `brandize` from `/plugins` inside Codex.

**Claude Code — CLI**

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
| `generate_logo` | Generates a watermarked, low-resolution preview from a design brief, plus a checkout link for the tier the user picked. |
| `buy_logo_variation` | Buys one of the alternates from `generate_logo_variations` instead of the original design. |
| `get_logo_result` | Polls a job by its `jobToken`. Once payment settles, returns a download URL for the tier's full deliverable. |

`generate_logo` asks the calling agent to collect a real brief — tier, style,
colors, layout, surface, industry — and to take the user's explicit acceptance of
the [Terms](https://brandize.me/legal/terms) and
[Privacy Policy](https://brandize.me/legal/privacy) before it will generate.

### Tiers

The `tier` argument to `generate_logo` picks what gets delivered. Call
`get_pricing_tiers` for the current prices and feature lists — they are set
server-side, not hardcoded here.

| Tier | Deliverable |
| --- | --- |
| `PREMIUM` | Full-resolution PNG, vector SVG, commercial license. |
| `COMPLETE` | Adds alternate logo variations, assembled after payment. |
| `BRAND_STARTER` | A brand kit: icon, horizontal, stacked, monochrome and knockout lockups, the full favicon package, a five-color palette, a font pairing, a print-ready business card PDF, and an HTML email signature. |
| `BRAND_KIT` | Everything in Brand Starter, plus a social media kit across five platforms, a brand guidelines PDF, a letterhead template, an Open Graph image, and iOS/Android/PWA app icons. |

The brand-kit tiers have fixed deliverables — `generate_logo_variations` refuses
on a purchased one. Only `COMPLETE` accepts more alternates after payment.

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
