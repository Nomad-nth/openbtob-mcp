# OpenBtoB MCP server

Public home for the OpenBtoB MCP server listing. OpenBtoB gives an AI agent direct access to trade data: HS code lookup, buyers and suppliers, company profiles, sanctions screening and duty by trade route. Shipment answers cite their records and the date the data runs through.

The server is hosted. This repository holds the listing metadata (`server.json`) and the connect instructions; it contains no server code.

- Endpoint: `https://mcp.openbtob.com/mcp` (MCP, Streamable HTTP)
- Docs: https://openbtob.com/mcp

## Tools

The server has 10 tools.

| Tool | What it does |
| --- | --- |
| `lookup_hs` | Look up HS codes by product description (any language) or code; add `from` and `to` countries to get the duty on that trade route |
| `search_buyers` | Find companies importing a product |
| `search_suppliers` | Find companies exporting a product |
| `get_company` | Company profile: overview, top products, partners, ports, recent shipments |
| `get_tariff_exposure` | Duty layers for a company's top US import products |
| `screen_buyer` | Sanctions check against the OFAC SDN, EU, UK and UN lists (a list match, not a model guess), plus trust and risk signals |
| `watch`, `unwatch`, `get_watchlist` | Keep a watchlist of companies, HS codes and tariff lines |
| `export_data` | Export search results as a CSV file (counts against your export quota) |

Duty figures are estimates, not legal advice: verify them with a licensed customs broker.

## Access

Every tool needs an API key. A free account works: it gets 100 tool calls a day across all tools, HS code lookup included. The day resets at 00:00 UTC.

Today the server authenticates with an API key (`opb_...`) sent as a bearer token. Create a key at https://openbtob.com/settings. Magic-link sign-in (OAuth) is planned and is not live yet; this page will change when it is.

Never commit an API key.

## Connect

Replace `YOUR_API_KEY` with your key.

### Claude Code

```sh
claude mcp add --transport http openbtob https://mcp.openbtob.com/mcp --header "Authorization: Bearer YOUR_API_KEY"
```

### Claude Desktop

Add this to `claude_desktop_config.json`, then restart Claude Desktop. It needs Node.js.

```json
{
  "mcpServers": {
    "openbtob": {
      "command": "npx",
      "args": ["-y", "mcp-remote@latest", "https://mcp.openbtob.com/mcp", "--header", "Authorization:${AUTH_HEADER}"],
      "env": { "AUTH_HEADER": "Bearer YOUR_API_KEY" }
    }
  }
}
```

### Cursor

Add this to `~/.cursor/mcp.json`, then reload Cursor.

```json
{
  "mcpServers": {
    "openbtob": {
      "url": "https://mcp.openbtob.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

To keep the key out of a file you commit (such as a project's `.cursor/mcp.json`), write the value as `"Bearer ${env:OPENBTOB_API_KEY}"` and set `OPENBTOB_API_KEY` in your environment.

## License

MIT, see [LICENSE](LICENSE). The licence covers the files in this repository, not the hosted service or its data.
