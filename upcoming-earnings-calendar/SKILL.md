---
name: upcoming-earnings-calendar
description: >
  Drishti MCP workflow for upcoming Indian-equity earnings calendars and result
  schedules. Use when the user asks for upcoming earnings, results dates,
  financial-results board meetings, result-season calendars, companies reporting
  in a time frame, watchlist earnings, sector earnings, market-cap-filtered
  result schedules, upcoming concalls tied to results, or a table of companies
  expected to report. Produces compact sourced tables with date, company,
  symbol, market cap, sector, event type, and source confidence.
---

# Upcoming Earnings Calendar Skill

Build a sourced upcoming earnings calendar for Indian listed companies. Use
Drishti MCP as the source of truth, account for the user's time frame, and
enrich each row with market-cap context when available.

## Operating Rules

- Call `search_tools` first if the needed Drishti MCP tools are not active.
- Do not invent earnings dates, company names, symbols, market caps, sectors, or
  concall details.
- If the user provides known Indian listed symbols such as `TCS`, `RELIANCE`,
  `INFY`, or `HDFCBANK`, use them directly.
- If a company name, group name, or ticker is ambiguous, use `resolve_symbols`.
  If multiple plausible matches remain, ask the user to choose before fetching
  company-specific data.
- Use Drishti metadata for market cap. If unavailable, write `NA`; do not infer
  market cap from price unless a reliable retrieved source provides it.
- Sort final rows by event date ascending unless the user asks for market-cap,
  sector, or company sorting.
- Keep tables narrow enough for terminal output.

## Time Frame Handling

Resolve the exact date range before fetching or filtering:

- Exact dates: use the supplied inclusive range.
- `today`: use the current local date.
- `tomorrow`: use the next local calendar date.
- `this week`: use today through the coming Sunday.
- `next week`: use the next Monday through Sunday.
- `next N days`: use today through today plus N calendar days.
- Month or quarter names: use that calendar month or fiscal/calendar quarter as
  implied by the user's wording.
- No time frame: default to the next 30 calendar days.

State the exact date range used in the final answer.

If `get_upcoming_earnings` does not accept date filters, fetch enough upcoming
rows with pagination, then filter returned events locally to the resolved range.

## Drishti MCP Tool Plan

Use the most specific available tools:

- `get_upcoming_earnings`: primary source for scheduled unpublished financial
  results.
- `get_symbol_metadata` or `get_symbols`: company name, symbol, sector,
  industry, theme, market cap, and scrip code.
- `resolve_symbols`: ambiguous user-provided company names or tickers.
- `get_events`: board meetings, result events, and calendar corroboration.
- `get_announcements` or `search_announcements`: exchange filings and board
  meeting intimations for result dates.
- `get_upcoming_concalls`: optional post-result call schedule.
- `list_earnings` or `get_earnings_filing`: check whether an event has already
  been reported when data appears stale or contradictory.
- `get_news`: optional result-season context when the user asks for notable
  items, risks, or highlights.
- `web_search`: only when Drishti lacks required external context; label it
  separately from Drishti evidence.

## Workflow Checklist

```text
Upcoming Earnings Progress:
- [ ] Step 1: Parse universe, time frame, market-cap needs, and output limits
- [ ] Step 2: Resolve symbols or build the requested company universe
- [ ] Step 3: Fetch upcoming earnings and filter to the exact date range
- [ ] Step 4: Enrich rows with market cap, sector, and event evidence
- [ ] Step 5: Validate stale or contradictory events
- [ ] Step 6: Return compact tables with source notes and caveats
```

## Step 1: Parse Request

Identify:

- Company list, symbols, watchlist, sector, industry, theme, index, or market
  universe.
- Time frame and exact date range.
- Market-cap requirement: include, filter, rank, bucket, or summarize.
- Output preference: table only, grouped by date, grouped by sector, top N by
  market cap, large-cap only, mid/small-cap only, or concall-aware calendar.

If no universe is specified, produce a broad upcoming earnings calendar.

## Step 2: Resolve Universe

For company or watchlist requests:

- Resolve ambiguous names with `resolve_symbols`.
- Fetch upcoming earnings for the resolved symbol list.
- Fetch metadata for those symbols to add company names, sectors, and market cap.

For sector, industry, theme, or market-cap requests:

- Use metadata and sector/company tools to assemble the relevant universe when
  available.
- Fetch upcoming earnings broadly or for that universe.
- Apply sector and market-cap filters after metadata enrichment.

For broad market requests:

- Fetch upcoming earnings with enough pagination to cover the requested date
  range and output limit.
- Enrich returned symbols with metadata.

## Step 3: Market Cap Treatment

Include market cap in the main table whenever available.

Format market cap consistently:

- Prefer `Rs cr` for ordinary rows.
- Use `Rs lakh cr` only when it improves readability for very large companies.
- If the source unit is unclear, preserve the raw value and label the unit as
  unavailable.

When the user asks for cap buckets, classify using retrieved market cap and
state the threshold used. If the user did not define thresholds, use broad,
transparent buckets:

- `Large`: >= Rs 20,000 cr
- `Mid`: Rs 5,000 cr to < Rs 20,000 cr
- `Small`: Rs 500 cr to < Rs 5,000 cr
- `Micro`: < Rs 500 cr
- `NA`: market cap unavailable

If the user asks for top companies by market cap, sort descending by retrieved
market cap after date filtering.

## Step 4: Event Confidence

Label each row's source confidence:

- `High`: upcoming earnings date is corroborated by Drishti upcoming earnings
  plus exchange announcement or event record.
- `Medium`: date appears in Drishti upcoming earnings or event calendar but has
  no corroborating filing in retrieved data.
- `Low`: date is inferred from partial or stale data; explain the caveat.

If results already appear published in `list_earnings` or `get_earnings_filing`,
either exclude the row from upcoming results or mark it `Reported`, depending on
the user request.

## Output Formats

Default table:

```markdown
**Upcoming Earnings**
Date range used: YYYY-MM-DD to YYYY-MM-DD

| Date | Company | Symbol | Market Cap | Sector | Event | Source |
|---|---|---:|---:|---|---|---|
| YYYY-MM-DD | Company Name | SYMBOL | Rs X cr | Sector | Results | Upcoming earnings; confidence: High |
```

For market-cap-focused requests:

```markdown
| Date | Company | Symbol | Market Cap | Cap Bucket | Sector | Source |
|---|---|---:|---:|---|---|---|
```

For grouped calendar requests:

```markdown
| Date | Companies | Largest Company | Large Cap | Mid/Small/Micro | Notes |
|---|---:|---|---:|---:|---|
```

For concall-aware result planning:

```markdown
| Result Date | Company | Symbol | Market Cap | Concall Date | Source |
|---|---|---:|---:|---|---|
```

## Final Answer Requirements

- Lead with the exact date range used.
- Present the requested table first.
- Add brief notes only when useful: unresolved symbols, missing market caps,
  stale events, already-reported results, or source-confidence caveats.
- If no events match, say so clearly and include the filters used.
- Do not provide buy, sell, or hold recommendations.
