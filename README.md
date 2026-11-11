# nyc-ai-devtools

This repository contains development-tool instructions, custom agent files, and reference checklists. Choose the bundle for your task; opening the repository alone does not register every `.agent.md` file with your assistant.

## Choose a bundle

| Bundle | Instructions | Setup and reference docs |
| --- | --- | --- |
| Accessibility audits | [accessibility-skill/AGENTS.md](accessibility-skill/AGENTS.md) | [Accessibility auditor](accessibility-skill/accessibility-auditor.agent.md) |
| NYC public-facing application launch review | [launch-checklist/AGENTS.md](launch-checklist/AGENTS.md) | [Launch checklist README](launch-checklist/README.md) |
| TFVC on macOS | [tfvc-macos/AGENTS.md](tfvc-macos/AGENTS.md) | [macOS README](tfvc-macos/README.md) |
| TFVC on Windows | [tfvc-windows/AGENTS.md](tfvc-windows/AGENTS.md) | [Windows README](tfvc-windows/README.md) |

## Make the instructions available

Open this repository in VS Code, or add the complete bundle folder to a multi-root workspace alongside your application. Keep each bundle's relative directory structure intact: its instructions refer to sibling docs and scripts. Adding a reference folder does not apply its `AGENTS.md` to an unrelated application automatically; explicitly ask the assistant to read the selected instructions for your task.

For direct use, attach or reference the bundle's `AGENTS.md` and the specific agent file in chat. Ask the assistant to read both before proceeding. Paths below are relative to this repository root; if you open a bundle alone, omit its leading directory. If this repository is alongside your application, identify both folders and the application to act on.

For a selectable VS Code custom agent, configure the desired agent file location using the [VS Code custom agent documentation](https://code.visualstudio.com/docs/agents/reference/custom-agent-file-reference), then verify that the agent appears in the agent picker. These files are stored in bundle-specific locations, and the macOS and Windows bundles reuse filenames; select only the platform you need. Tool availability depends on your assistant and environment.

## Agent inventory

- [Accessibility auditor](accessibility-skill/accessibility-auditor.agent.md)
- [macOS TFVC first-time setup](tfvc-macos/skills/tfvc-first-time-setup.agent.md)
- [macOS TFVC workspace from server URL](tfvc-macos/skills/tfvc-workspace-from-server-url.agent.md)
- [macOS TFVC development workflow](tfvc-macos/skills/tfvc-dev-workflow.agent.md)
- [Windows TFVC first-time setup](tfvc-windows/skills/tfvc-first-time-setup.agent.md)
- [Windows TFVC development workflow](tfvc-windows/skills/tfvc-dev-workflow.agent.md)

The launch-checklist bundle uses `AGENTS.md`, domain reference docs, and a checklist rather than a separate `.agent.md` file.

## Invocation examples

- “Read `accessibility-skill/AGENTS.md` and `accessibility-skill/accessibility-auditor.agent.md`, then audit http://localhost:5173. Report evidence and any checks you cannot run.”
- “Read `launch-checklist/AGENTS.md` and `launch-checklist/README.md`, then review this application's launch readiness. Identify relevant domains and missing evidence.”
- “Read `tfvc-macos/AGENTS.md` and `tfvc-macos/skills/tfvc-first-time-setup.agent.md`, then guide first-time setup for my Mac using collection `https://dev.azure.com/<org>` and server path `$/<project>`.”
- “Read `tfvc-windows/AGENTS.md` and `tfvc-windows/skills/tfvc-first-time-setup.agent.md`, then guide Visual Studio sign-in and workspace setup on Windows.”
- “Read the TFVC development workflow agent for my platform, inspect pending changes in my mapped workspace, and help prepare the intended changeset.”

Replace example URLs and paths with your actual targets. Follow the selected bundle's prerequisites and guardrails; invoking an agent does not itself install its tools or authorize destructive actions. Folder-level docs and `AGENTS.md` files remain the source of truth for each toolset.
