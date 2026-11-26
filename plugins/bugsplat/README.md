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

## Data

The plugin has no data store of its own. Its tools return crash data from your own BugSplat databases with your own permissions. A crash report can include a user name, an email address and an IP address when your application sends them, along with user-written descriptions, custom attributes and attachments.

One skill writes crash data to your machine: when a call stack isn't enough, `/bugsplat:fix` can download a crash's archive (the minidump, logs and other attachments your application uploaded) into a temporary folder outside your repository. It deletes the folder when it has finished with it.

The BugSplat MCP server is the plugin's only connector. The skills also send data to these services, each after asking you:

- **`/bugsplat:fix`** pushes a branch and opens a pull request on your git host, such as GitHub or GitLab. The pull request's description links the BugSplat crash group and gives its crash and user counts.
- **`/bugsplat:fix`** and **`/bugsplat:triage`** can file a crash group as a defect, or link it to an existing one, in the issue tracker connected to your BugSplat database. The request goes through the MCP server, and BugSplat sends the crash group's details and your notes to that tracker.
- **`/bugsplat:symbols`** uploads the symbol files you choose to BugSplat with the BugSplat CLI, and asks BugSplat to process a crash report again.
- **`/bugsplat:integrate`** has your application send a test crash to BugSplat, and uploads the application's symbol files to BugSplat.

The skills also download BugSplat documentation from docs.bugsplat.com, the BugSplat CLI from npm, and BugSplat SDKs from your platform's package registry. No skill sends data to any other service.

## License

MIT
