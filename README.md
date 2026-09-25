# mcp-seoul-data

Seoul Open Data MCP — city data for Seoul via the Seoul Open Data Plaza API

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1679+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `seoul_real_estate` | Seoul property/apartment SALE transactions (실거래가) from the official registry. PREFER for "apartment prices in <Seoul district>", "recent Seoul real-estate sales", "what did <building> sell for". Optionally filter by year and district code. Returns building name, neighborhood (dong), sale price (KRW), floor area (m²), floor, and contract date. Common district codes: |
| `seoul_air_quality` | Real-time air quality across Seoul districts (자치구) — PM10, PM2.5, ozone, NO2, CO, SO2, the comprehensive air index (CAI), and its grade (좋음/Good … 나쁨/Bad). Use for "air quality in Seoul / <district>", "Seoul PM2.5 right now". |
| `seoul_events` | Seoul cultural events — exhibitions, concerts, festivals, performances — with title, category, district, date, venue, target audience, and fee. Use for "cultural events in Seoul", "what's on in <Seoul district>". |
| `seoul_dataset` | Generic passthrough to ANY Seoul Open Data Plaza service by its service name (서비스명) — returns the raw rows. Use when a specific Seoul dataset is needed that the named tools don't cover (e.g. "CardSubwayStatsNew" subway ridership, "GetParkingInfo" parking, "tbCycleStationInfo" bike stations, "SPOP_LOCAL_RESD_DONG" population). Browse services at data.seoul.go.kr. |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "seoul-data": {
      "url": "https://gateway.pipeworx.io/seoul-data/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/seoul-data/mcp` returns the tools in the table
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
curl -X POST https://gateway.pipeworx.io/v1/tools/seoul_real_estate \
  -H 'Content-Type: application/json' \
  -d '{"year":2026,"district_code":"11680","limit":10}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/seoul_real_estate`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "seoul-data": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-seoul-data"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-seoul-data
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Seoul Data data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
