# Orbit

Your restaurant's Orbit in your AI. The plugin adds the Orbit connector (you sign in with Orbit and choose
your restaurant; there is no key to copy) and Orbit's skills:

- **orbit-daily-research**: checks Orbit's numbers, looks at the week ahead and writes the morning brief.
- **orbit-asks**: answers what your team asked Orbit to look into.
- **orbit-review-research**, **orbit-pay-research**, **orbit-market-research**: reviews, local pay and
  competitors' prices, posted to your Orbit.

Your AI can read your numbers and post briefs, notes, questions and research to Orbit. It can't change
anything in Toast, Cloudbeds or QuickBooks, or move money, and guests' contact details are never shared.
Disconnect it any time in Orbit → Settings → Connections.

## Install

- **Claude Code**: `claude plugin marketplace add TheModevShop/orbit-plugin`, then `claude plugin install orbit@orbit`,
  then `/mcp` → orbit → Authenticate.
- **Claude (web, desktop, phone)**: Customize → Connectors → Add → Add custom connector →
  `https://orbit.modevshop.com/mcp/orbit`. Add the skills in Customize → Skills → Upload a skill, from
  `https://orbit.modevshop.com/skills/<name>.zip`.
- **ChatGPT**: chatgpt.com/plugins → + → Add custom MCP server → `https://orbit.modevshop.com/mcp/orbit`, OAuth.
- **Codex**: `codex plugin marketplace add TheModevShop/orbit-plugin`, then `codex plugin add orbit@orbit`, then
  `codex mcp login orbit`.

Then ask: "Run the Orbit daily research."

Orbit is made by The Modev Shop, LLC · [Privacy](https://modevshop.com/orbit/privacy/) ·
[Terms](https://modevshop.com/orbit/terms/) · info@modevshop.com
