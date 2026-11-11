# TFVC Quickstart (macOS)

Purpose: fast path only. For complete explanations, manual command variants, and troubleshooting, use [TFVC_SETUP_MAC.md](TFVC_SETUP_MAC.md).

## Working directory and wrapper path

Start in the `tfvc-macos` bundle directory (the folder containing `scripts/`), then capture the wrapper path before changing to the mapped workspace:

```zsh
export TFVC_WRAPPER_PATH="$PWD/scripts/tfvc-wrapper.sh"
```

The mapped source directory does not contain this bundle’s scripts. Re-establish this absolute path in each new shell before using the commands below.

Before continuing, complete the [prerequisites](TFVC_SETUP_MAC.md#prerequisites). Replace placeholder values with your own; the bootstrap does not install prerequisites.

Before running bootstrap, validate that `TFVC_SERVER_PATH` begins with `$/` and inspect the existing workspace mappings using the wrapper’s `workfold` command. Reuse an existing matching mapping; if the mapping differs, stop and request explicit authorization before replacing it. Bootstrap does not enforce these checks itself.

## 1) Run this setup block

```zsh
# Required
export TFVC_COLLECTION_URL="https://dev.azure.com/<org>"
export TFVC_SERVER_PATH="$/<team-project-path>"

# Workspace defaults (keep these exported for the verification commands)
export TFVC_ROOT_DIR="$HOME/tfvc-workspace"
export TFVC_MAPPED_DIR="$TFVC_ROOT_DIR/src"
export TFVC_WORKSPACE_NAME="TFVC-Mac"
export TFVC_JAVA_HOME="/Library/Java/JavaVirtualMachines/<x64-jdk>.jdk/Contents/Home"
export TFVC_USERNAME="<azure-devops-username>"
export TFVC_PASSWORD="<azure-devops-password-or-pat>"
# Alternative single variable format (overrides USERNAME/PASSWORD):
# export TFVC_LOGIN="<azure-devops-username>,<azure-devops-password-or-pat>"
chmod +x ./scripts/tfvc-wrapper.sh ./scripts/bootstrap-tfvc-workspace.sh
./scripts/bootstrap-tfvc-workspace.sh
```

## 2) Validate with 3 commands

```zsh
"$TFVC_WRAPPER_PATH" workspaces -collection:"$TFVC_COLLECTION_URL"
```

```zsh
"$TFVC_WRAPPER_PATH" workfold -collection:"$TFVC_COLLECTION_URL" -workspace:"$TFVC_WORKSPACE_NAME"
```

```zsh
cd "$TFVC_MAPPED_DIR"
"$TFVC_WRAPPER_PATH" status . -recursive
```

For full detail and troubleshooting, read [TFVC_SETUP_MAC.md](TFVC_SETUP_MAC.md).
