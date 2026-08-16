<!-- markdownlint-disable -->

# Hardening Report: cicirello--user-statistician/v1.24.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cicirello--user-statistician/v1.24.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression is directly interpolated inside a `run:` shell command. In `.github/workflows/build.yml` line 48, `${{ steps.integration.outputs.exit-code }}` is embedded directly in an `echo` command: `echo "exit-code = ${{ steps.integration.outputs.exit-code }}"`. The `steps.*.outputs.*` context is workflow-controllable and flows through YAML template substitution before the shell sees it, enabling script injection. Fix: use an `env:` variable and reference it as `"$EXIT_CODE"` instead.

Locations:

- `.github/workflows/build.yml:48`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags instead of pinned 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced tag is moved or compromised. Failing references include: build.yml: `actions/checkout@v6`, `actions/setup-python@v6`, `actions/upload-artifact@v5`; codeql-analysis.yml: `actions/checkout@v6`, `github/codeql-action/init@v4`, `github/codeql-action/autobuild@v4`, `github/codeql-action/analyze@v4`; generate-international-samples.yml: `actions/checkout@v6`, `cicirello/user-statistician@v1` (×2); generate-samples.yml: `actions/checkout@v6`, `cicirello/user-statistician@v1` (×3); major-release-num.yml: `actions/checkout@v6`; manual-theme-sample.yml: `actions/checkout@v6`, `cicirello/user-statistician@v1`; stale.yml: `actions/stale@v10`.

Locations:

- `.github/workflows/build.yml:20`
- `.github/workflows/build.yml:23`
- `.github/workflows/build.yml:52`
- `.github/workflows/codeql-analysis.yml:32`
- `.github/workflows/codeql-analysis.yml:36`
- `.github/workflows/codeql-analysis.yml:48`
- `.github/workflows/codeql-analysis.yml:57`
- `.github/workflows/generate-international-samples.yml:12`
- `.github/workflows/generate-international-samples.yml:16`
- `.github/workflows/generate-international-samples.yml:26`
- `.github/workflows/generate-samples.yml:14`
- `.github/workflows/generate-samples.yml:18`
- `.github/workflows/generate-samples.yml:34`
- `.github/workflows/generate-samples.yml:46`
- `.github/workflows/major-release-num.yml:12`
- `.github/workflows/manual-theme-sample.yml:18`
- `.github/workflows/manual-theme-sample.yml:22`
- `.github/workflows/stale.yml:9`

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` blocks, meaning they run with the default (potentially broad) token permissions: `generate-international-samples.yml`, `generate-samples.yml`, `major-release-num.yml`, and `manual-theme-sample.yml`. Each should declare minimal required permissions (e.g., `contents: write` only if needed for push, otherwise `contents: read`).

Locations:

- `.github/workflows/generate-international-samples.yml:1`
- `.github/workflows/generate-samples.yml:1`
- `.github/workflows/major-release-num.yml:1`
- `.github/workflows/manual-theme-sample.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three finding types: (1) script-injection in build.yml line 48 - moved `${{ steps.integration.outputs.exit-code }}` to an env block as EXIT_CODE and referenced it as $EXIT_CODE in the shell; (2) unpinned-uses - pinned all 18 action references across 7 workflow files to full 40-char SHA digests with original tags as comments (actions/checkout@v6→d23441a4, actions/setup-python@v6→ece7cb06, actions/upload-artifact@v5→330a01c4, github/codeql-action/*@v4→7188fc36, cicirello/user-statistician@v1→1c77e7a9, actions/stale@v10→1e223db2); (3) missing-permissions - added `permissions: contents: write` to generate-international-samples.yml, generate-samples.yml, major-release-num.yml, and manual-theme-sample.yml (all need contents:write to push to the samples branch or update tags).

