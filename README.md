# Drishti Skills


Reusable agent skills for researching Indian listed companies with Drishti MCP.
The workflows keep reported facts, management commentary, and analyst
interpretation separate. They do not provide buy, sell, hold, allocation, or
target-price advice.

## Included Skills

| Skill | Use it for |
|---|---|
| `quick-stock-analysis` | A 10-15 minute first pass across the business, financial history, recent results, ownership, valuation, price behaviour, risks, catalysts, and investor-style fit. It returns `Proceed to deeper research`, `Watchlist`, `Reject for now`, or `Insufficient data`. |
| `earnings-analysis` | A focused review of an Indian company's reported quarter, including YoY/QoQ performance, margins, segment drivers, concall commentary, risks, and market reaction. |
| `event-calendar` | Date-sorted Indian-equity calendars covering earnings, concalls, board meetings, corporate actions, investor updates, and material filings. |

`quick-stock-analysis` includes its own lightweight humanizing pass. No separate
writing or humanizer skill is required.

## Install

Install every skill in the repository:

```bash
npx skills add manasijatech/drishti-skills
```

See the skills available before installing:

```bash
npx skills add manasijatech/drishti-skills --list
```

Install one skill:

```bash
npx skills add manasijatech/drishti-skills --skill quick-stock-analysis
```

Replace `quick-stock-analysis` with `earnings-analysis` or `event-calendar` to
install either workflow individually.

## Requirements

- Connect Drishti MCP before researching an Indian listed company. The skills
  read the active Drishti data instructions before choosing tools and do not
  assume a fixed tool schema.
- Provide a company name or ticker. Add the exchange or another identifier when
  the security may be ambiguous.
- For `quick-stock-analysis`, make a fundamentals source available for
  multi-year financial statements and ratios. Drishti supplies current company,
  filing, earnings, concall, news, event, and market context.
- Expect missing data to be stated plainly. The skills do not fill gaps from
  model memory.

## Example Prompts

```text
Use $quick-stock-analysis for TCS as a general investor.
```

```text
Use $earnings-analysis to explain the latest reported quarter for INFY, including margins and concall commentary.
```

```text
Use $event-calendar to list upcoming earnings, concalls, and board meetings for HDFCBANK and ICICIBANK over the next 30 days.
```

## Repository Layout

```text
drishti-skills/
|-- quick-stock-analysis/
|-- earnings-analysis/
|-- event-calendar/
|-- skills.sh.json
`-- README.md
```

`skills.sh.json` controls how the workflows are grouped on skills.sh.
