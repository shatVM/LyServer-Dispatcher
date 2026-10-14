# LyServer Dispatcher

Private repository for the design, documentation, review history, and eventually the versioned source of the local dispatcher for the LyServer project.

## What the dispatcher is

LyServer Dispatcher is planned as a local Windows PowerShell tool that coordinates a controlled workflow for AI-assisted development of LyServer. It is separate from the LyServer application itself: LyServer contains the application, while this repository documents and will preserve the dispatcher.

The dispatcher is intended to reduce repetitive manual checks and help identify tasks that are explicitly marked as ready. It must use clear preflight checks, logs, and safety boundaries before any future task execution is considered.

## Current scope: Stage 1

Stage 1 is **preflight and preview only**. Its intended workflow is to:

1. load and validate a local configuration;
2. check required commands and dependencies;
3. verify that the LyServer working tree meets the safety preconditions;
4. ask the configured model through OpenCode for a small readiness response (Ping/Pong);
5. discover GitHub issues marked `ready-for-opencode`, excluding issues already marked `in-progress`;
6. print a preview of eligible tasks and record the run in logs.

Stage 1 must not execute issue instructions or autonomously modify project files, labels, branches, commits, pull requests, or other GitHub state. A successful Ping/Pong means only that the configured model responded as expected; it is not a test of task-solving quality.

## Documentation

- [Design, architecture, workflow, and staged roadmap](DESIGN.md)
- [Stage 1 status, review items, and test plan](STAGE1.md)
- [Model and provider compatibility](MODEL_COMPATIBILITY.md)
- [Windows setup and launch guide](WINDOWS_SETUP.md)

## Development status

The implementation and tests are being worked on in the local LyServer checkout. The known local branch is `feature/dispatcher-v02`; do not assume that branch or its source has been pushed here. The uploaded `dispatcher-stage1-review.patch` is a review patch/diff, not a runnable program.

Do not treat the dispatcher as installed or runnable until the actual PowerShell source, example configuration, and test harness are present in the repository and have been reviewed. Do not start Stage 2 until Stage 1 has been explicitly accepted.

## Safety and repository hygiene

- Keep dispatcher source separate from the LyServer application repository.
- Commit only example configuration with placeholder values.
- Keep real tokens, API keys, passwords, local configuration, logs, and lock files out of version control.
- Keep runtime logs outside the LyServer working tree.
- Run tests from a clean working tree and retain the test output and commit IDs.
- Do not push or open a PR for the implementation until the current fixes and tests are reviewed.
- Keep an independent backup in addition to GitHub; never include secrets in backups.

