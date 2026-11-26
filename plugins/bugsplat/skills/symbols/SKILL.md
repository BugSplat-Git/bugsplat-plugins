---
name: symbols
description: Find the symbol files a BugSplat crash needs on this machine and upload them with the BugSplat CLI, then reprocess the crash to confirm its call stack shows function names and line numbers. Use when a crash report shows frames like `MyApp+0x12b5`, when the user asks why a call stack has no function names, or when they ask to find, check or upload symbols for a version.
argument-hint: "[crash id | application and version] [build folder]"
---

# Find and upload symbols for a BugSplat crash

The user asked: $ARGUMENTS

This skill uses the BugSplat CLI, `bugsplat`, which reads symbol files on this machine and uploads them. BugSplat's MCP tools (`get-report`, `list-symbols`, `reprocess-report`) do the same reads as `bugsplat reports get` and `bugsplat symbols list`; use whichever you have.

## 1. Check the CLI

- Run `bugsplat --version`. If the command is missing, ask before installing it with `npm install -g @bugsplat/cli`.
- Run `bugsplat whoami`. Exit code 3 means nobody is signed in: ask the user to run `bugsplat login`, which opens their browser. On a machine with no browser, the CLI signs in with the `BUGSPLAT_CLIENT_ID` and `BUGSPLAT_CLIENT_SECRET` environment variables. Never ask the user to paste a secret into the conversation.
- Every command below takes the database with `-b <database>`. `bugsplat databases list` shows the ones the user can open.

## 2. Work out what is missing

- From a crash id: read the report (`bugsplat reports get <id> -b <database>`). Note the application, the version, and the modules whose frames show only an offset, such as `MyApp+0x12b5`. Those modules have no matching symbols.
- From an application and version: that is enough to continue.
- List what BugSplat already has: `bugsplat symbols list -b <database> --application <app> --version <version>`. Each file is listed with its debug id. A module can be on the list and still not match: a rebuild of the same version has a different debug id.

## 3. Find the files on this machine

A crash needs the symbol files from exactly the build that crashed. Look in that version's build output, and ask the user where it is if the repository doesn't make it obvious.

- By name: `bugsplat symbols find --name '<module>*' --path <build folder>`. Repeat `--name` and `--path` for several.
- By debug id, when you know it: `bugsplat symbols find --debug-id <id> --path <build folder>`. A match by debug id is proof the file is from the right build.
- `bugsplat symbols info <file>...` prints any file's module name, debug id and type.

The command exits with 1 when something you asked for was not found. If the right build is not on this machine, say so and stop: symbols from another build of the same version will not fix the call stack. Tell the user where builds usually keep them, such as the CI job's artifacts for that version.

When you find files by name only, tell the user you can't prove they are from the crashing build, and show the debug ids and file dates you found.

## 4. Upload

- Show what would be uploaded first: `bugsplat symbols upload -b <database> -a <app> -v <version> -d <build folder> -f "<glob>" --dry-run`. The glob selects files under the folder, for example `"**/*.{pdb,exe,dll}"` on Windows or `"**/*.dSYM"` on macOS. For the right files on other platforms, read https://docs.bugsplat.com/guides/working-with-symbol-files.
- Use the application name and version the crash reports, exactly as they appear in the report.
- If the build uploads Breakpad `.sym` files, add `--dump-syms` to generate one from each binary and upload that.
- Show the user the dry-run list and ask before running the same command without `--dry-run`. Uploading changes the user's BugSplat database.
- Do not delete symbols as part of this skill.

## 5. Confirm it worked

- Ask before reprocessing: `bugsplat reports reprocess <id> -b <database>` asks BugSplat to process the crash again with the new symbols. Don't add `--force` unless the user asks for it.
- Read the report again until its Processing line no longer says it is still processing.
- The call stack should now show function names and line numbers for the modules you uploaded. If a module still shows only offsets, its file was not from the crashing build; say which module, and go back to step 3.

## 6. Keep it from happening again

If the symbols were missing because the build never uploads them, offer to add `bugsplat symbols upload` to the build or CI job. It reads `BUGSPLAT_DATABASE`, `BUGSPLAT_CLIENT_ID` and `BUGSPLAT_CLIENT_SECRET` from the environment, so no secret goes in the repository.
