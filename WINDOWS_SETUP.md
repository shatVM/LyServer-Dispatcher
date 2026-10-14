# Windows setup and launch guide

## Important: a patch is not a program

The file `dispatcher-stage1-review.patch` is a text diff that describes proposed code changes. Uploading it to GitHub stores the patch file; it does not apply the changes, install the dispatcher, or make it runnable.

The actual entry point is intended to be `dispatcher.ps1`. Do not run the dispatcher until the implementation, test harness, and example configuration have been reviewed and are available at the expected paths.

## Prerequisites

The current design targets Windows PowerShell and uses OpenCode for the model readiness check. Before the first run, confirm that:

- Windows PowerShell is available;
- Git is installed and can access the LyServer checkout;
- the configured OpenCode command is installed and works from a terminal;
- the required GitHub access is configured without placing tokens in tracked files;
- the actual dispatcher source and example configuration exist;
- the LyServer working tree meets the Stage 1 clean-tree requirement.

Exact command names and configuration fields must follow the final reviewed implementation, not this general guide.

## Configuration

1. Copy the example configuration to a local file named `config.psd1` in the implementation's configuration directory.
2. Set the paths and model/tool settings to match the local computer.
3. Set logs to a directory outside the LyServer repository.
4. Keep real credentials in an appropriate local secret store or environment variable where supported.
5. Verify that `config.psd1`, logs, tokens, and other local runtime files are excluded from version control.

Never paste real tokens, API keys, passwords, or private credentials into a tracked configuration file or issue description.

## Running from PowerShell

After the implementation has been reviewed and the paths are confirmed, the expected form is:

```powershell
Set-Location "C:\path\to\LyServer"
powershell.exe -NoProfile -ExecutionPolicy Bypass -File ".\tools\dispatcher\dispatcher.ps1"
```

Replace the path with the real checkout location. This is a template command, not confirmation that the current repository already contains a runnable copy. Follow the script's documented parameters if the reviewed version defines any.

## First-run checklist

- [ ] The working tree is clean.
- [ ] The local configuration exists and contains valid paths.
- [ ] The model readiness check finishes within its timeout.
- [ ] The preview lists only eligible issues.
- [ ] Logs are written outside the LyServer working tree.
- [ ] The run does not change files, labels, branches, commits, or pull requests.
- [ ] The full test harness passes and its output is saved before Stage 1 is accepted.

## Troubleshooting principles

- If configuration validation fails, correct the local configuration rather than bypassing the safety check.
- If the working tree is dirty, inspect or commit/stash the unrelated work before running tests; do not let the dispatcher overwrite it.
- If the model check times out or the model is unavailable, verify the OpenCode setup and model identifier.
- If GitHub issue discovery fails, check authentication and repository permissions without exposing credentials in logs.
- If the dispatcher creates an unexpected change, stop using it and preserve the logs and diff for review.

## Launching by double-click

A `.bat` launcher can be added after the final script path, arguments, and exit-code behavior are confirmed. Do not create a launcher that hides errors or automatically bypasses safety checks.
