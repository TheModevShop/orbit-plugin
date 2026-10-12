# Orbit for Restaurants: Claude, ChatGPT and Codex

[Orbit](https://orbit.modevshop.com) is restaurant and inn intelligence built on the business's own data:
Toast sales and bookings, labor, Cloudbeds, QuickBooks and Google Analytics. This repository is the Orbit
plugin: the Orbit connector (you sign in with Orbit and choose your restaurant; there is no key to copy) and
Orbit's skills.

| Skill | What it does |
|---|---|
| `orbit-daily-research` | Checks Orbit's numbers, looks at the week ahead and writes the morning brief |
| `orbit-asks` | Answers what your team asked Orbit to look into |
| `orbit-review-research` | Reads your reviews on Google, Yelp and Tripadvisor and flags the ones to answer |
| `orbit-pay-research` | Reads local job postings so you can see their pay beside yours |
| `orbit-market-research` | Reads comparable inns' prices and suggests yours, night by night |

Your AI can read your numbers and post briefs, notes, questions and research to your Orbit. It can't change
anything in Toast, Cloudbeds or QuickBooks, or move money, and guests' contact details are never shared.
Disconnect it any time in Orbit → Settings → Connections.

## Install

**Claude Code**

```bash
claude plugin marketplace add TheModevShop/orbit-plugin
claude plugin install orbit@orbit
```

Then run `/mcp`, choose orbit → Authenticate, and sign in to Orbit.

**Codex**

```bash
codex plugin marketplace add TheModevShop/orbit-plugin
codex plugin add orbit@orbit
```

Then `codex mcp login orbit` (or sign in when Codex asks).

**Claude (web, desktop, phone)**: Customize → Connectors → Add → Add custom connector, with the address
`https://orbit.modevshop.com/mcp/orbit`. Add the skills in Customize → Skills → Upload a skill, from
`https://orbit.modevshop.com/skills/<name>.zip`.

**ChatGPT**: Plugins → + → Add custom MCP server, with the address `https://orbit.modevshop.com/mcp/orbit`
and OAuth.

Then ask: "Run the Orbit daily research."

## About

Orbit is made by The Modev Shop, LLC · [Privacy](https://modevshop.com/orbit/privacy/) ·
[Terms](https://modevshop.com/orbit/terms/) · info@modevshop.com

This repository is published from Orbit's own build; changes made here directly are overwritten.
