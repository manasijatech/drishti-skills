---
name: quick-stock-analysis
description: >
  Run a 10-15 minute first-pass triage of a listed company to decide whether it
  deserves deeper research. Use for quick stock analysis, stock screens,
  past-present-future reviews, "analyze this stock in 15 minutes", "is [ticker]
  worth deeper research", or style-fit triage for value, growth, quality,
  turnaround, momentum, or general investors. Analyze the business, financials,
  recent results, management clues, valuation, ownership, price behaviour, risks,
  and catalysts using available financial-data, filing, market-data, news, web,
  or document tools. For Indian listed equities, require Drishti MCP for core
  current context; if it is missing, ask the user to add Drishti MCP before
  continuing. Apply the bundled humanizing pass before returning. Return a research
  disposition, never buy/sell/hold advice or a target price.
---

# Quick Stock Analysis

Produce a compact decision document answering one question:

> Is this company worth deeper research for this investor?

Treat the 10-15 minute framing as scope discipline, not permission to guess.
Study the company's past, present, and plausible future. Aim for 60-70% clarity
so weak ideas can be discarded quickly and promising ideas can move to deeper
research. Prefer a smaller data-backed report over a complete-looking report
with invented fields.

## Guardrails

- Do not give buy, sell, hold, allocation, return, or target-price advice.
- Resolve the exact listed security before making company-specific claims.
- Use retrieved current data, not model memory, for financial and market facts.
- Separate reported facts, management claims, and analyst interpretation.
- Attach a date or period to every material current claim.
- Treat guidance and catalysts as possibilities, not outcomes.
- State missing or incomparable data as `Not available`; never fill gaps.
- Compare consolidated with consolidated and standalone with standalone.
- Account for sector economics. Do not force industrial-company ratios onto
  banks, NBFCs, insurers, commodity producers, or pre-profit businesses.
- Prefer primary exchange filings, company reports, annual reports, investor
  presentations, earnings releases, and transcripts over news summaries. Use
  news for corroboration or external context.
- Explain price-event correlation cautiously. Price action supports a screen;
  it does not prove business quality or causality.
- Make the report scannable in narrow surfaces. Use short sections and tables
  with no more than five compact columns.
- If the available tools do not cover the requested market, say so and either
  ask for source documents or use web/document tools when available.

## Required Input

Require:

- Company name or ticker

Use when supplied:

- Investor style: value, growth, quality, turnaround, momentum, or general
- Holding period
- Maximum acceptable valuation or debt
- Specific concern to investigate
- Market, exchange, ISIN, CIK, SEDOL, or other identifier

### Symbol Resolution

- If the user provides an unambiguous listed symbol and market, use it directly.
- If the company name, brand, group, partial ticker, or security reference is
  ambiguous, stop and ask the user to choose before calling company tools.
  Include company name, exchange, symbol/ticker, ISIN or other identifier, and
  industry when available.
- If investor style is absent, use `General` and evaluate all lenses without
  pretending to know suitability.

## Runtime Evidence Plan

Gather only the evidence needed for a first-pass screen. Use the current agent's
available tools and map them to these capabilities. Run independent safe lookups
in parallel when supported. Prefer the narrowest reliable source for each lane;
use broad company dossiers only when several lanes are required.

### 0. Drishti For Indian Listed Equities

When the requested company is Indian-listed, require Drishti MCP for core
current context before relying on general web or news search. Treat Drishti as
the current-market layer, not the whole analysis.

If Drishti MCP is not present in the current agent session, stop and ask the
user to add or connect Drishti MCP before continuing the analysis. Do not
silently substitute web search, model memory, or another source for Drishti's
current-context lane.

Use Drishti for:

- Symbol and company metadata when available
- Latest earnings and earnings filing context
- Exchange announcements and material filings
- Concall records, transcripts, or management commentary
- Market news tied to the company
- Upcoming earnings, concalls, board meetings, or other events
- Alerts, price/volume, top-mover, 52-week, or market-context signals when the
  active Drishti interface exposes them

After confirming Drishti MCP is present, first read its current data-tool
instructions, commonly through `read_me(mode="data")`, then use the exact tool
names and schemas exposed in that session. Do not invent Drishti tool names or
assume app-specific wrappers exist. If Drishti MCP is connected but a specific
Drishti lane is unavailable, state that limitation and continue with the other
Drishti lanes plus the best available financial-data, filing, document, web, or
market-data sources.

