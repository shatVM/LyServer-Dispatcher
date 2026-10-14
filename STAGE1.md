# Stage 1: Status and Test Plan

## Goal

Stage 1 validates configuration and dependencies, checks repository safety, tests model availability, discovers eligible GitHub issues, and displays a preview. It does not execute issue work.

## Reported test coverage

The last user-provided report says **20/20 tests passed** after local commit `919f41f`. This report has not been independently rerun from this repository; regard it as a reported result, not an independently verified result.

| Test | Purpose |
|---|---|
| T1 | Command-line argument quoting, including spaces, quotes, backslashes and Unicode |
| T2 | Accept only the exact final non-empty response `PONG` |
| T3 | Handle a missing executable |
| T4 | Stop when the configuration file is missing |
| T5a | Reject an empty required configuration value |
| T5b | Reject a negative timeout |
| T5c | Reject a log directory inside the repository |
| T6a | Stop when the working tree is dirty |
| T6b | Do not ping the model after the dirty-tree preflight fails |
| T7 | Stop safely when model ping times out |
| T8 | Stop safely when the configured model is unavailable |
| T9 | Run the full preflight and preview path |
| T10a | Check that GitHub issue labels remain unchanged |
| T10b | Check Pull Request state (current test is too narrow; see open items) |
| T10c | Check that the repository working tree remains clean |
| T11 | Test path-containment boundary cases |
| T12a | Report a process timeout |
| T12b | Terminate a child process tree on timeout |
| T12c | Do not terminate an unrelated sentinel process |
| T13 | Check log order: Git/tree checks, ping, pong, issue discovery, preview |

## Required review fixes before acceptance

1. **Empty arguments (T1):** ensure an empty argument is passed as an explicit empty string; add a case such as `before, "", after`.
2. **Windows .cmd quoting:** Windows command-line quoting alone does not guarantee safe handling of `cmd.exe /c` metacharacters. Add a real wrapper test for spaces, quotes and shell metacharacters, or use a safer invocation strategy.
3. **Command resolution:** verify whether `.ps1` scripts are actually returned by `Get-Command -CommandType Application`. Explicitly define supported command types and test `.exe`, `.cmd` and `.ps1` where intended.
4. **Test preconditions and T10b:** require a clean working tree before the test harness begins. Improve T10b by capturing and comparing the full relevant PR state before and after the run, rather than checking only specific PR numbers.

## Acceptance sequence

1. Apply the four targeted fixes on the existing feature branch.
2. Start the test harness only when the repository is clean.
3. Run the full test suite after the fixes have been committed.
4. Save the full test output and commit IDs.
5. Review the final diff and verify the working tree is clean.
6. Only then decide whether to push the branch and open a review PR.
7. Do not start Stage 2 until Stage 1 is explicitly accepted.

## Reported local history

Known local branch: `feature/dispatcher-v02`, reportedly five commits ahead of `origin/dev @ 054ef0a`. The last reported commit is `919f41f`. These details are from the user's report and have not been independently verified here.
