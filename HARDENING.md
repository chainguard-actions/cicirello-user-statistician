<!-- markdownlint-disable -->

# Hardening Report: cicirello--user-statistician/v1.24.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cicirello--user-statistician/v1.24.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags (e.g. @v4, @v5, @v3, @v9, @v1) instead of full 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the tag is moved to a malicious commit. Affected references include: actions/checkout@v4, actions/setup-python@v5, actions/upload-artifact@v4, github/codeql-action/init@v3, github/codeql-action/autobuild@v3, github/codeql-action/analyze@v3, cicirello/user-statistician@v1, actions/stale@v9.

Locations:

- `.github/workflows/build.yml:19`
- `.github/workflows/build.yml:23`
- `.github/workflows/build.yml:50`
- `.github/workflows/codeql-analysis.yml:38`
- `.github/workflows/codeql-analysis.yml:43`
- `.github/workflows/codeql-analysis.yml:52`
- `.github/workflows/codeql-analysis.yml:57`
- `.github/workflows/generate-international-samples.yml:11`
- `.github/workflows/generate-international-samples.yml:15`
- `.github/workflows/generate-international-samples.yml:24`
- `.github/workflows/generate-samples.yml:13`
- `.github/workflows/generate-samples.yml:17`
- `.github/workflows/generate-samples.yml:33`
- `.github/workflows/generate-samples.yml:43`
- `.github/workflows/major-release-num.yml:13`
- `.github/workflows/manual-theme-sample.yml:14`
- `.github/workflows/manual-theme-sample.yml:19`
- `.github/workflows/stale.yml:9`

### script-injection (severity: high)

Sub-rule (a): In build.yml, the run: block directly interpolates a ${{ ... }} expression inside a shell command string: `echo "exit-code = ${{ steps.integration.outputs.exit-code }}"`. The expression `steps.integration.outputs.exit-code` is expanded by the GitHub Actions template engine before the shell sees the command, allowing any value in that output (which itself originates from the Docker action) to be injected into the shell command. All ${{ ... }} expressions inside run: blocks are a script-injection risk regardless of the context they read from.

Locations:

- `.github/workflows/build.yml:48`

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, workflows run with the repository's default token permissions, which may be overly broad (e.g. write access to contents). Each of these files should declare minimal required permissions: generate-international-samples.yml, generate-samples.yml, manual-theme-sample.yml, and major-release-num.yml.

Locations:

- `.github/workflows/generate-international-samples.yml:1`
- `.github/workflows/generate-samples.yml:1`
- `.github/workflows/manual-theme-sample.yml:1`
- `.github/workflows/major-release-num.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three finding types across 6 workflow files:

1. unpinned-uses: Pinned all 8 action references to full 40-char commit SHAs with tag comments for readability. Affected files: build.yml, codeql-analysis.yml, generate-international-samples.yml, generate-samples.yml, manual-theme-sample.yml, major-release-num.yml, stale.yml.

2. script-injection: In build.yml, moved `${{ steps.integration.outputs.exit-code }}` from the run: shell string into the step's env: block as EXIT_CODE, then referenced it as $EXIT_CODE in the shell script.

3. missing-permissions: Added top-level `permissions:` blocks to generate-international-samples.yml, generate-samples.yml, manual-theme-sample.yml (all set to `contents: write` since they commit/push SVG files), and major-release-num.yml (set to `contents: write` since it pushes git tags).

