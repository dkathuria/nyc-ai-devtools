# TFVC macOS Starter Pack

## Purpose
This folder contains a TFVC starter kit for macOS.

## Quick start
1. Copy this folder to your machine and open a terminal in the `tfvc-macos` directory.
2. Check the [prerequisites](docs/TFVC_SETUP_MAC.md#prerequisites). Bootstrap does not install them.
3. Follow [QUICKSTART.md](docs/QUICKSTART.md) to configure your values, capture the absolute wrapper path, and run bootstrap.

## Invoke the agent instructions

Explicitly attach or ask your assistant to read `AGENTS.md` and the relevant file under `skills/`; opening this folder alone does not guarantee agent discovery. For example: “Read `tfvc-macos/AGENTS.md` and `tfvc-macos/skills/tfvc-first-time-setup.agent.md`, then help set up my Mac.” Paths in that example are relative to the repository root.

Use `tfvc-workspace-from-server-url.agent.md` for a new workspace with existing tools, or `tfvc-dev-workflow.agent.md` for an existing mapped workspace. Supply the collection URL and server path for setup, or the mapped local path and intended action for daily work.

## Workspace template

`workspace/TFVC.code-workspace` is a template: replace its `folders[0].path` with your absolute mapped source path and `auto-tfs.tf.path` with the absolute path to this bundle’s wrapper before opening it. The checked-in relative paths assume a sibling `src/` directory, which bootstrap does not create inside this bundle by default. Review the template’s automatic checkout/add/delete/rename/sync settings before use.

## Included files

- `AGENTS.md`

### Scripts
- `scripts/tfvc-wrapper.sh`
- `scripts/bootstrap-tfvc-workspace.sh`

### Docs
- `docs/QUICKSTART.md`
- `docs/TFVC_SETUP_MAC.md`

### Workspace template
- `workspace/TFVC.code-workspace`

### AI setup skills
- `skills/tfvc-first-time-setup.agent.md`
- `skills/tfvc-workspace-from-server-url.agent.md`
- `skills/tfvc-dev-workflow.agent.md`
