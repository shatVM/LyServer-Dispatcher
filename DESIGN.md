# Dispatcher design and roadmap

## 1. Purpose

The dispatcher is a local coordinator for a controlled AI-assisted development workflow around the LyServer repository. It is not the LyServer web application, a GitHub bot, or an autonomous agent that should immediately start changing code.

Its first responsibility is to establish that the local environment is in a known state, verify that the configured model can answer a readiness prompt, and show a human which GitHub issues appear eligible for consideration.

## 2. High-level workflow

```text
Start
  |
  v
Load local configuration
  |
  v
Validate configuration and dependencies
  |
  v
Check repository path and working-tree safety
  |
  v
Call configured model through OpenCode (Ping/Pong)
  |
  v
Read eligible GitHub issues
  |
  v
Show a preview and write run logs
  |
  v
Exit without executing tasks or changing GitHub state
```

If a required precondition fails, the dispatcher should stop safely and explain the failure. It should not continue to model calls or task discovery after a failed safety preflight when those later steps depend on the failed check.

## 3. Main concepts

### Configuration

Local settings should define the locations and tools needed for a run, including the LyServer checkout, the required command/model settings, timeouts, and log location. Exact field names must be taken from the reviewed example configuration that ships with the implementation.

The actual local configuration belongs on the user's computer. The repository should contain only a template such as `config.example.psd1`, with placeholders and no secrets.

### Repository preflight

Before doing anything beyond harmless checks, the dispatcher should validate the target checkout and confirm that its working tree meets the configured safety rule. The Stage 1 test harness should start from a clean tree so its own results are not confused with unrelated edits.

Logs should be stored outside the LyServer working tree so the dispatcher does not make the application repository appear dirty simply by running.

### Model readiness check

Stage 1 uses OpenCode to send a small readiness prompt and expects the exact final non-empty response `PONG`. The check must have a timeout and must handle missing commands, unavailable models, unexpected responses, and process failures.

A successful readiness check proves only that the command/model responded in the expected format. It does not prove that the model can implement a feature correctly or safely.

### GitHub issue discovery

The initial task-selection convention is:

- include issues labelled `ready-for-opencode`;
- exclude issues labelled `in-progress`;
- show candidate issues in a preview for human review.

Stage 1 should read GitHub state only. It must not add or remove labels, create branches, push commits, open pull requests, or execute issue bodies as instructions.

### Logging

Logs should make the sequence of checks observable: preflight, model readiness request/response, issue discovery, preview, and any errors/timeouts. Logs may contain repository paths or task metadata, so keep them local and do not commit them automatically.

## 4. Safety boundaries

Stage 1 is deliberately non-executing.

| Area | Stage 1 rule |
|---|---|
| Application files | Do not edit |
| Git working tree | Do not alter |
| Branches and commits | Do not create or move |
| GitHub issue labels | Read only |
| Pull requests | Do not create or change |
| Model call | Readiness check only |
| Task instructions | Preview, never execute |
| Secrets and local config | Never commit |

The test harness should compare relevant GitHub state before and after the run and confirm the repository remains clean. Test reports must say which scenarios were actually executed; passing tests are not a universal guarantee.

## 5. Staged development plan

### Stage 1 — Preflight and preview

- validate configuration and required tools;
- verify repository safety conditions;
- test model availability with Ping/Pong and a timeout;
- read candidate issues using the agreed labels;
- display a preview and write logs;
- demonstrate no unintended repository or GitHub changes.

**Exit condition:** implementation and tests reviewed, test output and commit IDs saved, final diff checked, and Stage 1 explicitly accepted.

### Stage 2 — Controlled task execution (not authorized yet)

This stage is only a future planning placeholder. It must not begin until Stage 1 has been reviewed and explicitly accepted. Before implementation, define the permitted actions, worktree/branch isolation, limits on model tool access, failure recovery, review gates, and rules for GitHub writes. No Stage 2 behavior should be inferred from the current Stage 1 code or documentation.

### Later stages

Any automation beyond controlled task execution should be separately designed and approved. Each stage should have explicit capabilities, prohibitions, tests, and acceptance criteria before implementation.

## 6. Repository layout (intended)

The dispatcher repository is for durable project documentation and reviewed source. The expected source layout under the implementation project is currently described as:

```text
tools/dispatcher/
  dispatcher.ps1          # Main entry point
  config.example.psd1     # Safe template; no real credentials
  dispatcher.tests.ps1    # Test harness
  config.psd1              # Local-only configuration; must be ignored by Git
```

This is the intended layout based on the current design, not a claim that every file is already present on the default branch of this repository. Confirm the actual files and paths before running commands.

## 7. Model/provider independence

The workflow should depend on a narrow model-readiness adapter rather than on assumptions about one particular model. The initial implementation calls OpenCode; another model or provider is expected to work only if supported by the installed OpenCode setup and validated with tests. See [Model and Provider Compatibility](MODEL_COMPATIBILITY.md).

## 8. Current status and known limitations

- The private repository is the durable home for dispatcher documentation.
- The source is being developed in the local LyServer checkout; do not assume it has been mirrored here.
- `dispatcher-stage1-review.patch` is a diff for review, not an executable application.
- Do not report test results as independently verified unless the test harness was actually run and its output inspected.
- Do not push/merge the implementation or proceed to Stage 2 before the agreed review and acceptance.

## 9. Change discipline

For each meaningful change:

1. describe the behavior change and safety implications;
2. update tests for both expected behavior and failure paths;
3. run the complete test suite from the documented preconditions;
4. retain the test output and exact commit ID;
5. inspect the final diff and working-tree status;
6. update this documentation when behavior, configuration, or safety boundaries change.
