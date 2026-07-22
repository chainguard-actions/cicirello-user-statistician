<!-- markdownlint-disable -->

# Hardening Report: cicirello--user-statistician/v1.26.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cicirello--user-statistician/v1.26.3** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: A `${{ steps.integration.outputs.exit-code }}` expression is directly interpolated inside a `run:` shell command string. Even though `steps.*` outputs may appear "internal", any `${{ ... }}` expression inside a `run:` block is subject to YAML template substitution before the shell sees it, enabling script injection if the output value contains shell metacharacters. The offending line is: `echo "exit-code = ${{ steps.integration.outputs.exit-code }}"`

Locations:

- `.github/workflows/build.yml:40`

### unpinned-uses (severity: high)

All `uses:` references in workflow files use mutable version tags instead of full 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if the referenced tag is moved or compromised. Failing references include: actions/checkout@v7, actions/setup-python@v7, actions/upload-artifact@v7 (build.yml); actions/checkout@v7, github/codeql-action/init@v4, github/codeql-action/autobuild@v4, github/codeql-action/analyze@v4 (codeql-analysis.yml); actions/checkout@v7, cicirello/user-statistician@v1 (generate-international-samples.yml); actions/checkout@v7, cicirello/user-statistician@v1 (generate-samples.yml); actions/checkout@v7 (major-release-num.yml); actions/checkout@v7, cicirello/user-statistician@v1 (manual-theme-sample.yml); actions/stale@v10 (stale.yml).

Locations:

- `.github/workflows/build.yml:19`
- `.github/workflows/codeql-analysis.yml:34`
- `.github/workflows/generate-international-samples.yml:11`
- `.github/workflows/generate-samples.yml:13`
- `.github/workflows/major-release-num.yml:11`
- `.github/workflows/manual-theme-sample.yml:16`
- `.github/workflows/stale.yml:11`

### missing-permissions (severity: medium)

These workflow files have no top-level `permissions:` block and no job-level `permissions:` block on any job, meaning they run with the default (broad) repository permissions granted by GitHub Actions. Each should declare minimal required permissions explicitly.

Locations:

- `.github/workflows/generate-international-samples.yml:1`
- `.github/workflows/generate-samples.yml:1`
- `.github/workflows/major-release-num.yml:1`
- `.github/workflows/manual-theme-sample.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings: (1) script-injection in build.yml - moved ${{ steps.integration.outputs.exit-code }} to env block as EXIT_CODE; (2) unpinned-uses - pinned all actions to full SHAs: actions/checkout@v7->3d3c42e5, actions/setup-python@v7->5fda3b95, actions/upload-artifact@v7->043fb46d, github/codeql-action/*@v4->e4fba868, cicirello/user-statistician@v1->36500351, actions/stale@v10->1e223db2; (3) missing-permissions - added 'permissions: contents: write' to generate-international-samples.yml, generate-samples.yml, major-release-num.yml, and manual-theme-sample.yml.

