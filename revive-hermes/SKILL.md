---
name: revive-hermes
description: Diagnose and restore a locally installed Hermes Agent gateway and Telegram connectivity, including broken Homebrew service launchers and missing Telegram dependencies.
---

# Revive Hermes

Restore the user's existing Hermes gateway and verify Telegram connectivity. Preserve profiles, credentials, authorization lists, model configuration, sessions, and cron jobs. Creating or updating this skill does not authorize restarting a healthy gateway.

## Diagnose from current state

Locate `hermes` and resolve its wrapper, actual Python interpreter, and package version. Determine the active Hermes home and service label rather than assuming the default profile. Inspect service definitions before running lifecycle commands: some versions rewrite them on `start` or `restart`.

For the default macOS profile, useful sources are:

- `~/Library/LaunchAgents/ai.hermes.gateway.plist`
- `launchctl print gui/$(id -u)/ai.hermes.gateway`
- `~/.hermes/gateway_state.json`
- `~/.hermes/logs/gateway.log` and `gateway.error.log`

Use raw launchd state alongside Hermes status. In the documented failure, `hermes gateway status` reported an unloaded service while launchd showed a registered crash loop. A state-file PID alone does not prove a process is alive. Read recent timestamps and distinguish old errors from new ones. Avoid printing environment files, tokens, auth files, or complete configurations containing secrets.

Identify the actual failing layer: launcher, dependency import, adapter registration, credentials, network/polling conflict, or model execution. For Homebrew launcher errors or `adapter_unavailable`, read [the local recovery reference](references/homebrew-telegram-recovery.md).

## Repair and start

Choose the smallest repair supported by evidence. Back up changed service definitions and configuration. Prefer supported repair commands when their required package files exist; inspect failures before retrying. Use the runtime's own interpreter when verifying or installing dependencies.

An authorized gateway repair includes necessary local edits and restart. Do not replace the installation or rotate credentials merely because Telegram fails to load. Do not send test messages through Telegram unless the user authorizes them; existing gateway startup notifications can provide outbound evidence.

Before unloading a running gateway, inspect active work and use its supported graceful shutdown if available. Avoid duplicate polling processes using the same bot token. After a repair, make one controlled startup attempt and inspect the result. Retry only when new evidence supports another attempt; do not leave bootstrapping or crash loops running indefinitely.

If service registration or socket creation is denied by the execution sandbox, do not route around it through another execution surface. Finish preparing and validating the repair, give the user an exact Terminal command, and resume verification after they run it. Report the service as unverified until current runtime evidence is available.

## Verify the requested outcome

Require evidence from the new process after the repair:

- The service supervisor reports running, and the loaded arguments/environment use the repaired runtime.
- The gateway state reports running and Telegram connected without an active fatal error.
- Recent logs confirm healthy Telegram `getUpdates` polling; adapter imports alone are insufficient.
- Command registration and a successful existing startup notification provide additional API/outbound evidence. If the user requested conversational replies, also verify a fresh inbound message and successful model response; connection alone does not prove model execution.

Inspect for fresh failures or repeated restarts before declaring success. Explain any remaining version-specific workaround, particularly lifecycle commands that overwrite the repaired service. Do not claim Telegram was tested end to end when only connection was verified.
