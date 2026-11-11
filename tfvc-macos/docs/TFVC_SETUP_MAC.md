# TFVC Setup on macOS

Purpose: canonical setup and troubleshooting reference for Team Foundation Version Control (TFVC) on macOS.

If you only need the shortest working path, use [QUICKSTART.md](QUICKSTART.md).

## Prerequisites

1. Rosetta 2 installed (Apple Silicon only)
2. Team Explorer Everywhere installed
3. x64 JDK 11 or 8 installed
4. VS Code AutoTFS extension installed (optional)

## TFVC model for Git users

- TFVC is workspace-based and server-centric.
- A workspace maps a server path to a local path.
- `get` syncs latest from server.
- `checkin` publishes pending changes.
- `shelve` uploads pending changes without check-in.

Quick mapping from Git:

- `clone` -> create workspace + map + first `get`
- `pull` -> `tf get`
- `status` -> `tf status`
- `add` -> `tf add`
- `mv` -> `tf rename`
- restore/reset file -> `tf undo`
- commit/push -> `tf checkin`

## Working directory and wrapper path

Start in the `tfvc-macos` bundle directory (the folder containing `scripts/`), then capture the wrapper path before changing to the mapped workspace:

```zsh
export TFVC_WRAPPER_PATH="$PWD/scripts/tfvc-wrapper.sh"
```

The mapped source directory does not contain this bundle’s scripts. Re-establish this absolute path in each new shell before using the commands below.

## Java setup

Set your x64 JDK path:

```zsh
export TFVC_JAVA_HOME="/Library/Java/JavaVirtualMachines/<x64-jdk>.jdk/Contents/Home"
```

## Required environment variables

Before bootstrap, set:

```zsh
export TFVC_COLLECTION_URL="https://dev.azure.com/<org>"
export TFVC_SERVER_PATH="$/<team-project-path>"
```

Workspace defaults (export these for the manual and daily commands below, even when using the defaults). Set credential values as needed:

```zsh
export TFVC_ROOT_DIR="$HOME/tfvc-workspace"
export TFVC_MAPPED_DIR="$TFVC_ROOT_DIR/src"
export TFVC_WORKSPACE_NAME="TFVC-Mac"
export TFVC_USERNAME="<azure-devops-username>"
export TFVC_PASSWORD="<azure-devops-password-or-pat>"
# Alternative single variable format (overrides USERNAME/PASSWORD):
# export TFVC_LOGIN="<azure-devops-username>,<azure-devops-password-or-pat>"
```

Credential behavior in this package:

- If `TFVC_LOGIN` is set, wrapper passes `-login:<value>` to `tf`.
- Else if `TFVC_USERNAME` and `TFVC_PASSWORD` are both set, wrapper passes `-login:<username>,<password>`.
- When wrapper injects login, it also adds `-noprompt` unless you already supplied it.
- Explicit CLI `-login:` or `/login:` arguments take precedence over env vars.

Before running bootstrap, validate that `TFVC_SERVER_PATH` begins with `$/` and inspect the existing workspace mappings using the wrapper’s `workfold` command. Reuse an existing matching mapping; if the mapping differs, stop and request explicit authorization before replacing it. Bootstrap does not enforce these checks itself.

## Bootstrap

```zsh
chmod +x ./scripts/tfvc-wrapper.sh ./scripts/bootstrap-tfvc-workspace.sh
./scripts/bootstrap-tfvc-workspace.sh
```

The script creates/reuses the workspace, maps server to local, and runs recursive `get`.

## Manual equivalents

For a new workspace only; reuse an existing workspace and mapping instead of recreating them.

```zsh
mkdir -p "$TFVC_MAPPED_DIR"
"$TFVC_WRAPPER_PATH" workspace -new -collection:"$TFVC_COLLECTION_URL" -location:server -noprompt "$TFVC_WORKSPACE_NAME"
"$TFVC_WRAPPER_PATH" workfold -map -collection:"$TFVC_COLLECTION_URL" -workspace:"$TFVC_WORKSPACE_NAME" "$TFVC_SERVER_PATH" "$TFVC_MAPPED_DIR"
cd "$TFVC_MAPPED_DIR"
"$TFVC_WRAPPER_PATH" get . -recursive
```

## Daily workflow

```zsh
cd "$TFVC_MAPPED_DIR"
"$TFVC_WRAPPER_PATH" status . -recursive
"$TFVC_WRAPPER_PATH" get . -recursive
```

## Daily command cheat sheet

Run these from your mapped workspace directory unless noted.

```zsh
# Show pending changes
"$TFVC_WRAPPER_PATH" status . -recursive

# Pull latest from server (TFVC equivalent of pull)
"$TFVC_WRAPPER_PATH" get . -recursive

# Add a new file
"$TFVC_WRAPPER_PATH" add <path>

# Rename or move a file/folder
"$TFVC_WRAPPER_PATH" rename <old-path> <new-path>

# Revert a file
"$TFVC_WRAPPER_PATH" undo <path>

# Create a changeset (TFVC equivalent of push)
"$TFVC_WRAPPER_PATH" checkin -recursive -comment:"<comment>" .

# Shelve work instead of checking in
"$TFVC_WRAPPER_PATH" shelve -recursive -comment:"<comment>" <shelveset-name> .
```

Safe habits:

- Run `status` before `checkin`, `undo`, or `shelve`.
- Use meaningful `checkin` comments.
- Avoid bulk undo (`undo . -recursive`) unless you explicitly intend to discard all pending changes.

Check in:

```zsh
"$TFVC_WRAPPER_PATH" checkin -recursive -comment:"<comment>" .
```

Shelve instead:

```zsh
"$TFVC_WRAPPER_PATH" shelve -recursive -comment:"<comment>" <shelveset-name> .
```

## Copying Changes Into TFVC Workspace

When copying files from another repo (for example, a Git mirror) into a TFVC workspace,
target files are often read-only until they are pended for edit.

Use this sequence:

```zsh
# 1) Pend edits on exact files first (makes files writable)
"$TFVC_WRAPPER_PATH" checkout <file1> <file2> ...

# 2) Copy files from source repo into TFVC mapped workspace
# cp/rsync command here

# 3) Verify only intended changes are pending
"$TFVC_WRAPPER_PATH" status <scope-path> -recursive

# 4) Check in only intended files
"$TFVC_WRAPPER_PATH" checkin -comment:"<comment>" <file1> <file2> ...
```

If copy fails with `Permission denied`, run `checkout` on those files and retry.

## Troubleshooting

- Wrapper says tf not found: set `TFVC_TEE_ROOT` to your TEE install directory.
- Java arch error on Apple Silicon: confirm `TFVC_JAVA_HOME` points to an x64 JDK.
- Auth prompts repeat: confirm `TFVC_USERNAME` and `TFVC_PASSWORD` (or `TFVC_LOGIN`) are set, then verify the credential is valid for the target org.
