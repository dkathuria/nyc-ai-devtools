# TFVC Quickstart (Windows)

Visual Studio is the recommended Windows client. You do not need the PowerShell scripts to use TFVC.

## Connect and get the code

1. Install Visual Studio and sign in with the account that has access to the Azure DevOps organization.
2. In Visual Studio, open **View > Team Explorer**. In Team Explorer, choose **Manage Connections > Connect to a Project**.
3. Select the Azure DevOps organization/server and project, then choose **Connect**.
4. On the TFVC project page, choose **Configure your workspace**. Select a local folder and map the server path you need.
5. Choose **Map & Get** to download the code.

## Work and check in

Open **Team Explorer > Home > Pending Changes** (Ctrl+0, P). Review **Included Changes** and **Excluded Changes**, enter a useful comment, associate a work item if required by your team, then choose **Check In**. The successful check-in creates a changeset.

## Publish from pitcrew-dcwp

This section is specific to the separate `pitcrew-dcwp` Git mirror; its `publish:*` npm scripts are not included in this starter pack. Confirm that checkout provides these scripts and follow its setup instructions before continuing. Use this workflow to copy merged changes from the Git mirror into the mapped TFVC workspace. Run these commands from **Git Bash**. Stop the local stack first so temporary overlays are reverted.

Replace the example paths with your local paths. Before step 1, set the wrapper location and enter the mapped TFVC workspace:

```bash
export TFVC_TOOLS="/c/path/to/nyc-ai-devtools/tfvc-windows/scripts"
cd "/c/path/to/tfvc-workspace/DEV-OTI"
```

Publishing sends only the files changed in git between the `synced-to-tfvc` tag (the last commit checked in to TFVC) and `origin/main`, using their committed content. Build output, caches, and uncommitted edits are never copied, and you don't need to have `main` checked out.

1. Get latest in TFVC (Team Explorer, or `bash "$TFVC_TOOLS/tfvc.sh" get . /recursive`) and confirm there are no pending changes.

2. From the mirror repository, preview the publish:

   ```bash
   cd "/c/path/to/pitcrew-dcwp"
   npm run publish:dry-run
   ```

   The preview lists the merged PRs and each file as `add`, `edit`, `delete`, `already` (TFVC already matches), or `drift`. Drift means the TFVC file changed after the last sync, so overwriting it would lose that change. Compare it with the git versions, then rerun with `-- --skip-drift` and merge it by hand. Use `-- --force` only if you're sure the TFVC change should be replaced.

   To publish a different range, pass `-- --base <commit> --head <commit>`.

3. Apply and print the comment template:

   ```bash
   npm run publish:apply
   npm run publish:checkin-note
   ```

   `publish:apply` copies files into the TFVC workspace and prints `tf add` / `tf delete` commands for new and removed files. It doesn't create a changeset. To undo it, run `npm run publish:rollback`.

4. Pend new and deleted files, then review:

   ```bash
   cd "/c/path/to/tfvc-workspace/DEV-OTI"
   bash "$TFVC_TOOLS/tfvc.sh" add "<path from publish:apply>"
   bash "$TFVC_TOOLS/tfvc.sh" delete "<deleted path from publish:apply>"
   bash "$TFVC_TOOLS/tfvc.sh" status . /recursive
   ```

   Run `add` and `delete` only for the corresponding paths reported by `publish:apply`; skip either command when there are no such files. The pending changes should match the files from step 2 that weren't `already`. In **Team Explorer > Pending Changes**, don't promote other detected files.

5. Enter the comment (one sentence per change, PR numbers in parentheses, for example `Add Click-to-Cancel submit timeout handling and timeout error page (PR #132).`) and select **Check In**.

6. Record the check-in so the next publish starts from it. Replace `<changeset-number>` with the actual changeset number:

   ```bash
   cd "/c/path/to/pitcrew-dcwp"
   npm run publish:mark-synced -- "<changeset-number>"
   ```

   This moves the `synced-to-tfvc` tag to the commit you published and pushes it, so everyone's next publish uses the same starting point.

For command-line use, see [TFVC_SETUP_WINDOWS.md](TFVC_SETUP_WINDOWS.md). For more detail, see Microsoft's [Visual Studio TFVC setup guide](https://learn.microsoft.com/en-us/azure/devops/repos/tfvc/set-up-team-foundation-version-control-your-dev-machine?view=azure-devops).
