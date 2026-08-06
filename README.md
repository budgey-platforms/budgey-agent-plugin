# Budgey Agent Plugin

An [Agent Plugin](https://agent-plugins.org) (v1.0.0 spec) that connects
compatible agent clients — Codex, ChatGPT, Cursor, GitHub Copilot, Kiro,
VS Code, and others — to your [Budgey](https://budgeyapp.com) budget.

## Install

Add this repository as an agent plugin in your client (most clients accept
the git URL or a local checkout):

```
https://github.com/erichie/budgey-agent-plugin
```

On first use, your client will open a browser window to sign in to Budgey —
that's it. Clients without OAuth support can use a personal API key
(`budgey_sk_…`) from [budgeyapp.com/account/agents](https://www.budgeyapp.com/account/agents)
instead.

## What's inside

- `mcp.json` — points your client at Budgey's remote MCP server
  (`https://www.budgeyapp.com/mcp`, Streamable HTTP, OAuth 2.1 with dynamic
  client registration).
- `skills/budgeting-with-budgey/` — an Agent Skill that teaches agents
  Budgey's paycheck-period model and safe tool workflows.

## What agents can do

See what's left this period, log and search spending, manage categories,
goals, and recurring bills — everything BaoBot can do in the app, from
whatever agent you already use. Docs: [budgeyapp.com/developers](https://www.budgeyapp.com/developers).
