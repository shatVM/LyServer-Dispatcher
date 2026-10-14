# LyServer Dispatcher

Repository for the design, documentation, review history, and versioned source of the local dispatcher for the LyServer project.

## What the dispatcher is

LyServer Dispatcher is planned as a local Windows PowerShell tool that coordinates a controlled workflow for AI-assisted development of LyServer. It is separate from the LyServer application itself: LyServer contains the application, while this repository documents and will preserve the dispatcher.

The dispatcher is intended to reduce repetitive manual checks and help identify tasks that are explicitly marked as ready. It must use clear preflight checks, logs, and safety boundaries before any future task execution is considered.

## Current scope: Stage 2

Stage 1 is **preflight and preview only**. Its workflow is to:

1. load and validate a local configuration;
2. check required commands and dependencies;
3. verify that the LyServer working tree meets the safety preconditions;
4. ask the configured model through OpenCode for a small readiness response (Ping/Pong);
5. discover GitHub issues marked `ready-for-opencode`, excluding issues already marked `in-progress`;
6. print a preview of eligible tasks and record the run in logs.

Stage 1 must not execute issue instructions or autonomously modify project files, labels, branches, commits, pull requests, or other GitHub state. A successful Ping/Pong means only that the configured model responded as expected; it is not a test of task-solving quality.

Stage 2 (`-Stage2`, opt-in) executes at most one task in an isolated git worktree under a dispatcher lock, verifies the git diff afterwards, and reports one outcome (`SUCCESS` / `NO_CHANGES` / `ERROR` / `TIMEOUT` / `REFUSED`). The agent's stdout/stderr are streamed live, line by line, into the console and the journal while the agent is still running. Stage 2 performs **no** git push, **no** PR creation, and **no** GitHub mutations of any kind; publishing is intentionally not implemented, and no config key can enable it.

## Source

| File | Purpose |
| --- | --- |
| `dispatcher.ps1` | Dispatcher (Stage 1 preflight/preview + Stage 2 controlled execution) |
| `dispatcher.tests.ps1` | Automated tests using a fake agent; no real OpenCode calls |
| `config.example.psd1` | Example configuration with placeholder paths — copy to `config.psd1` |
| `Start-Dispatcher.bat` | Launch wrapper |
| `Test-Dispatcher.bat` | Test wrapper |
| `USAGE.md` | Full usage guide (Ukrainian) |

Local configuration (`config.psd1`), runtime logs, and lock files are excluded via `.gitignore`.

## Documentation

- [Design, architecture, workflow, and staged roadmap](DESIGN.md)
- [Stage 1 status, review items, and test plan](STAGE1.md)
- [Model and provider compatibility](MODEL_COMPATIBILITY.md)
- [Windows setup and launch guide](WINDOWS_SETUP.md)
- [Usage guide (Ukrainian)](USAGE.md)

## Development status

The PowerShell source, example configuration, and test harness are now present in this repository. Stage 2 is implemented and includes live streaming of agent output; the implementation originated in the local LyServer checkout (`feature/dispatcher-v02` / dispatcher worktrees) and is preserved here.

Stage 2 has **not** been accepted: live output, UTF-8/Cyrillic handling, and the task timeout still have open issues, and no independent review of this source has been recorded here. The `dispatcher-stage1-review.patch` file is a Stage 1 review patch/diff, not a runnable program.

Known gaps:
- Publishing (commit → push → PR → issue comment) is not implemented; results currently stay in the local worktree.
- The default task timeout is 1800 s, which proved too short for real tasks.
- Without live output, a running agent is indistinguishable from a hung one.

## Safety and repository hygiene

- Keep dispatcher source separate from the LyServer application repository.
- Commit only example configuration with placeholder values.
- Keep real tokens, API keys, passwords, local configuration, logs, and lock files out of version control.
- Keep runtime logs outside the LyServer working tree.
- Run tests from a clean working tree and retain the test output and commit IDs.
- The source was published to this repository via a pull request for review; merge only after review.
- Do not enable or implement automatic merging, pushing, or GitHub mutations from the dispatcher.
- Keep an independent backup in addition to GitHub; never include secrets in backups.

