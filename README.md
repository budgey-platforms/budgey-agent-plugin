# Budgey Agent Plugin

Connect compatible agent clients — [Grok Build](https://x.ai/news/grok-build-cli), Codex, ChatGPT, Cursor, GitHub Copilot, Kiro, VS Code, and others — to your [Budgey](https://budgeyapp.com) budget.

**Homepage:** [https://www.budgeyapp.com/developers](https://www.budgeyapp.com/developers)
**License:** [MIT](LICENSE)
**Author:** [Budgey Platforms, LLC](https://budgeyapp.com)

This repository is packaged for both [agent-plugins.org](https://agent-plugins.org) (`plugin.json` + `mcp.json`) and the [xAI Grok Build marketplace](https://github.com/xai-org/plugin-marketplace) (`.claude-plugin/plugin.json` / `.grok-plugin/plugin.json` + `.mcp.json`). Both formats point at the same hosted MCP endpoint — there is no local server.

## Authentication and network

**OAuth to Budgey is required.** On first use the client opens a browser so you can sign in to Budgey. This plugin does not ship secrets, API keys, or credentials.

- **Network endpoint:** [`https://www.budgeyapp.com/mcp`](https://www.budgeyapp.com/mcp)
- **Transport:** Streamable HTTP (OAuth 2.1 with dynamic client registration)
- Clients without OAuth can use a personal API key (`budgey_sk_…`) from [budgeyapp.com/account/agents](https://www.budgeyapp.com/account/agents)

## Install

### Grok Build (once catalogued)

After this plugin is listed in the [official xAI plugin marketplace](https://github.com/xai-org/plugin-marketplace):

1. In Grok Build, run `/plugin`
2. Search for **Budgey**
3. Install, then complete the Budgey OAuth sign-in when prompted

### Other agent clients

Add this repository as an agent plugin (most clients accept the git URL or a local checkout):

```
https://github.com/budgey-platforms/budgey-agent-plugin
```

## Discovery (Grok CTA)

Brand-scoped keywords and domains used to suggest this plugin — not generic terms like budgeting or personal-finance:

- **Keywords:** `budgey`, `budgeyapp`
- **Domains:** `budgeyapp.com`, `www.budgeyapp.com`

## What's inside

- `mcp.json` / `.mcp.json` — remote MCP config for [`https://www.budgeyapp.com/mcp`](https://www.budgeyapp.com/mcp)
- `.claude-plugin/plugin.json` / `.grok-plugin/plugin.json` — Grok Build / Claude-compatible manifests (MCP, skills path, keywords, CTA domains)
- `plugin.json` — agent-plugins.org manifest
- `skills/budgeting-with-budgey/` — teaches agents Budgey's paycheck-period model and safe tool workflows

## What agents can do

See what's left this period, log and search spending, manage categories, goals, and recurring bills — everything BaoBot can do in the app, from whatever agent you already use. Docs: [budgeyapp.com/developers](https://www.budgeyapp.com/developers).

## License

[MIT](LICENSE) © Budgey Platforms, LLC
