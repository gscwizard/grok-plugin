# Automating SEO reporting and optimization with a Grok Bot

A working playbook for running daily, weekly and monthly Search Console
work through a GSC Wizard Grok Bot: what to schedule, when to schedule it,
what counts as worth surfacing, and how to verify that the changes you make
actually did something.

The order matters. Everything here assumes you have run each analysis
interactively at least twice before putting it on a schedule.

---

## 0. Prerequisites

1. The GSC Wizard connector attached in Grok
   (`grok.com/connectors` → New Connector → Custom →
   `https://mcp.gscwizard.com/mcp`).
2. A **read-only** API key, unless a specific routine genuinely needs to
   write. A read key physically cannot call a write tool, which is what you
   want for anything running unattended on a schedule.
3. A Bot configured from [`../bot/GROK_BOT.md`](../bot/GROK_BOT.md).
4. One property per Bot, named explicitly in the routine prompt. A routine
   that has to guess which property you meant will eventually guess wrong.

---

## 1. Schedule around the data, not the calendar

**This is the part almost everyone gets wrong.** Search Console data
settles on a lag of roughly two to three days, and the GSC Wizard warehouse
reports the date each response is settled through.

So the intuitive schedules are quietly broken:

| Intuitive schedule | What it actually reads | Do this instead |
| --- | --- | --- |
| Weekly report, Monday morning | Last Mon–Sun week with its final 2–3 days missing or partial — every week understated, and the shortfall lands on the weekend | **Wednesday or Thursday** |
| Monthly report, 1st of the month | Previous month missing its last 2–3 days, which is also where month-end campaigns land | **3rd or 4th** |
| Daily check, this morning | Yesterday, which barely exists yet | Compare **settled days only** |

A weekly report that silently drops Saturday and Sunday will show a
"decline" every single week, and someone will eventually act on it. Moving
the job two days later costs nothing and removes a whole class of false
findings.

Two rules to put in every routine prompt:

- *"Use the most recent complete settled period. State the exact dates
  covered and the date the data is settled through."*
- *"If the period is not fully settled, say so and shorten the window
  rather than reporting a partial period as complete."*

---

## 2. Daily — exception watch (not a report)

**Goal:** notice a real problem within a day. Nothing else.

A daily report nobody reads is worse than no daily report, because it
trains you to ignore the channel. The daily job should stay silent unless
something crossed a threshold.

**Schedule:** every weekday morning.

**Prompt:**

```text
Run a daily exception check for <property>, using settled data only.

1. detect_anomalies on clicks and impressions for the last 30 days.
   Report only days flagged with high severity.
2. get_site_summary for the last 7 settled days vs the previous 7.
3. If anything material is flagged, check whether it is concentrated in
   one site section (breakdown_by_path) or one query set
   (get_ranking_changes), and check list_algo_updates for a confirmed
   Google update in the same window.
4. If the indexing tracker is configured, flag any newly lost URLs.

If nothing crosses the materiality thresholds, reply with one line:
"No material change." Do not summarize the numbers.
```

**Materiality thresholds** — tune these to your traffic, but set them
explicitly, or the Bot will invent its own:

| Signal | Surface it when |
| --- | --- |
| Clicks | Down more than 15% week over week, **and** more than ~100 clicks lost |
| A single page | Lost more than 25% of its clicks and was earning meaningfully before |
| Indexing | Any previously indexed URL now not indexed |
| Anomaly | High severity only — medium and low are noise on a daily cadence |

The absolute floor matters as much as the percentage. Without it, every
low-traffic page that drops from 4 clicks to 1 becomes a "75% decline".

---

## 3. Weekly — diagnosis

**Goal:** understand what moved and decide what to do about it.

**Schedule:** Wednesday or Thursday (see §1).

**Prompt:** run the `weekly-seo-report` skill for `<property>`, most recent
complete settled week against the week before.

It produces performance totals, ranking winners and losers, decay
candidates, CTR opportunities and growth opportunities, with an executive
summary on top and actions at the bottom.

**What to add to the standing instructions**, because it changes the output
substantially:

- *"Group related queries and pages rather than listing them flat.
  Three findings with evidence beat thirty rows."*
- *"For every loss, say which of these it is: ranking decay, demand
  decline, CTR decline, cannibalization, or a technical/indexing problem.
  If the data cannot distinguish them, say so."*
- *"End with at most three recommended actions, ordered by estimated
  clicks at stake."*

That last constraint is the one that makes the weekly report usable. An
unbounded list of recommendations gets skimmed; three ranked ones get done.

