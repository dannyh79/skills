---
name: node-bump-deps
description: Use when upgrading Node.js, addressing Node.js security advisories, or resolving dependency compatibility after a Node.js upgrade.
---

# Node.js Upgrade

Upgrade the runtime and its Node.js types together. Verify the application and deployment paths, not just the version string.

## Workflow

1. **Run a security sentinel before changes.** Audit the existing lockfile with the project's package manager and check official Node.js advisories. Inspect effective dependency-resolution policies, including pnpm `minimum-release-age`, and available patched versions. Record each CVE/advisory, affected package and installed version, severity, fix, and any policy or compatibility blocker. An unavailable or failed audit is unverified, not clean.
   - If a fix is unavailable or blocked by policy, notify the user immediately with the risk, available options and release-age wait time when known. Ask the user to choose whether to wait, authorize a narrowly scoped exception, or accept the remaining risk. Do not change or bypass the policy without approval.
2. **Choose a version.** Check official Node.js releases and security advisories. Prefer the latest patched release on the existing supported LTS line; change major versions when required or requested.
3. **Find every pin.** Check version-manager files, package engines, container images, CI, deployment configuration and docs. Update all applicable references consistently.
4. **Update Node.js types.** Bump `@types/node` to the target Node major and a suitable current version; update the lockfile.
5. **Eliminate CVEs in scope.** Fix all identified, fixable Node.js and dependency vulnerabilities. Put other dependency upgrades in a separate PR stacked on the Node upgrade branch, including their lockfile changes. Pin the existing package-manager version when necessary to make the Node upgrade build reproducibly. If the Node branch cannot pass independently without another dependency upgrade, report the blocker rather than claiming it passed.
6. **Verify each branch.** Install locked dependencies under the target Node version. Re-run the security audit and check the resolved versions and Node.js advisories; report every remaining CVE, blocked fix and unverified audit. Run the complete existing test suite, typecheck and production build. Build and smoke-test the container when applicable.
   - Use an isolated test database; inspect destructive test setup first.
   - Exclude real secrets from temporary container build contexts.
   - Fix upgrade-related failures; do not weaken meaningful tests.
7. **Report.** State versions, CVEs fixed and remaining, blockers, checks passed, failures and unverified paths. Distinguish the full test suite from 100% source coverage; never claim vulnerabilities are eliminated without evidence.
8. **Deliver when requested.** Open a concise Node upgrade PR against the base branch. Open the dependency PR against the Node upgrade branch, and identify it as stacked.
