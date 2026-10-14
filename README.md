# LyServer Dispatcher

Private repository for the dispatcher that coordinates the LyServer task workflow.

## Purpose

The dispatcher is a local PowerShell tool intended to:
1. validate its configuration and required tools;
2. check that the LyServer working tree is safe to use;
3. verify that the configured AI model responds to a small readiness prompt (Ping/Pong);
4. discover GitHub issues marked `ready-for-opencode`, excluding issues already marked `in-progress`;
5. show a preview of candidate tasks.

**Current scope: Stage 1 only.** Stage 1 is a read-only/preflight and preview stage. It must not autonomously edit project files, change GitHub issue labels, create branches, push commits, or create pull requests.

## Safety rules

- Keep dispatcher code separate from the LyServer application repository.
- Keep credentials, tokens, local configuration, logs, and lock files out of version control.
- Do not commit real secrets. Keep local configuration based on an example file.
- Do not push or create a PR for the Stage 1 implementation until the review findings are fixed and the tests pass on a clean working tree.
- Do not begin Stage 2 until Stage 1 is reviewed and explicitly accepted.
- Treat a successful test run as evidence for the scenarios tested, not proof that every possible case is safe.

## Documentation

- [Stage 1 status and test plan](STAGE1.md)
- [Model and provider compatibility](MODEL_COMPATIBILITY.md)

## Current implementation location

The Stage 1 implementation is being developed locally in the LyServer checkout under `tools/dispatcher/`. The known local branch is `feature/dispatcher-v02`; do not assume it has been pushed to GitHub.

## Backups

Use this private repository for durable documentation and, after review, versioned dispatcher source. Also keep an independent ZIP backup on a different drive or trusted storage. Never put credentials or real local config into either backup.
