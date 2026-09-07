# Brandize plugin for Claude Code

Connects Claude Code to the hosted Brandize MCP server at
`https://brandize.me/api/mcp`.

```shell
/plugin marketplace add muddi900/brandize-mcp
/plugin install brandize@brandize
```

That is the whole plugin: a remote MCP server declaration in
[`.mcp.json`](./.mcp.json). Nothing runs locally, and there is no key to paste —
the free tools are anonymous and rate-limited.

After installing, `/mcp` lists the server and its tools. Call `echo` to confirm
the connection.

The tool reference, tiers, and the end-to-end paid run live in the
[repository README](../../README.md). Prices come from `get_pricing_tiers` at
call time, not from this repo.

`version` tracks the MCP surface version the server advertises at
`/.well-known/mcp`, so a plugin update means the tool surface moved.
