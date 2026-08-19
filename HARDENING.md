<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-cobol-action/v1.5.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): A GitHub Actions expression is directly interpolated inside a run: shell command string. In the 'Install COBOL' step, `${{ steps.info.outputs.bin-path }}` is embedded directly in the run: block: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`  This allows the value to be parsed by the YAML template engine before the shell ever sees it, enabling command injection if the value contains shell metacharacters.

Locations:

- `action.yml:48`

### script-injection (severity: high)

Rule (a): GitHub Actions expressions are directly interpolated inside a run: shell command string in the 'Test action completion' step. The offending lines are:
  `"${{ steps.setup-cobol-1.outputs.installed }}"`
  `"${{ steps.setup-cobol-2.outputs.installed }}"`
  `"${{ matrix.force }}"`
These values flow through YAML template substitution before the shell processes them, enabling script injection if any value contains shell metacharacters.

Locations:

- `.github/workflows/functional-tests.yml:55`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/functional-tests.yml:1`
- `.github/workflows/linting.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/sync-labels.yml:1`
- `.github/workflows/update-license.yml:1`

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags or branch names instead of immutable 40-character commit SHAs, making the workflow vulnerable to supply-chain attacks if the referenced tag or branch is updated or hijacked:
- `actions/checkout@v4` (tag, not SHA) in functional-tests.yml
- `fabasoad/reusable-workflows/.github/workflows/wf-pre-commit.yml@main` (branch) in linting.yml
- `fabasoad/reusable-workflows/.github/workflows/wf-github-release.yml@main` (branch) in release.yml
- `fabasoad/reusable-workflows/.github/workflows/wf-security-sast.yml@main` (branch) in security.yml
- `fabasoad/reusable-workflows/.github/workflows/wf-sync-labels.yml@main` (branch) in sync-labels.yml
- `fabasoad/reusable-workflows/.github/workflows/wf-update-license.yml@main` (branch) in update-license.yml

Locations:

- `.github/workflows/functional-tests.yml:29`
- `.github/workflows/linting.yml:10`
- `.github/workflows/release.yml:9`
- `.github/workflows/security.yml:11`
- `.github/workflows/sync-labels.yml:10`
- `.github/workflows/update-license.yml:10`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions, unpinned-uses

**Notes:**

Fixed all 4 findings:
1. script-injection (action.yml line 48): Moved `${{ steps.info.outputs.bin-path }}` to env block as BIN_PATH, referenced as ${BIN_PATH} in shell.
2. script-injection (functional-tests.yml line 55): Moved steps.setup-cobol-1.outputs.installed, steps.setup-cobol-2.outputs.installed, and matrix.force to env block as INSTALLED_1, INSTALLED_2, MATRIX_FORCE.
3. missing-permissions: Added `permissions: {}` top-level to functional-tests.yml, linting.yml, release.yml, sync-labels.yml, and update-license.yml.
4. unpinned-uses: Pinned actions/checkout@v4 → SHA 11d5960a326750d5838078e36cf38b85af677262 in functional-tests.yml; pinned all fabasoad/reusable-workflows @main references → SHA 10062f8186847226cb4865efbb8047795d372bae in linting.yml, release.yml, security.yml, sync-labels.yml, and update-license.yml.

