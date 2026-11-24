# BugSplat plugins

Plugins that connect coding agents to [BugSplat](https://www.bugsplat.com) crash reporting.

The `bugsplat` plugin adds:

- **BugSplat's hosted MCP server** at `https://api.bugsplat.com/mcp`. You sign in with your BugSplat account, and your agent can read crash reports, crash groups, versions and symbol files in every database your login can open, file crash groups as defects in your issue tracker, and mark them fixed. See [MCP Server](https://docs.bugsplat.com/api-reference/mcp-server).
- **`/bugsplat:fix`** picks a crash group (or takes the one you name), reads its call stack, finds the code in your repository, fixes it, opens a pull request, and offers to mark the group fixed.
- **`/bugsplat:integrate`** adds BugSplat to an application: it picks the SDK for your platform, sets up symbol upload, and confirms a test crash arrived.

## Claude Code

```bash
claude plugin marketplace add BugSplat-Git/bugsplat-plugins
claude plugin install bugsplat@bugsplat
```

Then run `/mcp`, select the BugSplat server, and sign in to BugSplat in the browser window that opens.

## License

MIT
