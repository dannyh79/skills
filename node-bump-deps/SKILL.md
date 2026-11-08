---
name: node-bump-deps
description: Use when upgrading Node.js, addressing Node.js security advisories, or resolving dependency compatibility after a Node.js upgrade.
---

# Node.js Upgrade

Upgrade the runtime and its Node.js types together. Verify the application and deployment paths, not just the version string.

## Workflow

1. **Choose a version.** Check official Node.js releases and security advisories. Prefer the latest patched release on the existing supported LTS line; change major versions when required or requested.
2. **Find every pin.** Check version-manager files, package engines, container images, CI, deployment configuration and docs. Update all applicable references consistently.
3. **Update Node.js types.** Bump `@types/node` to the target Node major and a suitable current version; update the lockfile.
4. **Handle other dependencies separately.** Inspect compatibility with the target Node version. Put other dependency upgrades in a separate PR stacked on the Node upgrade branch. Update their lockfile there. Pin the existing package-manager version when necessary to make the Node upgrade build reproducibly. If the Node branch cannot pass independently without another dependency upgrade, report the blocker rather than claiming it passed.
5. **Verify each branch.** Install locked dependencies under the target Node version. Run the complete existing test suite, typecheck and production build. Build and smoke-test the container when applicable.
   - Use an isolated test database; inspect destructive test setup first.
   - Exclude real secrets from temporary container build contexts.
   - Fix upgrade-related failures; do not weaken meaningful tests.
6. **Report.** State versions, checks passed, failures and unverified paths. Distinguish the full test suite from 100% source coverage.
7. **Deliver when requested.** Open a concise Node upgrade PR against the base branch. Open the dependency PR against the Node upgrade branch, and identify it as stacked.
