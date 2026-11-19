# InfraRabbit MCP server

<img src="logo-400.png" alt="InfraRabbit logo" width="96" height="96">

A free, read-only [Model Context Protocol](https://modelcontextprotocol.io) server for [InfraRabbit](https://infrarabbit.com): published list prices of compute (GPU hours, VPS plans, LLM tokens), archived daily, with history and price indices. It lets an assistant answer "what does X cost today, and when did it change" from sourced, dated data instead of from memory.

- Endpoint: `https://infrarabbit.com/mcp` (remote, streamable HTTP)
- Authentication: none
- Registry name: `com.infrarabbit/data` (see [`server.json`](server.json))
- Data: CC BY 4.0 (the code and docs in this repository are MIT, see [`LICENSE`](LICENSE)), list prices (not transacted prices), each with source URL and verification date

## Tools

| Tool | What it does |
|---|---|
| `search_skus` | Find VPS plans, GPU instances or LLM models by name/id, category and provider. |
| `get_price` | Current list price of one SKU with secondary prices, USD/EUR, spec, source URL, `verified_at` and a stale flag. |
| `get_history` | Price change points of a SKU over the last N days. |
| `list_changes` | Dated price changes, new SKUs, delistings and spec changes, newest first. |
| `changes_since` | Everything recorded since a date, for a model whose knowledge ends at a cutoff. |
| `what_changed_since` | Same idea, grouped: new models and plans, price changes, retirements. |
| `get_uptime` | Uptime and latency of about 24 public AI API hosts, measured by InfraRabbit every 5 minutes, with incidents per host. |
| `cheapest_for_spec` | Cheapest active SKUs meeting a minimum spec, ranked by list price only. |
| `vps_plans` | VPS plans as on the site and in `GET /v1/vps/plans`, same function. |
| `stack_quote` | Best current answer for a whole stack (VPS, GPU, LLM items) in one call. |
| `estimate_cost` | Itemised cost of a usage profile at a SKU's current list price. |
| `get_index` | A named daily index series with its latest value and constituents. |
| `alert_me` | Returns the URL where a person can subscribe to a free daily change email for a SKU, provider, category, index or new models. Sends nothing itself. |

The list above comes from `tools/list` on the live server; that is the source of truth.

## Connect

All clients need the same two facts: URL `https://infrarabbit.com/mcp`, transport streamable HTTP, no headers.

**Claude Code**

```bash
claude mcp add --transport http infrarabbit https://infrarabbit.com/mcp
```

**Claude Desktop / claude.ai**: add a custom connector (Settings, Connectors) with the URL `https://infrarabbit.com/mcp`. Remote servers are added through the connector UI, not `claude_desktop_config.json`.

**Cursor**: `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "infrarabbit": { "url": "https://infrarabbit.com/mcp" }
  }
}
```

**VS Code**: `.vscode/mcp.json`:

```json
{
  "servers": {
    "infrarabbit": { "type": "http", "url": "https://infrarabbit.com/mcp" }
  }
}
```

**ChatGPT / OpenAI**: ChatGPT connectors (developer mode) and the OpenAI Responses API take a remote MCP server URL; use `https://infrarabbit.com/mcp`, authentication none. Example for the API:

```json
{
  "type": "mcp",
  "server_label": "infrarabbit",
  "server_url": "https://infrarabbit.com/mcp",
  "require_approval": "never"
}
```

**Cline**: `cline_mcp_settings.json` (MCP Servers, Configure MCP Servers):

```json
{
  "mcpServers": {
    "infrarabbit": {
      "type": "streamableHttp",
      "url": "https://infrarabbit.com/mcp"
    }
  }
}
```

**Other clients**: any client that supports remote streamable HTTP servers works with the URL above. Clients that only speak stdio can bridge with `npx mcp-remote https://infrarabbit.com/mcp`.

Contact: inbox@infrarabbit.com. Smithery and other directories: the server is listed through the MCP registry entry in [`server.json`](server.json).

## Example prompts

- "What is the cheapest EU VPS with 4 GB RAM right now, and when was that price last verified?"
- "Which providers list an H100 per GPU-hour, and what changed in the last 30 days?"
- "My knowledge ends in 2025-06. What changed in LLM prices and model lineups since?"
- "Estimate monthly cost for 50M input and 10M output tokens on claude-sonnet-5-5."
- "Show the h100 index over the last 90 days."

## License

Code and docs in this repository: MIT, see [`LICENSE`](LICENSE). Data served by the server: CC BY 4.0, see [infrarabbit.com/licensing](https://infrarabbit.com/licensing).

## Fair use

Free to use, no key. Please keep request rates reasonable and cache results where you can; prices update daily. Attribution under CC BY 4.0, bulk or older history, and commercial terms: [infrarabbit.com/licensing](https://infrarabbit.com/licensing).

## Links

[infrarabbit.com](https://infrarabbit.com) · [API docs](https://infrarabbit.com/api) · [llms.txt](https://infrarabbit.com/llms.txt) · [SDKs](https://github.com/InfraRabbit/sdk) · [Data](https://github.com/InfraRabbit/data)
