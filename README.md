# BugSplat plugins

Plugins that connect coding agents to [BugSplat](https://www.bugsplat.com) crash reporting.

The `bugsplat` plugin adds:

- **BugSplat's hosted MCP server** at `https://api.bugsplat.com/mcp`. You sign in with your BugSplat account, and your agent can read crash reports, crash groups, versions and symbol files in every database your login can open, file crash groups as defects in your issue tracker, and mark them fixed. See [MCP Server](https://docs.bugsplat.com/api-reference/mcp-server).
- **`/bugsplat:triage`** works out which crash to fix next. It ranks an application's crash groups by crashes, affected users, how recently they appeared and how fast they are growing, and gives the evidence for each.
- **`/bugsplat:fix`** picks a crash group (or takes the one you name), reads its call stack, finds the code in your repository, fixes it, opens a pull request, and offers to mark the group fixed.
- **`/bugsplat:integrate`** adds BugSplat to an application: it picks the SDK for your platform, sets up symbol upload, and confirms a test crash arrived.

## Claude Code

```bash
claude plugin marketplace add BugSplat-Git/bugsplat-plugins
claude plugin install bugsplat@bugsplat
```

Then run `/mcp`, select the BugSplat server, and sign in to BugSplat in the browser window that opens.

## Codex

```bash
codex plugin marketplace add BugSplat-Git/bugsplat-plugins
codex plugin add bugsplat@bugsplat
```

If Codex doesn't open BugSplat's sign-in page the first time it uses the server, run `codex mcp login bugsplat`.

## Cursor

Until the plugin is listed in the Cursor Marketplace, copy `plugins/bugsplat` to `~/.cursor/plugins/local/bugsplat` and run **Developer: Reload Window**. Then open **Settings > Tools & MCP**, click **Connect** next to BugSplat, and sign in.

## Layout

`plugins/bugsplat` follows the [Agent Plugins](https://agent-plugins.org) standard (`plugin.json`, `mcp.json`, `skills/`), which Codex and Cursor read, with Codex's listing details under `extensions.com.openai`. `.claude-plugin/` and `.mcp.json` are for Claude Code, and `.cursor-plugin/plugin.json` adds Cursor's logo. The marketplaces are `.claude-plugin/marketplace.json` (Claude Code), `.agents/plugins/marketplace.json` (Codex) and `.cursor-plugin/marketplace.json` (Cursor).

## License

MIT
