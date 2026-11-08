---
name: python-bump-deps
description: Use when upgrading Python, addressing Python security advisories, or resolving dependency compatibility after a Python upgrade.
---

# Python Upgrade

Upgrade the interpreter and verify the application and deployment paths, not just the version string. Prefer the project's existing tooling: use uv for uv projects and preserve pip workflows for pip projects.

## Workflow

1. **Run a security sentinel before changes.** Audit the existing dependency lock or requirements with the project's available audit tooling; check official Python security advisories. Inspect effective resolution policies, including uv `exclude-newer`, constraints and available patched versions. Record each CVE/advisory, affected package and installed version, severity, fix and blocker. A failed or unavailable audit is unverified, not clean.
   - If a fix is unavailable or blocked by policy, notify the user immediately with the risk and options. Ask whether to wait, authorize a narrowly scoped exception, or accept the remaining risk. Do not change or bypass policy without approval.
2. **Choose a version.** Check official Python releases and support status. Prefer the latest patched release on the existing supported Python line; change minor versions when required or requested.
3. **Find every pin.** Check `.python-version`, `.tool-versions`, `requires-python`, requirements and constraints, container images, CI, deployment configuration and docs. Update applicable references consistently without dropping supported versions unintentionally.
4. **Eliminate CVEs in scope.** Fix all identified, fixable interpreter and dependency vulnerabilities. Keep other dependency upgrades in a separate PR stacked on the Python upgrade branch.
   - For uv, update and verify `uv.lock` using the project's uv version.
   - For pip, preserve the existing requirements, constraints and lock-generation workflow; do not migrate the project to uv.
   - If the Python branch cannot pass without another dependency upgrade, report the blocker rather than claiming it passed.
5. **Verify each branch.** Install reproducibly under the target Python version. Re-run the security audit and check resolved versions and Python advisories. Run the complete existing test suite, typecheck and production build where configured; build and smoke-test the container when applicable.
   - Inspect destructive test setup and use isolated test databases.
   - Exclude real secrets from temporary build contexts.
   - Fix upgrade-related failures; do not weaken meaningful tests.
6. **Report.** State versions, CVEs fixed and remaining, blockers, checks passed, failures and unverified paths. Distinguish the full test suite from 100% source coverage; never claim vulnerabilities are eliminated without evidence.
7. **Deliver when requested.** Open a concise Python upgrade PR against the base branch. Open the dependency PR against the Python upgrade branch and identify it as stacked.
