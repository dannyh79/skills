# Skills

A personal registry of reusable agent skills maintained by [dannyh79](https://github.com/dannyh79).

| Skill | Purpose | Source |
| --- | --- | --- |
| [revive-hermes](revive-hermes/SKILL.md) | Diagnose and restore a local Hermes Agent gateway and Telegram connectivity. | Created from a verified macOS recovery workflow. |
| [ponytail](ponytail/SKILL.md) | Favor simple, minimal solutions when writing and reviewing code. | Unmodified skill from [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail), npm package `@dietrichgebert/ponytail` version 4.9.0. |
| [node-bump-deps](node-bump-deps/SKILL.md) | Upgrade Node.js and its types, with other dependency upgrades in a stacked PR. | Created from a verified Node.js upgrade workflow. |
| [python-bump-deps](python-bump-deps/SKILL.md) | Upgrade Python securely with uv or pip and stack dependency upgrades separately. | Adapted from the Node.js upgrade workflow. |

## Install for Codex

Clone the registry and copy the desired skill directories into your skills directory:

```sh
gh repo clone dannyh79/skills
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R skills/revive-hermes skills/ponytail skills/node-bump-deps skills/python-bump-deps "${CODEX_HOME:-$HOME/.codex}/skills/"
```

If a destination already exists, compare or back it up before copying. Start a new Codex session to discover installed skills, then invoke `$revive-hermes`, `$ponytail`, `$node-bump-deps`, or `$python-bump-deps`.

Each skill lives in a directory with a `SKILL.md` entry point. Referenced resources travel with that directory. Ponytail here contains the standalone skill, not the upstream plugin's hooks, extensions, or companion skills.

## Maintenance

For Hermes repairs, inspect the installed version and current service state before applying a historical workaround. The recorded Homebrew 0.22.4 recovery is evidence, not a universal fix.

When updating Ponytail, preserve upstream attribution and its license, and record the imported package version here.

## Licensing

Ponytail is by DietrichGebert and distributed under the [MIT license](ponytail/LICENSE). No repository-wide license is assigned to original material in this registry.
