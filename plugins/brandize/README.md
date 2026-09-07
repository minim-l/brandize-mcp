# Brandize plugin

Connects Claude Code or Codex to the hosted Brandize MCP server at
`https://brandize.me/api/mcp`.

**Claude Code**

```shell
/plugin marketplace add muddi900/brandize-mcp
/plugin install brandize@brandize
```

**Codex**

```shell
codex plugin marketplace add muddi900/brandize-mcp
```

Then install `brandize` from `/plugins`.

That is the whole plugin: a remote MCP server declaration. Nothing runs locally,
and there is no key to paste — the free tools are anonymous and rate-limited.

After installing, `/mcp` lists the server and its tools. Call `echo` to confirm
the connection.

## Layout

Both harnesses read the same plugin directory through their own manifest:

| File | Read by |
| --- | --- |
| `.claude-plugin/plugin.json` | Claude Code |
| `.mcp.json` | Claude Code — `mcpServers`, `{"type":"http","url":…}` |
| `.codex-plugin/plugin.json` | Codex — adds the `interface` block for the install card |
| `codex.mcp.json` | Codex — `mcp_servers`, `{"url":…}` |
| `assets/icon.png` | Codex install card |

The two MCP files exist because the formats differ: Claude Code wants a
`mcpServers` object with an explicit `type`, and Codex reads either a bare server
map or a `mcp_servers` wrapper. One file can't satisfy both — a `mcpServers` key
would look like a server named `mcpServers` to Codex.

The tool reference, tiers, and the end-to-end paid run live in the
[repository README](../../README.md). Prices come from `get_pricing_tiers` at
call time, not from this repo.

`version` in both manifests tracks the MCP surface version the server advertises
at `/.well-known/mcp`, so a plugin update means the tool surface moved.