### 1. Primary Financial Tables (Required)

Use the best available financial-data provider, company filings, annual reports,
or extracted spreadsheets to gather:

- Company description and key ratios
- Peer comparison table
- Quarterly results
- Annual P&L
- Balance sheet
- Cash flows
- ROE / ROCE and multi-year ratios
- Shareholding, ownership, dilution, and pledge where relevant

Preserve table values carefully. If a data pack or extracted document is saved
to a file, read the needed sections from that artifact rather than re-fetching.

### 2. Current Company Context (Required)

Use available market-intelligence, filing, earnings, transcript, news, or web
tools to gather:

- Latest earnings release or quarterly result
- Recent exchange filings or material announcements
- Investor presentation or annual report when relevant
- Latest concall transcript or management commentary when available
- Recent credible news that explains external context
- Upcoming earnings, meetings, dividends, corporate actions, or regulatory
  events when they affect the screen

Avoid downloading full transcripts, attachments, or detailed filings unless the
triage depends on them. Capture document dates, event dates, and reporting
periods while reasoning.

### 3. Focused Follow-Ups (Optional)

Use only when the initial evidence raises a specific question:

| Need | Capability to use |
|---|---|
| Recent-quarter results + management commentary | Earnings and transcript tools |
| Filings, annual reports, investor presentations | Filing or document tools |
| Price, volume, 52-week range, liquidity | Market-data tools |
| Orders, tenders, capex, expansion | Announcement, filing, or news tools |
| Dividends, M&A, splits, buybacks, capital actions | Corporate-action tools |
| Promoter, insider, institution, dilution, pledge | Ownership or governance tools |
| Negative news, legal, regulatory, auditor issues | Risk, filing, and news tools |
| Upcoming results, calls, board meetings | Event-calendar tools |
| Peer comparison | Financial-data provider or peer tools |
| Missing source documents | Ask the user for files or links |

Do not treat short-term price or alert data as a full historical price series.
State the limitation when exact price-history evidence is unavailable.

### 4. External Context (Last Resort)

Use web search or browser tools for policy, commodity, tariff, regulatory,
macro, media, or industry context not covered by structured sources. Read the
discovered source directly before relying on it. Use memory or prior notes only
for user preferences or earlier work, never as a substitute for current facts.

If an essential data lane is absent, state the limitation and name what must be
checked next.

## Analysis Workflow

### 1. Understand The Business

Explain without financial jargon:

- What the company sells, who buys it, and how it earns revenue
- Major segments and revenue mix
- Domestic, export, or international exposure
- Important geographies, customers, products, or concentration
- Whether it is asset-light, capital-intensive, cyclical, seasonal, regulated,
  commodity-linked, project-led, or structurally growing

End with a one-line business description and a business classification.

### 2. Classify Historical Growth

Review roughly 5-10 years where available. Calculate or retrieve 3-, 5-, and
10-year revenue CAGR only from comparable values. Count declines and identify
base effects, acquisitions, disruptions, or cycle recoveries.

Classify the pattern as `Consistent grower`, `Cyclical`, `Turnaround`,
`Stagnant`, `Declining`, or `Newly scaling`.

### 3. Examine Margins And Profit Growth

Review the normal operating-margin range, stability, current margin versus
history, operating-profit CAGR, net-profit CAGR, and EPS growth. Explain whether
profit growth came from revenue, margins, interest, tax, other income,
exceptional items, or share-count changes.

Interpret cautiously:

- Revenue growth plus stable margins usually indicates healthier growth.
- Flat revenue plus margin recovery may be less durable.
- Revenue growth plus falling margins may indicate low-quality growth.

Prefer operating profit for the first operating read, then reconcile to PAT.

### 4. Evaluate Returns On Capital

Review current and multi-year ROE and ROCE. Test whether leverage inflates ROE,
whether capex temporarily depresses returns, and whether ongoing reinvestment is
required merely to maintain growth. Judge returns relative to business type and
financial risk rather than using a universal threshold.

### 5. Inspect The Balance Sheet And Capex

Compare asset growth with revenue growth. Review total and net debt,
debt-to-equity, interest coverage, fixed assets, CWIP, and recent capacity
additions. Classify debt as `Negligible`, `Comfortable`, `Elevated`, or
`Dangerous` relative to cash flow and business stability.

Interpret expansion:

- Rising assets and sales may show utilization.
- Rising CWIP may indicate future growth plus execution risk.
- Rising assets with flat sales may indicate poor utilization.
- Debt-funded capex adds balance-sheet risk until cash generation appears.

