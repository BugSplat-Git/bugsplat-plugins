---
name: fix
description: Fix a crash reported to BugSplat. Picks a crash group (or takes one you name), reads its crash reports and call stack through the BugSplat MCP server, finds the code in this repository, fixes it, opens a pull request, and offers to mark the crash group fixed. Use when the user asks to fix a BugSplat crash, a crash group, or "what's crashing" in their app.
argument-hint: "[crash group id | crash id | BugSplat link | application and version]"
---

# Fix a BugSplat crash

The user asked: $ARGUMENTS

You have BugSplat's MCP tools (`list-databases`, `list-groups`, `get-group`, `list-reports`, `get-report`, `list-versions`, `list-symbols`, `create-defect`, `add-defect-link`, `set-group-status`). If they aren't available, tell the user to connect the server (`/mcp`, then sign in to BugSplat) and stop.

## 1. Pick the crash group

- A crash group id, or a `app.bugsplat.com` link with a group: call `get-group` with that `stackKeyId`.
- A crash id, or a link to one crash: call `get-report`, then `get-group` for its crash group.
- An application and version, or nothing: find the database first. If `list-databases` shows more than one and the repository doesn't make the choice obvious, ask which one. Call `list-versions` to find the newest version of this application, then `list-groups` for that version with `status` `{"in": ["open", "regression"]}`, sorted by `users`. Show the top few groups (stack key, crashes, users, linked defect) and ask which to fix, unless the user asked you to pick.

Skip groups that already link to a defect someone is working on unless the user picked that group.

## 2. Read the crash

- Call `get-group` with a `pageSize` of 5 to see recent reports, then `get-report` for the newest one, and for one or two older ones if the stacks might differ.
- Read the exception, the crashing thread's frames, the custom attributes, and the user comments.
- If the top frames show only a module and an offset (for example `MyApp+0x12b5`) instead of function names and line numbers, the symbols for that version are missing or don't match the build. Call `list-symbols` for the application and version, tell the user which files are missing, point them to https://docs.bugsplat.com/guides/working-with-symbol-files/upload-symbols-with-symbol-upload, and stop: guessing a fix from an unsymbolicated stack is not reliable.
- The report's Crash Archive link downloads the zip the app uploaded (minidump, logs, other attachments). It expires 15 minutes after `get-report` returns. Download it only when the stack and attributes aren't enough, and only into a temporary directory outside the repository.

## 3. Find the code

- Search this repository for the functions and files named in the crashing frames, starting from the first frame that belongs to the user's code rather than the system or a third-party library.
- Read the code on the current branch. If a report attribute or the version names a commit or tag, compare it with the current branch and tell the user when the crashing version is far behind.
- If the frames point to code that isn't in this repository, say where it lives and stop.

## 4. Fix it

- Explain the cause in two or three sentences, quoting the frames and the code that prove it.
- Make the smallest change that removes the cause. Don't refactor nearby code.
- Add or update a test that reproduces the crash when the project has tests for that area.
- Run the project's build and tests and report the real results.

## 5. Open a pull request

- Work on a new branch, never on the default branch.
- Ask before pushing or opening the pull request. In its description, link the crash group (`https://app.bugsplat.com/v2/group?stackKeyId=<id>&database=<database>`) and give the crash and user counts.

## 6. Update BugSplat

Ask before each of these; they change data outside the repository.

- If the group has no linked defect and the team tracks work in an issue tracker, offer `create-defect` with notes that link the pull request, or `add-defect-link` for an issue that already exists.
- Once the fix is merged or shipped, offer `set-group-status` with `fixed`. If the crash happens again in a newer version, BugSplat marks the group Regression.