---

## 4. Monthly — strategy and the executive view

**Goal:** the slower-moving picture, and the report you forward.

**Schedule:** the 3rd or 4th of the month (see §1).

Run these as one routine, in this order:

```text
For <property>, previous complete month vs the month before:

1. get_site_summary — headline performance.
2. find_decaying_content (pages) — sustained decline, not a single bad
   week. Diagnose each candidate and rank by recoverable clicks.
3. score_opportunities and find_page_poaching_opportunities — near-term
   wins ranked by estimated click upside.
4. analyze_cannibalization — only cases where more than one page takes
   meaningful impressions.
5. analyze_ctr_curve — position buckets underperforming the benchmark,
   with the largest absolute click gaps named.

Produce an executive summary a non-SEO can read: what changed, why, what
we are doing about it, and what to expect next month. Keep measured facts,
interpretation and recommendations clearly separated.
```

For a client-ready artifact, follow up with `generate_seo_report`, which
returns a styled, self-contained HTML report rather than chat text.

**Quarterly**, add the things that are meaningless month to month:
`get_query_shape_trend` and `analyze_query_shapes` for how demand is
changing shape, `get_position_distribution` for whether the whole portfolio
is drifting, and `forecast_traffic` for the coming quarter.

---

## 5. Close the loop: annotate, wait, verify

Most reporting automation stops at reporting. This is the half that makes
it worth running, and it is the only part that needs a **write-scoped**
key.

The cycle:

1. **Decide.** The weekly or monthly run produces a ranked action.
2. **Ship it.** Rewrite the titles, consolidate the pages, fix the
   template.
3. **Annotate it, the day it ships.** `create_annotation` with the event
   date and what changed. This is the step everyone skips, and without it
   nobody can tell three months later whether the September dip was the
   redesign or a Google update.
4. **Wait.** Four weeks minimum for anything ranking-related, two for a
   pure CTR change. Checking after three days measures noise.
5. **Verify.** Ask the Bot to compare the four weeks after the annotation
   against the four weeks before, for the affected pages or queries, using
   `get_ranking_changes`, `get_page_performance` and `detect_change_points`.
   Ask explicitly whether the change point lines up with the annotation
   date, and whether a confirmed Google update (`list_algo_updates`) landed
   in the same window and could explain it instead.

A monthly "did last month's changes work?" routine, run against your
annotations, is the highest-value scheduled job in this whole document. It
is also the one that will tell you some of your favourite tactics do
nothing.

**Guardrail:** keep the verification routine on a read-only key and create
annotations yourself (or in a separate, deliberate write-scoped
conversation). An unattended routine that can write should be doing exactly
one known thing, never open-ended optimization.

---

## 6. Rollout order

Do not build all of this at once. Each step earns the next.

1. **Week 1** — connector attached, Bot created, everything run by hand.
   Check the numbers against the Search Console UI at least once, and
   confirm the periods it reports match what you asked for.
2. **Week 2** — schedule the weekly report only. Read it properly. Fix the
   instructions where it drifted, hedged, or buried the finding.
3. **Week 3** — add the daily exception watch, and tune the thresholds
   until it is genuinely quiet on quiet days. If it cries wolf twice, fix
   it before adding anything else.
4. **Week 4** — add the monthly report, and start annotating changes.
5. **Month 2** — add the verification routine, once you have annotations
   worth verifying against.

---

## 7. Standing guardrails

Put these in the Bot's persistent instructions, not in individual prompts:

- Prefer the server-side analysis tools over pulling raw rows. The
  deterministic result is the point; asking the model to add up thousands
  of rows is how a report ends up confidently wrong.
- Always state the analysis and comparison periods, and the settled-through
  date.
- Keep measured facts, interpretation and recommendations separate, and
  never present correlation with an algorithm update as causation.
- Prioritize by clicks at stake, not by position movement. A drop from 2 to
  5 on a high-volume query matters; 45 to 80 usually does not.
- A lower numerical average position is better. Say so once; models slip on
  this constantly.
- Never call a write tool without explicit intent in the current
  conversation.
- If a scheduled run has nothing material to say, say that in one line.

---

## See also

- [`../bot/GROK_BOT.md`](../bot/GROK_BOT.md) — the Bot profile and
  instruction text to paste in.
- [`../README.md`](../README.md) — the plugin, its skills and slash
  commands.
- [`../README.md#1-use-in-grok-chat-custom-connector--no-install-needed`](../README.md) — connect
  the server and confirm a first query returns data before scheduling anything against it.