### 6. Test Profit-To-Cash Conversion

Use `net profit + depreciation` only as a quick reference point for operating
cash flow, not a substitute for a cash-flow statement. Review 5-10 years of CFO,
free cash flow, capex, receivables, inventory, payables, and working-capital
borrowing.

Classify cash conversion as `Strong`, `Acceptable`, `Mixed`, or `Weak`. Explain
whether weak free cash flow reflects expansion, poor collections, inventory, or
structural economics.

### 7. Review Receivables And Working Capital

Compare receivables as a percentage of revenue, receivable days, inventory days,
payable days, and the cash-conversion cycle when inputs are comparable. Flag
receivables growing faster than sales, profits without CFO, abrupt changes in
working-capital days, or unclear large other current assets.

### 8. Examine Ownership And Governance

Review promoter, insider, sponsor, institution, mutual-fund, or public ownership
depending on the market. Check ownership changes, pledge, dilution, warrants,
preferential allotments, related-party concerns, auditor issues, regulatory
issues, and major governance events. Treat a prominent investor's presence as
context, never validation.

### 9. Understand The Present

Review the latest 8-12 comparable quarters when available. Measure YoY revenue,
operating profit, PAT, EPS, and margin changes. Use sequential comparisons only
when seasonality and reporting bases permit. Compare the current rate with the
long-term pattern and identify whether recent revenue or operating profit is at
a meaningful high or low.

Classify the trajectory as `Accelerating`, `Stable`, `Slowing`, `Recovering`, or
`Deteriorating`.

### 10. Identify Seasonality And Earnings Quality

Compare corresponding quarters across years. Identify strong and weak periods
and industry drivers such as monsoons, festivals, commodity prices, travel,
holiday demand, policy cycles, or financial-year-end activity. Do not annualize
one quarter when it is seasonal or exceptional.

Review effective tax, other income, exceptional gains/losses, interest,
depreciation, minority interest, and share-count changes. State whether reported
profit appears operating and repeatable.

### 11. Evaluate Valuation

Use the measures suited to the business: trailing and normalized forward P/E,
price-to-book, EV/EBITDA, price-to-operating-cash-flow, free-cash-flow yield, or
market-cap-to-sales. Compare with growth, quality, cyclicality, leverage,
historical range, and relevant peers.

Only estimate forward P/E when a normalized earnings base is defensible:

```text
Estimated annual EPS = normalized quarterly EPS x 4
Forward P/E = current share price / estimated annual EPS
```

Do not annualize a seasonal or exceptional quarter. Do not call a stock cheap
from low P/E alone; peak earnings, stagnation, cyclicality, or governance risk
may explain the multiple.

Classify valuation as `Deeply discounted`, `Moderately discounted`, `Fair`,
`Expensive`, `Very expensive`, or `Not meaningful because earnings are abnormal`.

### 12. Search For Future Clues

Read the latest management remarks and, when available, the transcript, investor
presentation, annual report, filings, rating report, or credible industry
source. Extract guidance on revenue, margins, capacity, capex, order book,
products, customers, geographies, pricing, demand, input costs, regulation,
debt, and commissioning dates.

For each material forward statement, separate:

1. `Management claim`: what management said and when.
2. `Supporting evidence`: reported data or execution evidence.
3. `Interpretation`: what the evidence presently supports.

Read surrounding transcript paragraphs, not isolated keyword hits. Search for
`guidance`, `outlook`, `margin`, `capacity`, `capex`, `demand`, `order book`,
`utilization`, `debt`, and `risk`. If a claim is unclear or unusually optimistic,
inspect analyst Q&A. State that full transcript review is still required before
a serious investment decision when only a rapid pass was possible.

### 13. Review Price Behaviour

Use weekly or medium-term evidence to reduce daily noise. Review direction,
52-week position, distance from major highs, consolidation or breakdown, and
reaction after results. Classify as `Strong uptrend`, `Consolidation`, `Early
breakout`, `Weakening`, or `Downtrend`. State when exact price-history evidence
is unavailable.

### 14. Build The Past-Present-Future View

Summarize:

- `Past`: growth, margins, capital returns, debt, cash conversion, ownership
- `Present`: quarterly direction, margins, valuation, and price behaviour
- `Future`: guidance, capacity/order visibility, catalysts, execution, and
  external risks

Do not allow one attractive number to dominate the disposition.

