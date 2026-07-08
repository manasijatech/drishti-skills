# Drishti Skills

[![skills.sh](https://skills.sh/b/manasijatech/drishti-skills)](https://skills.sh/manasijatech/drishti-skills)

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
npx skills add manasijatech/drishti-skills --list
```

Install a specific skill:

```bash
npx skills add manasijatech/drishti-skills --skill earnings-deep-dive
```

## skills.sh

The `skills.sh.json` file controls how this repository appears on skills.sh. It groups the skill slugs into a Drishti market workflow section and keeps any future ungrouped skills at the bottom of the repo page.
