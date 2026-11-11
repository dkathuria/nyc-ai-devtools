# TFVC Setup on Windows

Visual Studio is the standard Windows client for Team Foundation Version Control (TFVC). It handles Azure DevOps sign-in and provides workspace mapping, source control browsing, pending-change review, and check-in. The included PowerShell scripts and Git Bash launchers are optional helpers for users who prefer `tf.exe` commands.

## Prerequisites

- Windows 10 or 11.
- Visual Studio 2019 or 2022, or another version supported by your Azure DevOps deployment.
- Membership in the Azure DevOps project and permission to use its TFVC repository.
- The Azure DevOps organization URL and the TFVC server path, such as `$/TeamProject/Main`.

## Visual Studio setup (recommended)

1. Install and launch Visual Studio, then sign in with your work account.
2. Open **View > Team Explorer**. In Team Explorer, select **Manage Connections > Connect to a Project**.
3. Right click the Azure DevOps organization/server, select the project, and choose **Connect**.
4. For a TFVC project, select **Configure your workspace**. Choose a local working folder and the server folder to map.
5. Select **Map & Get**. Visual Studio creates or updates the workspace mapping and downloads the files.

Use an existing workspace if it already maps the required server path. Avoid creating overlapping mappings to the same local directory.

## Optional PowerShell command line

When using PowerShell, set the path to this starter pack's wrapper once. Replace the example with the location where you cloned `nyc-ai-devtools`:

```powershell
$TfvcScript = 'C:\Path\To\nyc-ai-devtools\tfvc-windows\scripts\tfvc.ps1'
```

The wrapper looks for `tf.exe` on `PATH` and in the latest Visual Studio installation via `vswhere`. Set `TFVC_TF_PATH` only if automatic discovery fails:

```powershell
$env:TFVC_TF_PATH = 'C:\Path\To\Visual Studio\Common7\IDE\CommonExtensions\Microsoft\TeamFoundation\Team Explorer\tf.exe'
```

Run a command from inside a mapped TFVC workspace:

```powershell
& $TfvcScript status . /recursive
& $TfvcScript get . /recursive
```

PowerShell may block local scripts under your execution policy. If so, use a one-time process-scoped bypass rather than changing the machine policy:

```powershell
powershell.exe -ExecutionPolicy Bypass -File $TfvcScript status . /recursive
```

### Create a new workspace from PowerShell

This is optional; the Visual Studio setup above is simpler and is preferred if you already have Visual Studio open. Run the bootstrap script with a collection URL and TFVC server path. It creates a **new local workspace**, maps the server path to a local directory, then gets the latest version. Choose a workspace name and directory that do not conflict with an existing workspace.

```powershell
$BootstrapScript = Join-Path (Split-Path -Parent $TfvcScript) 'bootstrap-tfvc-workspace.ps1'
& $BootstrapScript `
  -CollectionUrl 'https://dev.azure.com/your-organization' `
  -ServerPath '$/TeamProject/Main' `
  -WorkspaceName 'TFVC-Windows' `
  -RootDir "$HOME\source\tfvc-workspace"
```

The mapped folder is `RootDir\WorkspaceName` (in this example, `$HOME\source\tfvc-workspace\TFVC-Windows`). The script uses Visual Studio's `tf.exe`; it does not accept or save credentials. Sign in through Visual Studio if prompted.

## Git Bash

Git Bash is included with [Git for Windows](https://git-scm.com/download/win). These launchers call the PowerShell scripts and disable MSYS argument path conversion so TFVC switches such as `/recursive` reach `tf.exe` unchanged.

Set the path to this starter pack's scripts, then run the commands from inside your mapped workspace:

```bash
export TFVC_TOOLS="/c/path/to/nyc-ai-devtools/tfvc-windows/scripts"
bash "$TFVC_TOOLS/tfvc.sh" status . /recursive
bash "$TFVC_TOOLS/tfvc.sh" get . /recursive
```

To create a new workspace from Git Bash, pass PowerShell-style named parameters to the launcher:

```bash
bash "$TFVC_TOOLS/bootstrap-tfvc-workspace.sh" \
  -CollectionUrl 'https://dev.azure.com/your-organization' \
  -ServerPath '$/TeamProject/Main' \
  -WorkspaceName 'TFVC-Windows' \
  -RootDir "$(cygpath -w "$HOME/tfvc-workspace")"
```

The Git Bash launchers require Windows PowerShell (`powershell.exe`, supplied by Windows) and `cygpath` (supplied by Git for Windows), available on `PATH`. Use Visual Studio or PowerShell for authentication prompts.

## Daily workflow

Run TFVC commands from inside the mapped local folder. The `status` command shows pending changes; `get` downloads the latest server version and can require conflict resolution if local edits exist.

```powershell
& $TfvcScript status . /recursive
& $TfvcScript get . /recursive
```

To check in, use **Team Explorer > Home > Pending Changes**. Review included and excluded changes, add a meaningful comment, associate work items or check-in notes if required, and select **Check In**. For command-line check-in, inspect status first and scope the operation to the intended items:

```powershell
& $TfvcScript checkin /comment:"Describe the change" '.\path\to\file-or-folder'
```

Avoid checking in `.` recursively unless you have confirmed every pending change in the workspace belongs in the changeset.

## Optional: install WSL

WSL is useful for Linux development tools, but it isn't required for TFVC. This starter pack's scripts target Windows PowerShell and Git Bash; they aren't native WSL scripts. Keep TFVC workspace and check-in operations in Visual Studio, PowerShell, or Git Bash.

To install WSL 2, run the following from an elevated Windows PowerShell prompt, restart when prompted, then finish creating your Linux user in the launched distribution:

```powershell
wsl --install
```

See Microsoft's [install WSL guide](https://learn.microsoft.com/en-us/windows/wsl/install) for supported Windows versions, distro selection, and troubleshooting.

## Troubleshooting

- **Project isn't listed:** confirm you signed into the correct organization and have been added to the project.
- **TFVC options aren't visible:** confirm the selected project uses TFVC, not Git, and reopen Team Explorer.
- **`tf.exe` not found:** install/repair Visual Studio, or set `TFVC_TF_PATH` to its `tf.exe` location.
- **Workspace or mapping already exists:** open **Team Explorer > Workspace > Manage Workspaces** and reuse or edit the existing mapping instead of bootstrapping another one over it.
- **Get reports conflicts:** resolve them in Visual Studio before continuing; don't overwrite local work blindly.
- **Check-in is blocked:** review the policy or conflict details in Team Explorer and follow the project team's required process.

## References

- [Connect to a project from Visual Studio](https://learn.microsoft.com/en-us/azure/devops/organizations/projects/connect-to-projects?view=azure-devops)
- [Set up TFVC on your dev machine](https://learn.microsoft.com/en-us/azure/devops/repos/tfvc/set-up-team-foundation-version-control-your-dev-machine?view=azure-devops)
- [Create and work with workspaces](https://learn.microsoft.com/en-us/azure/devops/repos/tfvc/create-work-workspaces?view=azure-devops)
- [TFVC status command](https://learn.microsoft.com/en-us/azure/devops/repos/tfvc/status-command?view=azure-devops)
- [Check in your work](https://learn.microsoft.com/en-us/azure/devops/repos/tfvc/check-your-work-team-codebase?view=azure-devops)
