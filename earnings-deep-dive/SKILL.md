---
name: earnings-deep-dive
description: >
  Drishti MCP workflow for analyzing Indian listed company earnings results.
  Use when the user asks for an earnings deep dive, quarterly results analysis,
  result review, financial performance breakdown, latest earnings summary,
  YoY/QoQ comparison, margin analysis, revenue/PAT/EBITDA table, concall-backed
  result interpretation, or simple explanation of a company's reported results.
  Produces sourced tables and plain-English takeaways using earnings filings,
  structured earnings tables, announcements, concalls, news, metadata, and
  relevant upcoming events.
---

# Earnings Deep Dive Skill

Analyze a reported earnings result for an Indian listed company using Drishti
MCP evidence. Prefer simple tables, short explanations, and clear source notes.

## Operating Rules

- Call `search_tools` first if the needed Drishti MCP tools are not active.
- Use Drishti MCP as the primary source for symbols, earnings, announcements,
  concalls, news, market cap, and events.
- Do not invent financial metrics, guidance, management commentary, or dates.
- If the company name or ticker is ambiguous, call `resolve_symbols`. If several
  plausible matches remain, ask the user to choose.
- If the user gives a clear Indian listed symbol, use it directly.
- If the user gives a quarter, use that quarter. If not, analyze the latest
  available reported quarter from Drishti.
- Separate reported facts from interpretation.
- Keep the explanation simple enough for a non-specialist investor.
- Do not provide buy, sell, or hold recommendations.

## Drishti MCP Tool Plan

Use the most specific available tools:

- `resolve_symbols`: resolve ambiguous company names or tickers.
- `get_symbol_metadata` or `get_symbols`: company name, sector, industry, theme,
  market cap, scrip code, and basic context.
- `list_earnings`: find available reported quarters and identify the latest
  quarter when the user does not specify one.
- `get_earnings_filing`: fetch the detailed earnings filing, summaries, source
  filing details, and structured earnings table.
- `get_earnings_attachments`: resolve filing/PDF URLs only when the user asks
  for source documents or the analysis needs attachment-level verification.
- `get_announcements` or `search_announcements`: result announcements, board
  meeting outcomes, investor presentations, dividend/corporate-action context,
  and result-related filings.
- `get_concalls`, `search_concalls`, or `get_concall_detail`: management
  commentary, transcript/audio URLs, sentiment, risks, guidance, and Q&A themes.
- `get_upcoming_concalls`: if results are reported but the call is still
  upcoming.
- `get_news` or `search_news`: result coverage, sector context, and market
  reaction.
- `get_price_and_volume`: optional post-result stock reaction when the user asks
  how the market reacted.
- `get_upcoming_earnings` or `get_events`: optional next result/event context.
- `web_search`: only for external context unavailable in Drishti; label it
  separately.

## Workflow Checklist

```text
Earnings Deep Dive Progress:
- [ ] Step 1: Resolve company and target quarter
- [ ] Step 2: Fetch metadata and earnings filing
- [ ] Step 3: Extract key financial metrics and changes
- [ ] Step 4: Add announcements, concall, news, and event context
- [ ] Step 5: Interpret performance drivers and risks
- [ ] Step 6: Return tables, simple explanation, and caveats
```

## Step 1: Resolve Company And Quarter

Identify:

- Company name, symbol, exchange/scrip code if available.
- Quarter requested by the user, such as `Q1 FY27`, `q1_27`, `latest quarter`,
  `last reported quarter`, or `FY26 Q4`.
- Whether the user wants a quick summary, detailed deep dive, peer comparison,
  market reaction, concall takeaways, or source pack.

If quarter format is unclear, infer the likely fiscal quarter from Drishti data
only after calling `list_earnings`. State the quarter actually analyzed.

## Step 2: Fetch Core Evidence

For the selected company:

1. Fetch metadata for company name, sector, industry, market cap, and theme.
2. Fetch `list_earnings` to identify available quarters.
3. Fetch `get_earnings_filing` for the target quarter.
4. Fetch result-related announcements around the filing date.
5. Fetch concall data for the same quarter. If no call is available, check
   upcoming concalls.
