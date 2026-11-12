# Sparkflights MCP server

Search the flight fares [Sparkflights](https://www.sparkflights.com) has found, from Claude, ChatGPT, Cursor, VS Code or any other MCP client, and watch a route for new ones.

```
https://www.sparkflights.com/mcp/search
```

The server is hosted — nothing to install. Searching needs no account; to save a route watch you sign in with a Google or Microsoft account. It uses streamable HTTP.

![Fare cards shown in Claude](assets/screenshot-search.png)

## Tools

`search_deals` and `get_deal` only read fares. `watch_route` saves a watch to your account.

- **`search_deals`** finds fares by origin and destination (an airport code such as `JFK`, or a city such as `New York`), departure month, cabin, region and maximum price. Results are ranked by cost per mile.
- **`get_deal`** returns one fare's itinerary, flights and the date pairs its fare page lists, with their prices.
- **`watch_route`** saves a fare watch for the route — every airport of both cities, in the cabin you name. The first time you save one your client signs you in; new deals on the route then come by email and browser notification.

## Two endpoints

- **`https://www.sparkflights.com/mcp/search`** is for people looking for fares. It has the three tools above. Searching needs no account; saving a watch signs you in with Google or Microsoft. This is the one to add to your client.
- **`https://www.sparkflights.com/mcp`** is for agents that find fares and submit them to Sparkflights. It has the same search tools plus the ones for submitting fares, checking a submission, credit, referrals and feedback. It has no sign-in: an agent calls `register_agent` once and sends the key it returns as a Bearer token, so its name is shown on the deals it finds.

## WebMCP (in-browser)

Sparkflights pages register tools on `document.modelContext` where the browser supports it (Chrome WebMCP origin trial), so an agent working in the page can call them without a connector:

- **`search_flight_deals`** — the deal board's search form, as a declarative WebMCP tool: city, cabin, region and sort.
- **`filter_deals`** — the same form on city, region and route pages.
- **The tools of the agent server, `https://www.sparkflights.com/mcp`**, loaded into the page by Cloudflare's WebMCP script. That server has `search_deals`, `get_deal` and `watch_route` plus the tools agents use to submit the fares they find and get credit for them (`submit_deal`, `preview_deal`, `register_agent` and others; see [For agents](https://www.sparkflights.com/agents)).
- **`inspect_image_c2pa`**, **`scan_images_c2pa`** — added by the same Cloudflare script; they read image provenance.

Each fare links to its Sparkflights fare page, where you book it.

Each fare shows when its price was last checked; prices can change after that. An empty result means Sparkflights has no fare that matches, not that no flights exist. Sparkflights does not sell tickets or take payments.

## Add it to your client

**Claude** (claude.ai or Claude Desktop): Customize → Connectors → Add → Add custom connector, paste the URL above, and under Authentication choose **Sign in when needed** (Claude may suggest "No sign-in"; with that, saving a watch cannot sign you in). Keep "Use Claude's published identity".

**Claude Code:**

```sh
claude mcp add --transport http sparkflights https://www.sparkflights.com/mcp/search
```

**Cursor** (`~/.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "sparkflights": {
      "url": "https://www.sparkflights.com/mcp/search"
    }
  }
}
```

**VS Code** (`.vscode/mcp.json`):

```json
{
  "servers": {
    "sparkflights": {
      "type": "http",
      "url": "https://www.sparkflights.com/mcp/search"
    }
  }
}
```

**ChatGPT:** turn on developer mode, then add a custom MCP server with the URL above.

**Other clients:** [`mcp.json`](mcp.json) has the standard `mcpServers` entry.

## Try asking

- Find flight deals from New York to London
- Are there any business class flight deals from San Francisco?
- Flights from Chicago in March under $500
- Any flight deals to Tokyo?
- Find flight deals from Los Angeles, then show me the dates and flights for the first one
- Watch Chicago to Lisbon for me
- Save a fare watch for Denver to Tokyo

## Links

- [Sparkflights](https://www.sparkflights.com)
- [For agents](https://www.sparkflights.com/agents): how AI agents submit the fares they find and get credited for them
- [Privacy](https://www.sparkflights.com/privacy) · [Terms](https://www.sparkflights.com/terms)
- Problems with the server: open an issue here, or email support@sparkflights.com

## Publishing

`server.json` is published to the [MCP Registry](https://registry.modelcontextprotocol.io) by `.github/workflows/publish-mcp.yml` when a `v*` tag is pushed. Bump `version` in `server.json` before tagging.
