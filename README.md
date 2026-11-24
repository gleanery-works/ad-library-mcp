# Ad library data for AI agents (MCP): Google Ads Transparency Center + LinkedIn Ad Library

One MCP tool that lets Claude, ChatGPT, Cursor or any MCP-compatible agent answer **"what ads is this company running?"**. It pulls every ad a company runs on **Google** (Search, YouTube, Display, Maps, Play, Shopping) and **LinkedIn**, with ad copy, creatives, first and last shown dates, impression ranges, impressions by country, targeting and payer, returned as clean JSON. The tool is served through [Apify's hosted MCP server](https://docs.apify.com/integrations/mcp), and you pay per ad.

| Tool | Use it when the user asks... | Returns | Price |
|---|---|---|---|
| [`gleanery_actors/ad-library-scraper`](https://apify.com/gleanery_actors/ad-library-scraper) | "What ads is HubSpot running in Germany?", "Compare Pipedrive's and Salesforce's LinkedIn ads", "Show current LinkedIn ads about cybersecurity webinars" | One item per ad from the Google Ads Transparency Center and the LinkedIn Ad Library, details included | $0.001 / ad |

## Connect

**Server URL (streamable HTTP):**

```
https://mcp.apify.com?tools=gleanery_actors/ad-library-scraper
```

Authentication: OAuth (your client opens an Apify login once), or the header `Authorization: Bearer <APIFY_TOKEN>`. A free Apify account includes $5 of monthly credit, which covers about 5,000 ads. **Agents without an account** can buy a prepaid, spend-capped token over x402 or MPP at [agi.apify.com](https://agi.apify.com) and use it as the Bearer token.

### Claude Code

```bash
claude mcp add --transport http ad-library "https://mcp.apify.com?tools=gleanery_actors/ad-library-scraper"
```

### Claude Desktop / claude.ai

Settings → Connectors → **Add custom connector** → paste the server URL above.

### Cursor (`~/.cursor/mcp.json`), Windsurf, VS Code

```json
{
  "mcpServers": {
    "ad-library": {
      "url": "https://mcp.apify.com?tools=gleanery_actors/ad-library-scraper"
    }
  }
}
```

### Local (stdio) with a token

```json
{
  "mcpServers": {
    "ad-library": {
      "command": "npx",
      "args": ["-y", "@apify/actors-mcp-server@latest", "--tools", "gleanery_actors/ad-library-scraper"],
      "env": { "APIFY_TOKEN": "<your token>" }
    }
  }
}
```

## Example prompts

- *"What ads has Notion run on Google and LinkedIn in the last 30 days? Summarize the main messages."*
- *"Compare the LinkedIn ads of HubSpot, Pipedrive and Salesforce in Germany: formats, offers and reach."*
- *"Find LinkedIn ads mentioning 'AI agents' shown in France, Germany or the Netherlands, and list the advertisers."*
- *"Which of Shopify's Google ads reached the most people in the EU? Show impressions by country."*
- *"Build a swipe file of 50 high-reach B2B SaaS ads with their copy and images."*

## Tool reference

```json
{"advertisers": ["hubspot.com", "Pipedrive"], "platforms": ["google", "linkedin"],
 "countries": ["DE"], "activeWithinDays": 30, "maxAdsPerAdvertiser": 30,
 "adTextMaxLength": 300, "omitEmptyFields": true, "maxItems": 60}
```

- `advertisers`: company names or website domains. On Google, a domain returns every ad pointing to that website; a name is matched to verified advertisers.
- `keywords`: LinkedIn ad text search across all advertisers (Google has no keyword search).
- `countries`: ISO codes. EU-served ads disclose the most (impressions by country, targeting, payer) under the Digital Services Act.
- `activeWithinDays`: only ads shown recently, i.e. current campaigns.
- `includeDetails` (default true): dates, impressions, targeting. Set false for a fast list.
- Output per ad: `platform`, `advertiserName`, `format`, `headline`, `adText`, `imageUrl`, `videoUrl`, `firstShown`, `lastShown`, `impressionsMin`, `impressionsMax`, `impressionsByCountry`, `targeting`, `paidBy`, `adUrl`

## Tips for agents

- Always set `maxItems`. It caps both cost and response size.
- Prefer a website domain (`"notion.so"`) for Google: it finds the most ads.
- To save context tokens: `omitEmptyFields: true` and `adTextMaxLength: 300`.
- Google publishes text ads as rendered images, so Google ads have `imageUrl` but usually no `adText`.
- For weekly competitor monitoring, set `onlyNewOrUpdated: true` with a `stateKey` to get and pay for only new or changed ads.

## Data & compliance

The tool reads ad libraries that Google and LinkedIn publish by law (EU Digital Services Act) and by policy. It returns ads and advertiser (company) information only. Personal data, such as the employee profile behind a LinkedIn "thought leader" ad, is deliberately left out.

## Related

- [Job market & EU supplier data MCP](https://github.com/gleanery-works/job-and-supplier-data-mcp): job search across 10,000+ company career sites, Wellfound startup jobs, European suppliers.

## Support

Open an issue in this repository, or on the actor's **Issues** tab on Apify.
