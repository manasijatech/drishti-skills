---
name: earnings-analysis
description: >
  Drishti MCP workflow for high-quality Indian listed company earnings deep
  dives. Use when the user asks for an earnings deep dive, quarterly results
  review, latest results analysis, YoY/QoQ financial performance breakdown,
  margin analysis, revenue/EBITDA/PAT table, segment performance, concall-backed
  interpretation, post-result risks, or a plain-English explanation of reported
  earnings. Produces sourced, compact, decision-useful analysis using Drishti
  earnings filings, announcements, concalls, news, events, price/volume data,
  and company metadata. No buy/sell/hold recommendations.
---

# Indian Equity Earnings Deep Dive

Create professional **EARNINGS DEEP DIVE RESPONSES** for Indian listed
companies using Drishti MCP as the primary evidence source.

This skill is for agent responses, not long-form DOCX reports. The output should
feel like a concise institutional research note: factual, sourced, easy to scan,
and useful to an investor who wants to understand what changed in the latest
reported quarter.

## Key Characteristics

- **Length**: 800-1,800 words by default; shorter if the user asks for a brief
  summary.
- **Tables**: 2-5 compact markdown tables.
- **Charts**: Not required unless the user asks; use tables and sourced
  commentary first.
- **Audience**: Indian equity investors, analysts, or agents needing reliable
  earnings interpretation.
- **Focus**: Reported quarter, YoY/QoQ performance, drivers, management
  commentary, risks, and what to monitor next.
- **Tone**: Plain English, analytical, and source-grounded.
- **Recommendation Policy**: Do not provide buy, sell, hold, rating, target
  price, or personalized investment advice.

## When To Use

Use when the user requests:

- "Do an earnings deep dive for [Company]"
- "Analyze [Company] Q1 FY27 results"
- "Explain [Company]'s latest quarterly results"
- "Give me revenue, EBITDA, PAT, margins, YoY and QoQ"
- "Summarize the concall after results"
- "What drove the result and what are the risks?"
- "How did the market react after earnings?"

Do not use if:

- The request is for a generic company profile with no earnings angle.
- The user asks for upcoming results only; use an upcoming earnings calendar
  workflow instead.
- The user asks for a full published research report, valuation model, rating,
  or target price.

## Critical Requirements

### 1. Drishti MCP First

Use Drishti MCP as the system of record for Indian listed company facts.

The expected MCP namespace is `mcp__Drishti`. If Drishti tools are not already
visible in the active tool list, discover them with tool search using a query
such as `Drishti MCP earnings filings concalls announcements`, then proceed with
the returned Drishti tools. Do not substitute non-Drishti data for Indian market
facts unless Drishti is unavailable and the limitation is disclosed.

Before using Drishti data tools in a new session, call:

```text
read_me(mode="data")
```

Then use Drishti tools for:

- Symbol resolution and metadata
- Earnings filings and structured result tables
- Announcements and exchange filings
- Concall transcripts, summaries, Q&A themes, and upcoming calls
- Result-date news and market context
- Price and volume reaction when relevant
- Corporate calendar events

Use web search only when Drishti cannot answer a required external context
question. Label external-web evidence separately from Drishti evidence.

### 2. No Invented Data

Never invent:

- Revenue, EBITDA, PAT, EPS, margins, AUM, NIM, GNPA/NNPA, order book, volume,
  realizations, segment numbers, guidance, dates, or management commentary.
- YoY/QoQ growth rates when the current and comparison values are not available.
- Concall commentary when no concall transcript or summary was retrieved.

If a metric is missing, write `not available in retrieved Drishti data`.

### 3. Latest Quarter Discipline

If the user does not specify a quarter:

1. Use `list_earnings` for the company.
2. Identify the latest available reported quarter from returned Drishti records.
3. State the quarter actually analyzed and the filing/report date.

If the user specifies a quarter:

1. Use that quarter in `list_earnings` or `get_earnings_filing`.
2. If missing, show the nearest available quarters from `list_earnings`.
3. Do not silently switch quarters.

### 4. Separate Facts From Interpretation

Every important claim should be classed as one of:

- **Reported fact**: Directly from earnings filing, announcement, or structured
  Drishti table.
- **Management commentary**: From concall or transcript/search result.
- **Market context**: From Drishti news, events, or price/volume tools.
- **Analyst interpretation**: Your synthesis from the above evidence.

### 5. Source Attribution

Every table and major paragraph must name the source type and date when
available. Use dated source notes such as:

```text
Source: Drishti earnings filing for Q1 FY27, filed/reported 2026-07-22.
Source: Drishti concall search, Q1 FY27 transcript/summaries.
Source: Drishti announcements, 30-day window around result date.
```

When exact dates are not returned, say so rather than fabricating them.

## Drishti MCP Tool Usage

Use this exact tool order unless the user request clearly requires a smaller
subset.

### Step 0: Tool Routing Read

- `read_me(mode="data")`: call once at the start of a Drishti MCP analysis
  session when available.
