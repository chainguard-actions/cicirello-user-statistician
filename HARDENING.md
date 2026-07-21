<!-- markdownlint-disable -->

# Hardening Report: cicirello--user-statistician/v1.26.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cicirello--user-statistician/v1.26.2** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references in workflow files use mutable tag refs instead of pinned 40-character SHA commit hashes, making the workflows vulnerable to supply-chain attacks if the referenced action tags are moved or compromised.

- build.yml: `actions/checkout@v7`, `actions/setup-python@v7`, `actions/upload-artifact@v7`
- codeql-analysis.yml: `actions/checkout@v7`, `github/codeql-action/init@v4`, `github/codeql-action/autobuild@v4`, `github/codeql-action/analyze@v4`
- generate-international-samples.yml: `actions/checkout@v7`, `cicirello/user-statistician@v1` (×2)
- generate-samples.yml: `actions/checkout@v7`, `cicirello/user-statistician@v1` (×3)
- major-release-num.yml: `actions/checkout@v7`
- manual-theme-sample.yml: `actions/checkout@v7`, `cicirello/user-statistician@v1`
- stale.yml: `actions/stale@v10`

Locations:

- `.github/workflows/build.yml:18`
- `.github/workflows/build.yml:21`
- `.github/workflows/build.yml:48`
- `.github/workflows/codeql-analysis.yml:36`
- `.github/workflows/codeql-analysis.yml:41`
- `.github/workflows/codeql-analysis.yml:47`
- `.github/workflows/codeql-analysis.yml:57`
- `.github/workflows/generate-international-samples.yml:11`
- `.github/workflows/generate-international-samples.yml:16`
- `.github/workflows/generate-international-samples.yml:26`
- `.github/workflows/generate-samples.yml:13`
- `.github/workflows/generate-samples.yml:18`
- `.github/workflows/generate-samples.yml:33`
- `.github/workflows/generate-samples.yml:44`
- `.github/workflows/major-release-num.yml:11`
- `.github/workflows/manual-theme-sample.yml:14`
- `.github/workflows/manual-theme-sample.yml:19`
- `.github/workflows/stale.yml:11`

### script-injection (severity: high)

Rule (a) violation: A `${{ steps.integration.outputs.exit-code }}` expression is interpolated directly inside a `run:` shell command string. The `steps.*.outputs.*` context is workflow-controllable and flows through YAML template substitution before the shell sees it, enabling script injection. Offending line: `echo "exit-code = ${{ steps.integration.outputs.exit-code }}"`

Locations:

- `.github/workflows/build.yml:44`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, workflows run with the default (potentially broad) token permissions.

- generate-international-samples.yml: no permissions defined
- generate-samples.yml: no permissions defined
- major-release-num.yml: no permissions defined
- manual-theme-sample.yml: no permissions defined

Locations:

- `.github/workflows/generate-international-samples.yml:1`
- `.github/workflows/generate-samples.yml:1`
- `.github/workflows/major-release-num.yml:1`
- `.github/workflows/manual-theme-sample.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all 3 findings across 7 workflow files:

1. unpinned-uses: Pinned all 18 `uses:` references to full 40-char SHA hashes with original tag as comment. Actions pinned: actions/checkout@v7 (SHA: 3d3c42e5), actions/setup-python@v7 (SHA: 5fda3b95), actions/upload-artifact@v7 (SHA: 043fb46d), github/codeql-action/{init,autobuild,analyze}@v4 (SHA: 7188fc36), cicirello/user-statistician@v1 (SHA: c264af15), actions/stale@v10 (SHA: 1e223db2).

2. script-injection: In build.yml, moved `${{ steps.integration.outputs.exit-code }}` out of the `run:` shell string into the step's `env:` block as EXIT_CODE, referencing it as `$EXIT_CODE` in the shell script.

3. missing-permissions: Added `permissions: contents: write` to generate-international-samples.yml, generate-samples.yml, major-release-num.yml, and manual-theme-sample.yml (all workflows that push commits or tags to the repository).

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted ${MAJOR} variable expansions in hardened/action/.github/workflows/major-release-num.yml. Changed `git tag -fa ${MAJOR} -m "Update major version tag"` and `git push origin ${MAJOR} --force` to use double-quoted `"${MAJOR}"` in both commands. This prevents shell metacharacter injection via a crafted release tag name derived from GITHUB_REF.