6. Fetch news around the result date only if useful for context.

If Drishti has no earnings filing for the requested quarter, say so clearly and
show the nearest available quarters from `list_earnings`.

## Step 3: Build The Financial Tables

Prefer metrics from structured earnings tables when available. Common metrics:

- Revenue or sales
- EBITDA or operating profit
- EBITDA margin or operating margin
- PAT or net profit
- EPS
- Segment revenue or segment profit
- Order book, AUM, loan book, NIM, GNPA/NNPA, combined ratio, or other
  sector-specific metrics when present in the filing

Do not force generic metrics if the company's sector uses different reporting
metrics. Use the actual available labels from Drishti.

Default financial table:

```markdown
| Metric | Current Quarter | YoY | QoQ | Comment |
|---|---:|---:|---:|---|
| Revenue | Rs X cr | +Y% | +Z% | Short explanation |
```

If YoY or QoQ values are unavailable, write `NA`. Do not calculate them unless
the needed current and comparison figures are clearly available.

For segment results:

```markdown
| Segment | Revenue/Metric | Growth | Margin/Profit | Comment |
|---|---:|---:|---:|---|
```

For banks/NBFCs/financials, adapt the table:

```markdown
| Metric | Current Quarter | Change | Comment |
|---|---:|---:|---|
| Net Interest Income | Rs X cr | +Y% YoY | Short explanation |
| NIM | X% | +Y bps | Short explanation |
| GNPA | X% | -Y bps | Asset quality comment |
```

## Step 4: Add Context Tables

Use only the tables that match the user request and available evidence.

Concall takeaways:

```markdown
| Theme | Management Commentary | Why It Matters |
|---|---|---|
| Demand | Short sourced summary | Plain-English implication |
```

Announcements and filings:

```markdown
| Date | Filing/Event | Relevance |
|---|---|---|
| YYYY-MM-DD | Result announcement / investor presentation | Why it matters |
```

Market reaction, only when requested:

```markdown
| Date/Period | Price Move | Volume Context | Possible Driver |
|---|---:|---|---|
```

Risks and watch items:

```markdown
| Watch Item | Evidence | What To Monitor |
|---|---|---|
```

## Step 5: Interpret Simply

Write short, direct explanations:

- What improved?
- What worsened?
- What drove revenue and margins?
- Was growth broad-based or concentrated?
- Did management commentary confirm or contradict the numbers?
- Are there one-offs, seasonality, accounting effects, or sector factors?
- What should be monitored next quarter?

Use confidence labels:

- `High`: directly supported by earnings filing or concall.
- `Medium`: supported by related filing/news but not directly quantified.
- `Low`: plausible inference with limited evidence.

## Final Output Format

Use this structure by default:

```markdown
**Earnings Deep Dive: Company Name (SYMBOL)**
Quarter analyzed: [Quarter]
Market cap: [Rs X cr or NA]
Sources used: [Drishti earnings filing, concall, announcements, news]

**Simple Summary**
[3-5 bullets in plain English.]

**Key Financials**
| Metric | Current Quarter | YoY | QoQ | Comment |
|---|---:|---:|---:|---|

**What Drove The Result**
| Driver | Evidence | Interpretation |
|---|---|---|

**Management Commentary**
| Theme | Commentary | Implication |
|---|---|---|

**Risks And Watch Items**
| Item | Evidence | Monitor |
|---|---|---|

**Source Notes**
[Brief dated source notes and unavailable-data caveats.]
```

If the user asks for a brief answer, include only `Simple Summary`,
`Key Financials`, and `Risks And Watch Items`.

## Evidence Standards

- Cite the Drishti tool/source behind each major claim.
- Prefer dated source references over generic phrases.
- Say `not available in retrieved data` when a metric or commentary is missing.
- Keep tables compact; use at most 5 columns.
- Avoid long quote blocks. Summarize management commentary unless exact wording
  is essential.
