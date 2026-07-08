---
name: event-calendar
description: >
  Drishti MCP workflow for Indian-equity event calendars. Use when the user asks
  for upcoming events, earnings calendar, concall calendar, board meetings,
  result dates, investor calls, exchange-announcement events, portfolio or
  watchlist calendar, sector calendar, date-range event schedule, or a table of
  market/company events. Produces compact sourced tables by date using upcoming
  earnings, upcoming concalls, event records, announcements, metadata, and
  related filings.
---

# Event Calendar Skill

Build a sourced event calendar for Indian listed companies using Drishti MCP.
Account for user-provided dates, companies, watchlists, sectors, and event
types. Present the result as clear date-sorted tables with simple explanations.

## Operating Rules

- Call `search_tools` first if the needed Drishti MCP tools are not active.
- Use Drishti MCP as the primary source for events, earnings, concalls,
  announcements, metadata, and filings.
- Do not invent event dates, symbols, company names, agendas, filing links, or
  call details.
- Resolve ambiguous company names with `resolve_symbols`. If several plausible
  matches remain, ask the user to choose before fetching company-specific data.
- Use clear Indian listed symbols directly when provided.
- Keep event tables compact for CLI output.
- State the exact date range used.
- Do not provide buy, sell, or hold recommendations.

## Time Frame Handling

Resolve the exact date range before filtering results:

- Exact dates: use the supplied inclusive range.
- `today`: use the current local date.
- `tomorrow`: use the next local calendar date.
- `this week`: use today through the coming Sunday.
- `next week`: use the next Monday through Sunday.
- `next N days`: use today through today plus N calendar days.
- `this month`: use today through the end of the current month.
- Month names: use that calendar month.
- No time frame: default to the next 30 calendar days.

If a Drishti tool does not accept date filters, fetch enough records with
pagination and filter locally to the resolved range.

## Event Types

Support these event categories:

- `Earnings`: scheduled financial results and result announcements.
- `Concall`: upcoming or recent investor/analyst conference calls.
- `Board meeting`: meetings for results, dividends, fundraising, buybacks,
  M&A, capital actions, or other agenda items.
- `Corporate action`: dividends, splits, bonus, rights, buybacks, mergers,
  demergers, fundraising, and related actions.
- `Filing`: exchange announcements and important company filings.
- `Investor update`: investor presentations, transcripts, business updates,
  and management commentary.
- `Other`: material events that do not fit the above categories.

If the user asks for only one event type, filter to that type. If not, include
earnings, concalls, board meetings, and important announcements by default.

## Drishti MCP Tool Plan

Use the most specific available tools:

- `resolve_symbols`: ambiguous company names, partial tickers, or group names.
- `get_symbol_metadata` or `get_symbols`: company name, sector, industry, theme,
  market cap, and scrip code.
- `get_events`: primary calendar/event records, including board meetings and
  scheduled events.
- `get_upcoming_earnings`: scheduled unpublished financial-results events.
- `get_upcoming_concalls`: scheduled investor or analyst calls.
- `get_announcements` or `search_announcements`: exchange filings, meeting
  intimations, outcome filings, investor presentations, corporate actions, and
  event corroboration.
- `list_earnings`: check whether a scheduled result has already been reported.
- `get_concalls` or `search_concalls`: recent calls or call records around the
  requested period.
- `get_news` or `search_news`: optional event-related context when requested.
- `web_search`: only for external context unavailable in Drishti; label it
  separately.

## Workflow Checklist

```text
Event Calendar Progress:
- [ ] Step 1: Parse date range, universe, event types, and output preference
- [ ] Step 2: Resolve symbols or build the requested universe
- [ ] Step 3: Fetch events, upcoming earnings, upcoming concalls, and filings
- [ ] Step 4: Deduplicate and classify events
- [ ] Step 5: Enrich rows with metadata and source confidence
- [ ] Step 6: Return compact calendar tables and caveats
```

## Step 1: Parse Request

Identify:

- Date range or relative time frame.
- Company list, symbols, watchlist, sector, industry, theme, or broad market.
- Event type filters: earnings, concalls, board meetings, corporate actions,
  filings, investor updates, or all.
- Output preference: daily agenda, weekly calendar, grouped by company, grouped
  by event type, watchlist view, or sector view.

If no universe is specified, build a broad market event calendar for the
requested window.

## Step 2: Resolve Universe

For company or watchlist requests:

- Resolve ambiguous names with `resolve_symbols`.
- Use resolved symbols consistently across event, earnings, concall, filing, and
  metadata tools.

For sector or theme requests:

- Use metadata and sector/company tools when available to identify relevant
  symbols.
- Fetch events broadly if needed, then filter by metadata.

For broad market requests:

- Fetch upcoming earnings, upcoming concalls, and event records with enough
  pagination to cover the requested date range.

## Step 3: Build Event Rows

Create one normalized row per event:

- Date
- Company
- Symbol
- Event type
- Event detail or agenda
- Source
- Confidence

Deduplicate records that refer to the same event, such as an upcoming earnings
record plus a board meeting announcement for the same results date. Preserve the
stronger source trail in the `Source` cell.

Use confidence labels:

- `High`: event is corroborated by Drishti event/upcoming record plus exchange
  announcement or filing.
- `Medium`: event appears in one primary Drishti calendar source.
- `Low`: event is inferred from partial, stale, or indirect evidence.

## Step 4: Enrich And Validate

Add metadata when useful:

- Sector
- Industry
- Market cap
- Cap bucket

If an event appears stale:

- For earnings, check `list_earnings` to see if results are already reported.
- For concalls, check recent `get_concalls` records.
- Mark stale events as `Reported` or `Completed` when appropriate, or exclude
  them if the user asked only for upcoming events.

If market cap is requested and unavailable, write `NA`; do not estimate it.

## Output Formats

Default event calendar:

```markdown
**Event Calendar**
Date range used: YYYY-MM-DD to YYYY-MM-DD

| Date | Company | Symbol | Event | Detail | Source |
|---|---|---:|---|---|---|
| YYYY-MM-DD | Company Name | SYMBOL | Earnings | Results board meeting | Events + announcement; confidence: High |
```

Watchlist calendar:

```markdown
| Date | Symbol | Event | Detail | Status | Source |
|---|---:|---|---|---|---|
```

Grouped daily agenda:

```markdown
| Date | Earnings | Concalls | Board Meetings | Other Important Events |
|---|---:|---:|---:|---:|
```

Sector or market-cap view:

```markdown
| Date | Company | Symbol | Sector | Market Cap | Event |
|---|---|---:|---|---:|---|
```

Corporate-action view:

```markdown
| Date | Company | Symbol | Action | Detail | Filing Source |
|---|---|---:|---|---|---|
```

## Final Answer Requirements

- Lead with the exact date range used.
- Put the main event table first.
- Add a short `Notes` section only for unresolved symbols, unavailable market
  caps, stale/completed events, source gaps, or important assumptions.
- If no matching events are found, say so and list the filters used.
- Keep explanations simple and factual.

## Evidence Standards

- Cite the Drishti tool or source type behind each material event.
- Prefer dated filings and event records over generic descriptions.
- Do not turn event presence into an investment conclusion.
- Label external web context separately if used.
