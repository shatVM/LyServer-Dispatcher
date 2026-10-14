# Stage 1: Status, review, and test plan

## Goal

Stage 1 is a safe preflight and preview run. It validates local configuration and dependencies, checks repository safety, verifies model readiness, discovers eligible GitHub issues, and displays a preview. It does not execute issue work.

## Current status

The dispatcher implementation is being developed and tested in the local LyServer checkout on the known branch `feature/dispatcher-v02`. The implementation has not been independently verified from this documentation repository, and the source should not be assumed to exist on this repository's `main` branch.

A review patch named `dispatcher-stage1-review.patch` was uploaded to this repository. It is a text diff, not a runnable program. Static review of that patch showed changes addressing several prior concerns, including empty arguments, real `.cmd` wrapper coverage, command resolution for supported script types, clean-tree test preconditions, and broader before/after GitHub-state checks. These observations do not replace running the final committed test suite.

A prior local report stated **20/20 tests passed** after commit `919f41f`. That result was reported by the user and has not been independently rerun here. Treat it as a reported result only until the final output and commit are checked.

## Intended Stage 1 behavior

1. Load local configuration and reject missing or invalid required values.
2. Verify required commands and dependencies.
3. Validate the target LyServer path and require a safe working-tree state.
4. Run a bounded model readiness request through OpenCode and require the exact final non-empty response `PONG`.
5. Discover issues labelled `ready-for-opencode`, excluding issues labelled `in-progress`.
6. Display a preview and write logs outside the LyServer working tree.
7. Exit without running issue instructions or making repository/GitHub changes.

## Test coverage to verify

| Area | Required check |
|---|---|
| Argument handling | Spaces, quotes, backslashes, Unicode, and explicit empty arguments |
| Command wrappers | Real `.cmd` wrapper invocation with representative Windows metacharacters; documented unsupported cases must fail safely |
| Command resolution | Explicit behavior for supported executable and script types, including `.exe`, `.cmd`, and `.ps1` where intended |
| Readiness response | Accept only exact final non-empty `PONG`; reject unexpected responses |
| Configuration | Missing file, empty required values, invalid timeout, and unsafe log path |
| Repository preflight | Reject a dirty tree and do not continue to the model call after preflight failure |
| Timeouts | Report timeout, terminate the relevant child process tree, and avoid terminating an unrelated process |
| Model failures | Handle missing command, unavailable model, non-zero exit, and timeout |
| Issue discovery | Include eligible issues and exclude issues marked `in-progress` |
| No side effects | Compare GitHub issue labels and relevant PR state before/after; confirm the working tree remains clean |
| Logging | Verify useful event order and diagnostic errors without leaking secrets |

The actual test names and count may differ in the final implementation. Use the committed test harness as the source of truth and record the exact command, output, and commit ID.

## Acceptance checklist

- [ ] Developer has finished the current fixes.
- [ ] Tests start with the required clean working-tree precondition.
- [ ] Full test suite passes after the final code changes are committed.
- [ ] Full test output and exact commit ID are saved.
- [ ] Final diff and working-tree status are inspected.
- [ ] The test harness demonstrates no unintended repository or GitHub changes.
- [ ] Configuration example contains placeholders only; real config and logs are ignored.
- [ ] Source, tests, and documentation agree.
- [ ] Stage 1 is explicitly accepted before any push/PR decision or Stage 2 work.

## Safety boundary

Stage 1 must not modify application files, issue labels, branches, commits, pull requests, or other GitHub state. It must not execute task instructions. If any of these changes occur unexpectedly, stop and investigate before using the dispatcher again.

## Known development references

- Local implementation branch reported: `feature/dispatcher-v02`
- Last reported local commit: `919f41f`
- Review patch in this repository: [`dispatcher-stage1-review.patch`](https://github.com/shatVM/LyServer-Dispatcher/blob/main/dispatcher-stage1-review.patch)

The branch/commit details are historical user-reported context and are not independently verified by this document.
