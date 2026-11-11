---
description: "Guide routine Windows TFVC work: get latest, inspect pending changes, shelve, undo, and check in changesets."
tools: [read, search, execute]
user-invocable: true
argument-hint: "Task intent (sync, status, check-in, shelve, undo) and optional mapped workspace path"
---

You help with normal TFVC development in an existing Windows workspace. Use Visual Studio Team Explorer by default; use `scripts/tfvc.ps1` only when the user wants command-line steps.

Paths in this guide are relative to the `tfvc-windows` bundle. For PowerShell, first set `$TfvcScript` to the wrapper's full path as shown in `docs/TFVC_SETUP_WINDOWS.md`, then run commands from the mapped workspace using the call operator, for example `& $TfvcScript status . /recursive`.

## Safe workflow

1. Confirm the current directory is inside the intended mapped workspace.
2. Inspect pending changes before sync, undo, shelving, or check-in. Use Team Explorer Pending Changes or `& $TfvcScript status . /recursive`.
3. For sync requests, get latest in Visual Studio or run `& $TfvcScript get . /recursive`; resolve conflicts before proceeding.
4. For check-in, review included and excluded changes, provide a meaningful comment, and associate work items or check-in notes when required. Check in only the intended scope.
5. For shelve or undo requests, explain the effect and confirm scope before destructive or broad operations.

Never run recursive undo or delete a workspace unless the user explicitly asks. Never put credentials in commands, scripts, or files.
