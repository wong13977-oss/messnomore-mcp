# MessNoMore Data Tools MCP server

Remote MCP server that gives Claude, ChatGPT, Cursor, VS Code and other MCP clients ten data tools: **UK / US H-1B /
Canada visa sponsor lookup, school term dates (UK, Australia, Malaysia), JAKIM and BPJPH halal certificate checks, HS
code classification (US HTS, EU TARIC, UK), airline baggage allowances, Shopee / Lazada / TikTok Shop listing
translation, IAB website categorization, open tech conference CFPs, mobile plan prices (Malaysia, Singapore,
Australia) and recipes from cooking videos**.

It runs on [Apify's hosted MCP server](https://docs.apify.com/mcp), limited to these tools. You sign in with a free
Apify account; each tool charges per result returned, billed to your own account. No subscription.

## Server URL

Streamable HTTP, OAuth:

```
https://mcp.apify.com/?tools=messnomore/cfp-radar,messnomore/school-holidays-api,messnomore/visa-sponsor-checker,messnomore/halal-certificate-checker,messnomore/airline-baggage-allowance,messnomore/hs-code-classifier,messnomore/sea-listing-localizer,messnomore/website-categorization,messnomore/mobile-plans-tracker,messnomore/video-to-recipe,get-actor-run,get-dataset-items
```

`get-actor-run` and `get-dataset-items` let the assistant fetch results when a lookup takes longer than the client
waits.

## Tools

| Tool | Ask it for | Price |
| --- | --- | --- |
| [Visa Sponsor Checker](https://apify.com/messnomore/visa-sponsor-checker) (unofficial) | Whether a company is on the UK register of licensed sponsors, files US H-1B LCAs, or has positive Canadian LMIA decisions | $0.002 per company record, $0.005 per enriched job |
| [School Holidays & Term Dates API](https://apify.com/messnomore/school-holidays-api) | Term dates, half terms and holidays for 10 UK councils plus Northern Ireland, five Australian states and every Malaysian state | $0.005 per record |
| [Halal Certificate Checker](https://apify.com/messnomore/halal-certificate-checker) (unofficial) | Certificate status, expiry date and number in the JAKIM (Malaysia) and BPJPH (Indonesia) registries | $0.005 per check |
| [AI HS Code Classifier](https://apify.com/messnomore/hs-code-classifier) | 6-digit HS codes from product descriptions, mapped to US HTS, EU TARIC and UK codes with base duty rates | $0.01 per product |
| [Airline Baggage Allowance API](https://apify.com/messnomore/airline-baggage-allowance) | Carry-on, personal item and checked bag rules per fare for Malaysia Airlines, Thai Airways, Vietnam Airlines and Air Arabia | $0.02 per fare |
| [Shopee, Lazada & TikTok Shop Listing Translator](https://apify.com/messnomore/sea-listing-localizer) | Shopee, Lazada and TikTok Shop listings translated into Malay, Indonesian, Thai or Vietnamese within title limits, plus a Malaysia CPETTR 2024 check | $0.02 per localized listing, $0.01 per run |
| [AI Website Categorization](https://apify.com/messnomore/website-categorization) | IAB Content Taxonomy 3.1 categories, industry, brand safety flags, parked or dead domain detection | $0.006 per domain, $0.001 if unclassifiable |
| [CFP Radar](https://apify.com/messnomore/cfp-radar) | Open tech conference calls for papers by topic, region, deadline and travel support, ranked against your talk abstract | $0.01 per CFP |
| [Mobile Plans Tracker](https://apify.com/messnomore/mobile-plans-tracker) | SIM-only, prepaid and postpaid plans from 12 carriers in Malaysia, Singapore and Australia, priced per month and in USD, with the changes since your last run | $0.005 per plan, $0.05 per carrier |
| [Video to Recipe API](https://apify.com/messnomore/video-to-recipe) | Structured recipes from YouTube links (with your own YouTube API key), audio or video file links and pasted TikTok or Reels captions: ingredients, steps, servings, nutrition per serving and a shopping list | $0.04 per recipe, $0.015 per extra media minute |

Apify Bronze, Silver and Gold subscribers get lower prices. Apify's free plan includes monthly usage credit.

## Connect

### Claude (claude.ai and Claude Desktop)

1. **Settings** → **Connectors** → **Add** → **Add custom connector**.
2. Name: `MessNoMore data tools`. Remote MCP server URL: the URL above.
3. **Continue**, keep the detected OAuth settings, **Add**. Sign in to Apify in the browser window.

### Claude Code

```bash
claude mcp add --transport http messnomore "https://mcp.apify.com/?tools=messnomore/cfp-radar,messnomore/school-holidays-api,messnomore/visa-sponsor-checker,messnomore/halal-certificate-checker,messnomore/airline-baggage-allowance,messnomore/hs-code-classifier,messnomore/sea-listing-localizer,messnomore/website-categorization,messnomore/mobile-plans-tracker,messnomore/video-to-recipe,get-actor-run,get-dataset-items"
```

### ChatGPT

1. Turn on Developer mode.
2. **Settings** → **Apps & Connectors** → **Create**.
3. Paste the URL above as the MCP server URL, choose **OAuth**, then **Create** and authorize in the Apify window.

ChatGPT cannot change a connector's tools after it is created; create a new connector to change them.

### Cursor

`~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "messnomore": {
      "url": "https://mcp.apify.com/?tools=messnomore/cfp-radar,messnomore/school-holidays-api,messnomore/visa-sponsor-checker,messnomore/halal-certificate-checker,messnomore/airline-baggage-allowance,messnomore/hs-code-classifier,messnomore/sea-listing-localizer,messnomore/website-categorization,messnomore/mobile-plans-tracker,messnomore/video-to-recipe,get-actor-run,get-dataset-items"
    }
  }
}
```

### VS Code

`.vscode/mcp.json`:

```json
{
  "servers": {
    "messnomore": {
      "type": "http",
      "url": "https://mcp.apify.com/?tools=messnomore/cfp-radar,messnomore/school-holidays-api,messnomore/visa-sponsor-checker,messnomore/halal-certificate-checker,messnomore/airline-baggage-allowance,messnomore/hs-code-classifier,messnomore/sea-listing-localizer,messnomore/website-categorization,messnomore/mobile-plans-tracker,messnomore/video-to-recipe,get-actor-run,get-dataset-items"
    }
  }
}
```

### Clients without OAuth

Send your Apify API token as a header: `Authorization: Bearer YOUR_APIFY_TOKEN` (from Apify Console →
Settings → API & Integrations).

## Agent skill

The free [Data Tools Pack skill on Agensi](https://www.agensi.io/skills/data-tools-pack-visa-sponsors-hs-codes-halal-holidays)
is a SKILL.md for Claude Code, Cursor, Codex and other agents that support skills. Install it next to this server: the
skill holds the instructions and the server runs the lookups. It tells the agent which tool fits a question, caps each
run to the results it needs, asks before any run likely to cost more than about $1, waits for long runs without
starting duplicates, and answers with source links. Single-tool skills are listed on the
[creator page](https://www.agensi.io/creators/wong).

## Example prompts

- "Is Revolut a licensed UK visa sponsor, and for which routes?"
- "List the 2027 school holidays for New South Wales as a table."
- "Check whether Gardenia Bakeries has a valid JAKIM halal certificate."
- "What are the carry-on limits on Malaysia Airlines economy fares?"
- "Find the HS code and US base duty rate for a vacuum-insulated stainless steel water bottle."
- "Which IAB category is shopify.com in, and is it brand safe?"
- "Find open Rust conference CFPs that cover speaker travel."
- "Compare Singapore SIM-only plans by monthly price in USD."
- "Turn this TikTok caption into a recipe for 4 servings in cups, with a shopping list: ..."

## Guides and free data

- Setup guide: https://wong13977-oss.github.io/mcp-server/
- Agent skill (free): https://www.agensi.io/skills/data-tools-pack-visa-sponsors-hs-codes-halal-holidays
- Guides that explain the free official way to get each answer: https://wong13977-oss.github.io/
- Free school term dates sample dataset: https://github.com/wong13977-oss/school-term-dates

## Notes

- The tools are independent and read official public sources (the UK register of licensed sponsors, US DOL LCA
  disclosure data, Canadian positive LMIA lists, council and state school calendars, JAKIM and BPJPH registries, USITC
  and UK Trade Tariff, airline baggage pages, mobile carriers' plan pages, USDA FoodData Central). Video to Recipe reads only what you give it: pasted captions, file links, and YouTube titles and descriptions through your own YouTube Data API key. They are not affiliated with those organisations. Confirm important
  results with the official source.
- HS codes and duty rates need human review before a customs declaration; the duty shown is the base rate only.
- `server.json` in this repository is the [MCP Registry](https://registry.modelcontextprotocol.io) entry.

## License

MIT for the files in this repository. The tools themselves are paid Apify Actors.
