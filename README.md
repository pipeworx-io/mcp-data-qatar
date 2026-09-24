# @pipeworx/data-qatar

Qatar government open data — population, economic indicators, trade, and SDG social statistics from Qatar's national open data portal.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1679+ live data sources.

## Tools

- `search_datasets(query?, limit?, offset?)` — find dataset_ids by keyword (population, economy, trade, labor, environment, SDG social statistics).
- `dataset_info(dataset_id)` — field schema (bilingual English/Arabic labels), record count, description for one dataset.
- `query(dataset_id, query?, where?, select?, group_by?, order_by?, limit?, offset?)` — ODSQL records/aggregations from one dataset.

## Auth

Keyless.

## Data sources

- <https://www.data.gov.qa/api/explore/v2.1/> — Qatar's national open data portal (334+ datasets at last check). Returns bilingual field labels (`label_en` / `label_ar`) on every field; keep dataset_ids and field names in their native form and surface `label_en`/`title_en` for display rather than the bare `label`/`title`, which can be Arabic-only on some fields (the native-slug trap — see `reference_pxweb_native_slugs`).

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "data-qatar": {
      "url": "https://gateway.pipeworx.io/data-qatar/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/data-qatar/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1679+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/data_qatar_search_datasets \
  -H 'Content-Type: application/json' \
  -d '{"query":"population"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/data_qatar_search_datasets`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "data-qatar": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-data-qatar"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-data-qatar
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Data Qatar data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
