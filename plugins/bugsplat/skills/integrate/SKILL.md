---
name: integrate
description: Add BugSplat crash reporting to an application. Picks the right BugSplat SDK for the project, wires it in, sets up symbol upload, and confirms a test crash reached the user's BugSplat database. Use when the user asks to add, set up, or integrate BugSplat or crash reporting in an app.
argument-hint: "[platform or framework, if not obvious from the project]"
---

# Add BugSplat to an application

The user asked: $ARGUMENTS

BugSplat's docs are written for coding agents as well as people. Follow them rather than memory: SDK versions, package names and steps change.

## 1. Read the guide for agents

Fetch https://docs.bugsplat.com/getting-started/for-ai-coding-agents.md and follow it in order. It lists the values to ask the developer for, where to download each SDK, how to upload symbols, and how to confirm a crash arrived.

Every docs page is also available as Markdown by adding `.md` to its URL, and https://docs.bugsplat.com/llms.txt lists every page with a one-line description. Use llms.txt to find the integration page for this project's platform or framework.

## 2. Pick the SDK

Look at the project (build files, package manifests, engine project files) to find its platform and framework, then find the matching integration page in llms.txt and read it as Markdown. If the project targets more than one platform, ask which to start with.

## 3. Ask the developer for what only they can provide

Ask for every value the guide lists before you change any code: the database name, the application name and version the app will report, and an OAuth Client ID and Client Secret for symbol upload. The developer creates the database and the OAuth client in the BugSplat web app; don't sign in to BugSplat with their email and password to do it for them. Keep the Client Secret out of the repository.

## 4. Integrate and upload symbols

Follow the platform page to add the SDK and initialize it with the database, application and version. Read the database from the `BUGSPLAT_DATABASE` environment variable, which the app and the `symbol-upload` tool share, so the name stays out of the repository when that fits the project. Add symbol upload to the build or CI as the guide shows, using the same application name and version the app reports.

Adding the upload step to the build is a code change. Running an upload yourself sends the application's symbol files to BugSplat: before you do, tell the developer which files will go to which database, and ask.

## 5. Confirm a crash arrived

A test crash sends a crash report from this machine to the developer's BugSplat database. Say so and ask before you trigger it. Then make the app crash as the platform page's verification step describes. If BugSplat's MCP tools are connected (`list-reports`), list the database's newest reports and find the one whose application, version and crash time match the test; otherwise use the REST API steps in the guide. If the top of its stack is a module and an offset instead of function names, the symbols for that version are missing or don't match the build.

Tell the developer what you changed, what still needs their action (secrets in CI, turning off any test-only settings), and how to open the crash in BugSplat.
