# GSC Wizard for Grok

Google Search Console analysis and SEO workflows in Grok, Grok Bots, and
Grok Build — powered by the [GSC Wizard](https://gscwizard.com) MCP server.

No new Search Console integration is involved: this package plugs Grok into
the existing production MCP endpoint, which already handles Google auth,
data warehousing, rate limiting, and 100+ tools with **server-side** SEO
analysis (ranking changes, content decay, cannibalization, CTR curves,
opportunity scoring, full HTML reports). The model orchestrates and
interprets; GSC Wizard computes.

```text
https://mcp.gscwizard.com/mcp
```

## 1. Use in Grok chat (custom connector — no install needed)

1. Open `grok.com/connectors` → **New Connector** → **Custom**.
2. Enter `https://mcp.gscwizard.com/mcp`.
3. Complete authentication: sign in to GSC Wizard (OAuth), or paste a
   `gscw_live_` API key from **gscwizard.com → Account → API keys**.
   Read-only keys cannot invoke write tools.

Then ask things like:

- "How did example.com perform over the last 28 days?"
- "Which queries lost the most rankings this month?"
- "Find query cannibalization on example.com."
- "Find my best near-term organic search opportunities."

## 2. Use as a Grok Bot

See [`bot/GROK_BOT.md`](bot/GROK_BOT.md) for the copy-paste profile of the
**GSC Wizard SEO Analyst** Bot (persistent instructions + suggested
routines). Bots are shareable by public link — the recipient gets the
configuration, never your credentials — and each user connects their own
GSC Wizard account.

## 3. Use in Grok Build (this plugin)

The plugin bundles:

| Component | Path | Contents |
| --- | --- | --- |
| MCP server | `.mcp.json` | Remote GSC Wizard server registration |
| Agent | `agents/seo-analyst.md` | Specialized SEO analyst subagent |
| Skills | `skills/` | **gsc-wizard-mcp** (how to use the MCP: tool choice, dates, row caps, scope, errors — plus a full tool map), then the workflows: weekly-seo-report, ranking-changes, cannibalization, content-decay, ctr-opportunities, seo-opportunities |
| Commands | `commands/` | `/gsc-report`, `/gsc-rankings`, `/gsc-decay`, `/gsc-cannibalization`, `/gsc-opportunities` |
| Manifest | `.grok-plugin/plugin.json` | Plugin metadata |

To put these on a schedule — daily exception watch, weekly diagnosis,
monthly executive report, and the annotate-wait-verify loop that checks
whether your changes worked — follow
[`docs/PLAYBOOK.md`](docs/PLAYBOOK.md).

Authentication happens at connect time (browser OAuth flow, or an API-key
header where static headers are appropriate). **Never commit keys to this
repository.**

## Design principles baked into the skills

- Prefer GSC Wizard's deterministic server-side analysis tools over pulling
  raw Search Console rows and doing model-side arithmetic
  (`query_search_analytics` is a last resort).
- Date ranges are optional everywhere; omitted ranges default to the last
  28 settled days (GSC data lags ~3 days). Answers always state their
  periods.
- Facts, interpretation, and recommendations are kept separate; findings
  are prioritized by clicks at stake, not raw position movement.
- Write/mutation tools require explicit user intent.

## Support

Product docs, account settings, and API keys live at
[gscwizard.com](https://gscwizard.com). Problems with the plugin itself can
be filed as an issue in this repository.

## License

MIT — see [`LICENSE`](LICENSE).
