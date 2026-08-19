<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.6.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-cobol-action/v1.6.2** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Install COBOL' step in action.yml directly interpolates `${{ steps.info.outputs.bin-path }}` inside a `run:` shell command string. This expression is substituted by the YAML template engine before the shell sees it, allowing injection of shell metacharacters. The offending line is: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`

Locations:

- `action.yml:49`

### script-injection (severity: high)

Sub-rule (a): The 'Test action completion' step in functional-tests.yml directly interpolates `${{ steps.setup-cobol-1.outputs.installed }}`, `${{ steps.setup-cobol-2.outputs.installed }}`, and `${{ matrix.force }}` inside a multi-line `run:` shell script. These expressions are substituted before the shell executes, enabling injection of shell metacharacters via action outputs or matrix values.

Locations:

- `.github/workflows/functional-tests.yml:56`

### unpinned-uses (severity: high)

Multiple workflow files reference actions/reusable workflows using mutable tag or branch refs instead of pinned 40-character commit SHAs. Unpinned refs are vulnerable to supply-chain attacks if the upstream ref is moved or compromised. Failing references: functional-tests.yml: `actions/checkout@v6` (appears twice); linting.yml: `fabasoad/reusable-workflows/.github/workflows/wf-pre-commit.yml@main`; release.yml: `fabasoad/reusable-workflows/.github/workflows/wf-github-release.yml@main`; security.yml: `fabasoad/reusable-workflows/.github/workflows/wf-security-sast.yml@main`; sync-labels.yml: `fabasoad/reusable-workflows/.github/workflows/wf-sync-labels.yml@main`; update-license.yml: `fabasoad/reusable-workflows/.github/workflows/wf-update-license.yml@main`.

Locations:

- `.github/workflows/functional-tests.yml:20`
- `.github/workflows/functional-tests.yml:37`
- `.github/workflows/linting.yml:11`
- `.github/workflows/release.yml:9`
- `.github/workflows/security.yml:20`
- `.github/workflows/sync-labels.yml:11`
- `.github/workflows/update-license.yml:11`

### missing-permissions (severity: medium)

The workflow file functional-tests.yml has no top-level `permissions:` key and neither of its jobs (`run-script`, `test-force`) defines a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g., write access to contents).

Locations:

- `.github/workflows/functional-tests.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all four findings: (1) action.yml script injection: moved `${{ steps.info.outputs.bin-path }}` into env block as BIN_PATH; (2) functional-tests.yml script injection: moved steps outputs and matrix.force into env block; (3) unpinned-uses: pinned actions/checkout@v6 to SHA d23441a48e516b6c34aea4fa41551a30e30af803 and all fabasoad/reusable-workflows@main references to SHA 10062f8186847226cb4865efbb8047795d372bae across 5 workflow files; (4) missing-permissions: added `permissions: {}` at top level of functional-tests.yml.

