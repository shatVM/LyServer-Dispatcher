# Model and Provider Compatibility

## Current design

The dispatcher calls OpenCode for the model readiness check. The selected model/provider is expected to be configured through OpenCode and the dispatcher's local configuration. Exact compatibility depends on the current script and configuration fields.

## What should work

- **Another model supported by the installed OpenCode version:** expected to work after configuration changes, provided it can respond to the readiness prompt within the configured timeout.
- **Another provider supported by OpenCode:** may work after provider credentials and model identifiers are configured locally.
- **A local Ollama model:** possible if the installed OpenCode version is configured to use that Ollama model/provider and can reach it.
- **Replacing OpenCode with a different CLI:** not guaranteed; the adapter/invocation code and tests would need to be changed.

## Ping/Pong limitations

Ping/Pong checks only that the configured command/model produces the exact expected response. It does not prove that the model can solve repository tasks correctly, follow every instruction, use tools successfully, or avoid mistakes.

Each model/provider should be tested with:
1. a readiness response;
2. timeout and unavailable-model cases;
3. a small, non-destructive representative task in a disposable test repository;
4. explicit confirmation that Stage 1 still makes no repository or GitHub changes.

## Configuration and secrets

- Keep provider API keys and tokens in local environment/configuration that is excluded from version control.
- Commit only an example configuration with placeholders.
- Never upload real tokens, API keys, passwords, or personal credentials to this repository.

## Design recommendation

Keep the dispatcher workflow independent from a specific model. Isolate the OpenCode/model call behind a small adapter and keep the provider/model identifier configurable. Do not implement that refactor as part of Stage 1 unless required to fix an existing defect; first finish and accept Stage 1.
