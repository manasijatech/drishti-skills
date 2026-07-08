# Drishti MCP Skills

[![skills.sh](https://skills.sh/b/manasijatech/drishti-mcp-skills)](https://skills.sh/manasijatech/drishti-mcp-skills)

Reusable agent skills for Indian-equity research workflows powered by Drishti MCP.

## Skills

- `earnings-deep-dive`: analyze reported Indian listed company earnings with filings, concalls, announcements, news, and event context.
- `event-calendar`: build sourced Indian-equity event calendars across earnings, concalls, board meetings, corporate actions, and filings.
- `upcoming-earnings-calendar`: build upcoming Indian-equity earnings/result schedules with source confidence and market context.

## Install

Install all skills from this repository:

```bash
npx skills add manasijatech/drishti-skills
```

List available skills without installing:

```bash
npx skills add manasijatech/drishti-mcp-skills --list
```

Install a specific skill:

```bash
npx skills add manasijatech/drishti-mcp-skills --skill earnings-deep-dive
```

## skills.sh

The `skills.sh.json` file controls how this repository appears on skills.sh. It groups the skill slugs into a Drishti market workflow section and keeps any future ungrouped skills at the bottom of the repo page.