- `get_workflow_prompt(workflow="earnings_concall", company_or_symbol="SYMBOL",
  quarter="qN_YY")`: optional helper when the agent needs Drishti's own
  task-specific workflow guidance before collecting data.

### Step 1: Resolve Company

- `resolve_symbols(queries=[company_or_phrase])`: use for ambiguous company
  names, short names, brands, or informal names.
- `get_symbols(query={"symbols": "SYMBOL"})`: use when the user gives a clear
  symbol.
- `get_sectoral_data(symbols="SYMBOL")`: fetch sector/industry/company metadata
  when useful for context.

If multiple plausible symbols remain after resolution, ask the user to choose.
Do not proceed with a guessed symbol when ambiguity is material.

### Step 2: Find The Earnings Record

- `list_earnings(query={"symbols": "SYMBOL", "limit": 8, "detailed": true})`:
  find available quarters and select the requested/latest quarter.
- `get_earnings_filing(symbol="SYMBOL", quarter="qN_YY", detailed=true)`: fetch
  the detailed filing for the selected quarter.
- If `list_earnings` returned a specific `id`, use
  `get_earnings_filing(earnings_id="...")` when that is more precise.

Prefer Drishti's `earnings_table` and extracted fields over manually parsing
free text. Use free-text summaries only to explain drivers and context.

### Step 3: Add Filing And Event Context

- `get_announcements(query={"symbols": "SYMBOL", "lookback_days": 60,
  "detailed": true})`: result announcement, financial results filing, investor
  presentation, dividend/corporate-action context.
- `search_announcements(search_query="financial results investor presentation
  dividend board meeting", symbols="SYMBOL", days=90)`: use when the direct
  announcement list is sparse or when looking for a specific filing type.
- `get_events(symbols="SYMBOL", days_count=90)`: upcoming board meetings, AGM,
  or event context where relevant.

### Step 4: Add Concall Evidence

- `search_concalls(search_query="quarter results revenue margin demand guidance
  outlook risks", symbols="SYMBOL", quarter_selector="LAST", result_mode="chunks",
  per_page=8)`: use for latest-quarter commentary when quarter is not specified.
- `search_concalls(..., specific_quarters="qN_YY")`: use for a specified
  quarter when the quarter code is known.
- `get_concalls(query={"symbols": "SYMBOL", "limit": 4, "detailed": true})`:
  retrieve available concall records and dates.
- `get_upcoming_concalls(query={"symbols": "SYMBOL", "detailed": true})`: use
  if results are out but the concall has not happened yet.

Do not claim management said something unless it is in retrieved concall data.
Summarize commentary; avoid long quotations.

### Step 5: Add News And Market Reaction

- `get_news(query={"symbols": "SYMBOL", "lookback_days": 30, "limit": 10})`:
  result coverage and sector context.
- `search_news(queries=["SYMBOL results", "Company quarterly results"],
  symbols="SYMBOL", days=30, per_query_limit=5)`: use for targeted result news.
- `get_price_and_volume(symbols="SYMBOL")`: current market data only.
- `get_price_and_volume_since(symbols="SYMBOL", days_count=7, series=true)`:
  post-result price/volume reaction when requested or useful.

Market reaction is optional. Include it only when the user asks or when it helps
explain the result's immediate impact.

## Analysis Workflow

The earnings deep dive process follows five phases.

### Phase 1: Identify Scope

Determine:

- Company name and symbol.
- Requested quarter, or latest available quarter.
- Whether the user wants brief summary, detailed deep dive, concall takeaways,
  market reaction, segment analysis, peer context, or source pack.
- Sector-specific metrics that matter.

Common Indian fiscal-quarter notation:

- `Q1 FY27` usually maps to `q1_27`.
- `Q2 FY27` usually maps to `q2_27`.
- `Q3 FY27` usually maps to `q3_27`.
- `Q4 FY27` usually maps to `q4_27`.

Always confirm the actual quarter returned by Drishti.

### Phase 2: Collect Evidence

Required minimum evidence:

- Company metadata from `get_symbols` and/or `get_sectoral_data`.
- Available earnings records from `list_earnings`.
- Target quarter details from `get_earnings_filing`.

Preferred additional evidence:

- Result-related announcements from `get_announcements` or
  `search_announcements`.
- Concall evidence from `search_concalls` or `get_concalls`.
- News from `get_news` or `search_news`.
- Events/upcoming concalls when concall data is missing.

If Drishti has no earnings filing for the requested quarter, stop and provide:

- What was requested.
- What Drishti returned.
- Nearest available quarters.
- What data would be needed to proceed.

### Phase 3: Build Financial Tables

Use actual metric labels from Drishti where possible. Do not force a standard
industrial template onto banks, NBFCs, insurers, commodity companies, or platform
businesses.

Default financial table:

```markdown
| Metric | Current Quarter | YoY | QoQ | Interpretation |
|---|---:|---:|---:|---|
| Revenue | Rs X cr | +Y% | +Z% | Short explanation |
| EBITDA | Rs X cr | +Y% | +Z% | Short explanation |
| EBITDA Margin | X% | +Y bps | -Z bps | Short explanation |
| PAT | Rs X cr | +Y% | +Z% | Short explanation |
```

