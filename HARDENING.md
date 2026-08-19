<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.6.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-cobol-action/v1.6.1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Install COBOL' step directly interpolates `${{ steps.info.outputs.bin-path }}` into the `run:` shell command string. This expression is passed as a positional argument to the shell script without going through an env: variable, meaning the YAML template substitution happens before the shell ever sees the value. A malicious value containing shell metacharacters could achieve command injection. The offending line is: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`

Locations:

- `action.yml:48`

### script-injection (severity: high)

Sub-rule (a): The 'Test action completion' step in the test-force job directly interpolates `${{ steps.setup-cobol-1.outputs.installed }}`, `${{ steps.setup-cobol-2.outputs.installed }}`, and `${{ matrix.force }}` into the `run:` shell command string. These expressions are substituted by the YAML template engine before the shell executes the script, allowing an attacker who can influence step outputs or matrix values to inject shell metacharacters. Offending lines include: `"${{ steps.setup-cobol-1.outputs.installed }}"`, `"${{ steps.setup-cobol-2.outputs.installed }}"`, and `"${{ matrix.force }}"`.

Locations:

- `.github/workflows/functional-tests.yml:57`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions and reusable workflows using mutable tags or branch names instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved or compromised.

Failing references:
- functional-tests.yml: `actions/checkout@v6` (appears twice, tag reference)
- linting.yml: `fabasoad/reusable-workflows/.github/workflows/wf-pre-commit.yml@main` (branch reference)
- release.yml: `fabasoad/reusable-workflows/.github/workflows/wf-github-release.yml@main` (branch reference)
- security.yml: `fabasoad/reusable-workflows/.github/workflows/wf-security-sast.yml@main` (branch reference)
- sync-labels.yml: `fabasoad/reusable-workflows/.github/workflows/wf-sync-labels.yml@main` (branch reference)
- update-license.yml: `fabasoad/reusable-workflows/.github/workflows/wf-update-license.yml@main` (branch reference)

Locations:

- `.github/workflows/functional-tests.yml:27`
- `.github/workflows/functional-tests.yml:42`
- `.github/workflows/linting.yml:13`
- `.github/workflows/release.yml:11`
- `.github/workflows/security.yml:16`
- `.github/workflows/sync-labels.yml:12`
- `.github/workflows/update-license.yml:11`

### missing-permissions (severity: medium)

The workflow file functional-tests.yml has no top-level `permissions:` key, and neither of its two jobs (`run-script` and `test-force`) defines a job-level `permissions:` block. This means the workflow runs with the default GitHub token permissions, which may be broader than necessary (e.g., write access to contents by default on some repository configurations). All other workflow files in this repository correctly define job-level permissions.

Locations:

- `.github/workflows/functional-tests.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all four findings:

1. **script-injection (action.yml)**: Moved `${{ steps.info.outputs.bin-path }}` from the `run:` shell string into the step's `env:` block as `BIN_PATH`, then referenced it as `"${BIN_PATH}"` in the shell command.

2. **script-injection (functional-tests.yml)**: Moved `${{ steps.setup-cobol-1.outputs.installed }}`, `${{ steps.setup-cobol-2.outputs.installed }}`, and `${{ matrix.force }}` from the `run:` shell string into the step's `env:` block as `INSTALLED_1`, `INSTALLED_2`, and `MATRIX_FORCE`, then referenced them as plain env vars.

3. **unpinned-uses**: Pinned `actions/checkout@v6` to `@df4cb1c069e1874edd31b4311f1884172cec0e10 # v6` in functional-tests.yml (both jobs). Pinned all five `fabasoad/reusable-workflows/...@main` references to `@4e2279474e598bee3ae8ded28899a24bbc7bf971 # main` in linting.yml, release.yml, security.yml, sync-labels.yml, and update-license.yml.

4. **missing-permissions**: Added `permissions: {}` at the workflow top level and `permissions: contents: read` at the job level for both jobs in functional-tests.yml.