### 15. Match The Investor Style

Apply the requested lens:

- `Value`: normalized valuation, balance-sheet safety, reversible problems,
  downside protection, and a credible recovery trigger
- `Growth`: acceleration, addressable market, sustainable capacity, margins,
  and valuation relative to growth
- `Quality`: stable ROCE, cash conversion, low debt, predictability,
  governance, and reinvestment runway
- `Turnaround`: prior deterioration, early improvement, debt reduction,
  utilization, margin normalization, and catalyst visibility
- `Momentum`: earnings acceleration, revisions when confirmed, relative price
  strength, result-backed breakouts, and liquidity

A good company can still be unsuitable for the requested style.

## Required Output

Follow the transcript's analysis order. Keep the default report concise enough
to function as a first-pass filter, normally 600-900 words. Do not append a
citations or references section unless the user explicitly asks for sources or
the hosting agent requires citations.

### Header

```text
Quick Stock Analysis: [Company] ([Ticker])
Analysis date:
Market / exchange:
Investor style:
```

### 1. Business

```text
Business in one line:
Main segments:
Geographic exposure:
Business character:
```

Explain only enough to establish how the company earns money and what drives
its economics.

### 2. Past

#### P&L

| Check | Read | Interpretation |
|---|---|---|
| Revenue trend | 3/5/10-year growth | Consistent / cyclical / stagnant / recovering |
| Operating margin | Normal range and current level | Stable / expanding / contracting / volatile |
| Profit growth | Operating profit, PAT, EPS | Revenue-led / margin-led / affected by other items |
| Returns | ROE and ROCE | Strong / acceptable / weak for this business |
| Price CAGR | Available long-term periods | Wealth creation context, not a forecast |

#### Balance Sheet

State the change in total balance-sheet size, debt level, debt-to-equity,
fixed-asset growth, and CWIP or equivalent growth assets. Explain whether
capital is being deployed into growth and whether revenue has kept pace.

#### Cash Flow

```text
Operating cash flow versus PAT + depreciation:
Operating cash-flow trend:
Receivables:
Cash-conversion read:
```

#### Ratios And Shareholding

```text
ROE / ROCE trend:
Promoter / insider / sponsor holding and recent change:
Pledge or insider selling:
Institutional ownership trend:
Notable ownership change:
```

End the section with:

```text
Past read:
Strong / Above average / Average / Weak

Flags to remember:
- [Only the important observations carried into the next stages]
```

### 3. Present

Compare the latest quarters with the historical pattern.

| Check | Current read | What it means |
|---|---|---|
| Revenue growth | Latest YoY and recent range | Accelerating / maintaining / slowing |
| Operating profit | Growth and recent high/low | Operating momentum |
| Margin | Latest versus normal range | Structural change or normalization |
| Seasonality | Strong/weak quarters | Whether annualization is valid |
| Tax and earnings quality | Normal / unusual | Repeatability of PAT/EPS |
| Valuation | Suitable current multiples | Cheap / fair / expensive in context |

Then state:

```text
Present read:
Accelerating / Stable / Slowing / Recovering / Deteriorating
```

### 4. Future Clues

Summarize what management indicates about revenue, margins, profitability,
capacity, capex, order book, demand, and material external triggers.

| Topic | Management indication | Analyst read |
|---|---|---|
| Revenue / demand | | |
| Margins / profitability | | |
| Capex / capacity / order book | | |
| External trigger or risk | | |

Do not convert management optimism into a forecast. State what requires a full
transcript, presentation, filing, or later result check.

### 5. Price Action

```text
Weekly trend:
Position versus 52-week/all-time high:
Consolidation, breakout, or breakdown:
Agreement with fundamentals:
```

Use price action as an input to the screen, not a prediction.

### 6. Strategy Fit

State briefly how the company fits or fails the requested investor style.

```text
Best-fit style:
Why it may fit:
Why it may not fit:
```

### 7. Triage Conclusion

```text
Decision:
Proceed to deeper research / Watchlist / Reject for now / Insufficient data

Core reason:
[One or two sentences]

Key positives:
- [Two or three points]

Key flags:
- [Two or three points]

What to investigate next:
- [Specific unanswered questions or documents]
```

## Humanizing Final Pass

Before returning, read and apply
[references/content-humanizer.md](references/content-humanizer.md) to the
completed report. Preserve every figure, period, classification, caveat, and
triage decision. Return only the edited report and stop after the triage
conclusion.