For financials:

```markdown
| Metric | Current Quarter | Change | Interpretation |
|---|---:|---:|---|
| Net Interest Income | Rs X cr | +Y% YoY | Short explanation |
| NIM | X% | +Y bps YoY | Margin implication |
| GNPA | X% | -Y bps QoQ | Asset quality implication |
| PAT | Rs X cr | +Y% YoY | Profitability implication |
```

For sector-specific results:

```markdown
| Metric/Segment | Current Quarter | Change | What It Means |
|---|---:|---:|---|
| Order Book / AUM / Volume / Occupancy | X | +Y% | Business driver |
```

If YoY or QoQ is absent, write `NA`. Calculate only when all values needed for
the calculation are retrieved and clearly comparable.

### Phase 4: Explain Drivers

Answer these questions:

- What was the headline result?
- What improved versus last year and last quarter?
- What worsened?
- Was growth led by volume, pricing, mix, utilization, recoveries, treasury
  gains, credit costs, commodity prices, or one-offs?
- Did margins expand or contract, and why?
- Did segment performance diverge?
- Did management commentary confirm, soften, or contradict the reported numbers?
- What are the next-quarter watch items?

Use confidence labels:

- **High**: directly supported by earnings filing, structured table, or concall.
- **Medium**: supported by related announcement/news, but not fully quantified.
- **Low**: reasonable inference from evidence; mark clearly as interpretation.

### Phase 5: Produce The Response

Default structure:

```markdown
**Earnings Deep Dive: Company Name (SYMBOL)**
Quarter analyzed: QN FYYY
Evidence base: Drishti earnings filing, announcements, concall/news if available

**Bottom Line**
- 3-5 bullets with the result verdict, main driver, margin/profit quality, and
  key risk/watch item.

**Key Financials**
| Metric | Current Quarter | YoY | QoQ | Interpretation |
|---|---:|---:|---:|---|

Source: Drishti earnings filing for [quarter], [date if available].

**What Drove The Quarter**
| Driver | Evidence | Interpretation | Confidence |
|---|---|---|---|

**Management Commentary**
| Theme | What Management Indicated | Why It Matters |
|---|---|---|

**Risks And Watch Items**
| Item | Evidence | What To Monitor |
|---|---|---|

**Source Notes And Caveats**
- Dated Drishti sources used.
- Missing data caveats.
- Any external-web source used, clearly labeled.
```

If the user asks for a brief answer, return only:

- `Bottom Line`
- `Key Financials`
- `Risks And Watch Items`
- `Source Notes`

If the user asks for concall-only analysis, focus on:

- Management commentary themes
- Demand/pricing/margin outlook
- Capex, balance sheet, guidance, and risks
- Q&A concerns
- Explicit caveat if transcript/search coverage is incomplete

If the user asks for market reaction, add:

```markdown
**Market Reaction**
| Period | Price Move | Volume Context | Likely Driver |
|---|---:|---|---|
```

Use `get_price_and_volume_since` for the table and label "likely driver" as
interpretation unless directly supported by news.

## Response Quality Bar

A strong answer should:

- Lead with the earnings verdict, not background.
- Quantify the key moves where Drishti provides numbers.
- Use the company's sector-specific metrics.
- Explain both growth and margin/profit quality.
- Include management commentary when available.
- Separate one-offs from recurring drivers.
- Identify next-quarter watch items.
- Cite Drishti source types and dates.
- Clearly state missing data instead of filling gaps.
- Avoid recommendation language.

## Common Mistakes To Avoid

- Using training-memory numbers instead of Drishti MCP data.
- Saying "latest quarter" without naming the quarter and date.
- Mixing quarters from filings, concalls, and news without checking dates.
- Calculating YoY/QoQ from incompatible standalone vs consolidated numbers.
- Treating management commentary as fact without sourcing concall data.
- Overloading the response with long company background.
- Providing buy/sell/hold advice or target prices.
- Calling missing data "flat", "weak", or "strong" without evidence.

## Verification Checklist

Before final response, verify:

- [ ] `read_me(mode="data")` was called when Drishti tools were available.
- [ ] Symbol was resolved or confirmed.
- [ ] Quarter analyzed is explicitly stated.
- [ ] Earnings filing was retrieved with `get_earnings_filing` or missing data was
      disclosed.
- [ ] Tables use actual retrieved metrics and labels.
- [ ] YoY/QoQ values are sourced or clearly calculated from retrieved values.
- [ ] Concall claims are backed by `search_concalls` or `get_concalls`.
- [ ] Announcement/news/event context is dated where available.
- [ ] Missing data is called out plainly.
- [ ] No recommendation, rating, target price, or personalized advice is given.

## Dependencies

Required:

- Drishti MCP tools for Indian equity data.

Optional:

- Spreadsheet or charting tools if the user asks for exportable tables, model
  updates, or charts.
- Web search only for external context unavailable in Drishti; label separately.
