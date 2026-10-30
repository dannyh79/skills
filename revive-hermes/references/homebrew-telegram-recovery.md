# Homebrew Telegram recovery observed on macOS

This is a recorded Hermes Agent 0.22.4 failure, repaired on 2026-10-31. Revalidate paths and behavior on future versions; do not apply this workaround indiscriminately.

## Failure signatures

1. The gateway logged `adapter_unavailable` and `Platform 'Telegram' requirements not met`. Its actual Python interpreter raised `ModuleNotFoundError: No module named 'telegram'`.
2. The launchd definition invoked a generated launcher under `libexec/lib/python3.14/site-packages/.hermes/bin/hermes`. It repeatedly exited with `no dependency environment is committed for this install; run hermes pm repair`.
3. The dependency sync receipt showed that `uv.lock` was missing under site-packages, so the packaged repair mechanism could not rebuild that environment.
4. `hermes gateway start` regenerated the broken plist, overwriting a manual fix. Use direct launchctl loading of the validated repaired plist for this affected version.

The public wrapper `/opt/homebrew/bin/hermes` correctly supplies Homebrew's bundled-plugin and runtime variables. Calling a generated store launcher bypassed the working packaged entry point.

## Telegram dependency recovery

The package's Telegram adapter was at:

`/opt/homebrew/Cellar/hermes-agent/0.22.4/share/hermes-agent/plugins/platforms/telegram/adapter.py`

It imports `telegram`, `telegram.ext`, `telegram.constants`, and `telegram.request`. Inspect the installed adapter and dependency metadata to determine current requirements.

A complete cached `python-telegram-bot` 22.8 distribution was available in `~/.cache/uv/archive-v0/`. Its `telegram/` package and matching `.dist-info` directory were copied to `~/.hermes/lazy-packages`. The runtime already satisfied the required `httpx`, `httpcore`, and SOCKS dependencies. Cache directory identifiers are not stable: rediscover and validate distribution metadata, interpreter compatibility, and imports before reusing cached files. Do not blindly copy arbitrary packages or overwrite unrelated dependencies.

Homebrew's older `uv` crashed in this sandbox. A newer Hermes-managed `uv` existed under `~/.hermes/tools/`, but offline resolution did not find the cached distribution through its index. These are historical observations, not reasons to skip a working supported installer.

## Working launchd configuration

The existing osascript supervisor shape and service settings were preserved. Its inner command became:

```sh
exec /opt/homebrew/Cellar/hermes-agent/0.22.4/libexec/bin/python \
  -m hermes_cli.stderr_timestamp \
  --error-log $HOME/.hermes/logs/gateway.error.log -- \
  /opt/homebrew/bin/hermes gateway run --external-supervisor \
  >> $HOME/.hermes/logs/gateway.log \
  2>> $HOME/.hermes/logs/gateway.error.log
```

The plist's `EnvironmentVariables.PYTHONPATH` included `$HOME/.hermes/lazy-packages` so the restored dependency was importable. Preserve any existing required Python paths when adapting this repair. Resolve the current Homebrew interpreter instead of assuming version 0.22.4 remains installed.

The local recovery helpers created during the incident are:

- `~/.hermes/scripts/repair-gateway-launcher.py`
- `~/.hermes/scripts/start-repaired-gateway.command`

Read them before reuse. They contain fixed 0.22.4 paths, and the start helper unloads the service. They are historical local artifacts, not prerequisites of this skill. A later upgrade may make them inappropriate.

For this affected installation, the user ran:

```sh
~/.hermes/scripts/start-repaired-gateway.command
```

That helper patched the plist, used `launchctl bootout` followed by `launchctl bootstrap gui/$(id -u)` with the plist path, and printed startup logs. Validate a changed plist with `plutil -lint` and a shell helper with `zsh -n` before use.

## Successful recovery evidence

The loaded launchd service reported running with the corrected Python/module command and dependency path. Fresh gateway logs showed `Telegram polling confirmed healthy: getUpdates progressing`, `telegram connected`, successful bot-command registration, and a sent home-channel startup notification. Gateway state showed `gateway_state=running`, Telegram `state=connected`, and no attention flag. This proved connection and API activity; a fresh model conversation was not tested in that repair.
