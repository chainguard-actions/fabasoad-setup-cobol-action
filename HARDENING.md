<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.5.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-cobol-action/v1.5.1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation inside a `run:` shell command string. In action.yml, the 'Install COBOL' step interpolates `${{ steps.info.outputs.bin-path }}` directly in the run command: `./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`  — this value flows through YAML template substitution before the shell sees it, enabling script injection. The value should be passed via an `env:` variable and double-quoted instead.

Locations:

- `action.yml:50`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation inside a `run:` shell command string. In functional-tests.yml, the 'Test action completion' step interpolates `${{ steps.setup-cobol-1.outputs.installed }}`, `${{ steps.setup-cobol-2.outputs.installed }}`, and `${{ matrix.force }}` directly inside the run block shell script. These expressions are substituted by the template engine before the shell executes the script, allowing injection of shell metacharacters.

Locations:

- `.github/workflows/functional-tests.yml:60`
- `.github/workflows/functional-tests.yml:63`
- `.github/workflows/functional-tests.yml:65`

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags or branch names instead of immutable 40-character commit SHAs, making the workflows vulnerable to supply-chain attacks if the referenced tag or branch is updated or hijacked. Failing references: `actions/checkout@v5` (tag) in functional-tests.yml (two occurrences); `fabasoad/reusable-workflows/.github/workflows/wf-pre-commit.yml@main` (branch) in linting.yml; `fabasoad/reusable-workflows/.github/workflows/wf-github-release.yml@main` (branch) in release.yml; `fabasoad/reusable-workflows/.github/workflows/wf-security-sast.yml@main` (branch) in security.yml; `fabasoad/reusable-workflows/.github/workflows/wf-sync-labels.yml@main` (branch) in sync-labels.yml; `fabasoad/reusable-workflows/.github/workflows/wf-update-license.yml@main` (branch) in update-license.yml.

Locations:

- `.github/workflows/functional-tests.yml:30`
- `.github/workflows/functional-tests.yml:48`
- `.github/workflows/linting.yml:9`
- `.github/workflows/release.yml:8`
- `.github/workflows/security.yml:19`
- `.github/workflows/sync-labels.yml:9`
- `.github/workflows/update-license.yml:9`

### missing-permissions (severity: medium)

These workflow files have no top-level `permissions:` key and no job-level `permissions:` block on any of their jobs. Without explicit permissions, workflows inherit the default repository token permissions (which may be broad), violating the principle of least privilege. Affected files: functional-tests.yml, linting.yml, release.yml, sync-labels.yml, update-license.yml.

Locations:

- `.github/workflows/functional-tests.yml:1`
- `.github/workflows/linting.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/sync-labels.yml:1`
- `.github/workflows/update-license.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all findings: (1) Script injection in action.yml: moved `steps.info.outputs.bin-path` to env var `BIN_PATH`. (2) Script injection in functional-tests.yml: moved three expressions (`steps.setup-cobol-1.outputs.installed`, `steps.setup-cobol-2.outputs.installed`, `matrix.force`) to env vars `INSTALLED_1`, `INSTALLED_2`, `MATRIX_FORCE`. (3) Unpinned uses: pinned `actions/checkout@v5` to SHA `93cb6efe18208431cddfb8368fd83d5badbf9bfd` (two occurrences in functional-tests.yml); pinned all five `fabasoad/reusable-workflows@main` references to SHA `4e2279474e598bee3ae8ded28899a24bbc7bf971` in linting.yml, release.yml, security.yml, sync-labels.yml, update-license.yml. (4) Missing permissions: added `permissions: {}` top-level block to functional-tests.yml, linting.yml, release.yml, sync-labels.yml, and update-license.yml (security.yml already had job-level permissions).

