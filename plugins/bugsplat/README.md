# BugSplat

Connects your coding agent to [BugSplat](https://www.bugsplat.com) crash reporting. The plugin adds BugSplat's hosted MCP server and four skills.

You sign in with your BugSplat account, and the agent has the access you have in the BugSplat web app: it reads crash reports, crash groups, versions and symbol files in every database your login can open, files crash groups as defects in your issue tracker, and marks them fixed. Filing defects needs an [issue tracker connected to your BugSplat database](https://docs.bugsplat.com/integrations/issue-trackers).

## Skills

- **`/bugsplat:triage`** works out which crash to fix next. It ranks crash groups by crashes, affected users, how recently they appeared and how fast they are growing, and gives the evidence for each.
- **`/bugsplat:fix`** takes a crash group, reads its call stack, finds the code in your repository, fixes it, opens a pull request, and offers to mark the group fixed.
- **`/bugsplat:symbols`** finds the symbol files a crash needs on your machine and uploads them with the [BugSplat CLI](https://docs.bugsplat.com/api-reference/bugsplat-cli) (`npm install -g @bugsplat/cli`).
- **`/bugsplat:integrate`** adds BugSplat to an application: it picks the SDK for your platform, sets up symbol upload, and confirms a test crash arrived.

## MCP server

`https://api.bugsplat.com/mcp`, described at https://docs.bugsplat.com/api-reference/mcp-server. Sign-in is OAuth in your browser; the plugin stores no credentials.

## License

MIT
