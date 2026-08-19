<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-cobol-action/v1.6.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Install COBOL' step in action.yml directly interpolates `${{ steps.info.outputs.bin-path }}` inside a `run:` shell command string: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`  — the `steps.*.outputs.*` context is workflow-controllable and flows through YAML template substitution before the shell sees it, enabling command injection. The value should be passed via an `env:` variable and double-quoted in the script instead.

Locations:

- `action.yml:54`

### script-injection (severity: high)

Sub-rule (a): The 'Test action completion' run block in functional-tests.yml directly interpolates `${{ steps.setup-cobol-1.outputs.installed }}`, `${{ steps.setup-cobol-2.outputs.installed }}`, and `${{ matrix.force }}` inside shell command strings. All three are workflow-controllable contexts (`steps.*.outputs.*` and `matrix.*`) that are substituted before the shell parses the script, enabling command injection. These values should be routed through `env:` variables and double-quoted.

Locations:

- `.github/workflows/functional-tests.yml:65`
- `.github/workflows/functional-tests.yml:67`
- `.github/workflows/functional-tests.yml:68`

### unpinned-uses (severity: high)

Multiple workflow files reference actions and reusable workflows by mutable tag or branch instead of a full 40-character commit SHA, making them vulnerable to supply-chain attacks if the referenced ref is moved or overwritten. Failing references:
- functional-tests.yml: `actions/checkout@v5` (appears twice — tag `v5`)
- linting.yml: `fabasoad/reusable-workflows/.github/workflows/wf-pre-commit.yml@main` (branch `main`)
- release.yml: `fabasoad/reusable-workflows/.github/workflows/wf-github-release.yml@main` (branch `main`)
- security.yml: `fabasoad/reusable-workflows/.github/workflows/wf-security-sast.yml@main` (branch `main`)
- sync-labels.yml: `fabasoad/reusable-workflows/.github/workflows/wf-sync-labels.yml@main` (branch `main`)
- update-license.yml: `fabasoad/reusable-workflows/.github/workflows/wf-update-license.yml@main` (branch `main`)
All should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v5`.

Locations:

- `.github/workflows/functional-tests.yml:32`
- `.github/workflows/functional-tests.yml:49`
- `.github/workflows/linting.yml:10`
- `.github/workflows/release.yml:9`
- `.github/workflows/security.yml:18`
- `.github/workflows/sync-labels.yml:10`
- `.github/workflows/update-license.yml:9`

### missing-permissions (severity: medium)

Five workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, GitHub Actions grants the default token permissions (which can be broad, especially on older repositories or those with permissive defaults), violating the principle of least privilege. Each workflow should declare the minimal permissions required.
- functional-tests.yml: two jobs (`run-script`, `test-force`), neither has permissions
- linting.yml: one job (`pre-commit`), no permissions
- release.yml: one job (`github`), no permissions
- sync-labels.yml: one job (`maintenance`), no permissions
- update-license.yml: one job (`maintenance`), no permissions

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

Fixed all 4 findings across 7 files:

1. **script-injection (action.yml)**: Moved `${{ steps.info.outputs.bin-path }}` out of the `run:` shell string into an `env:` variable `BIN_PATH`, referenced as `${BIN_PATH}` in the script.

2. **script-injection (functional-tests.yml)**: Moved `${{ steps.setup-cobol-1.outputs.installed }}`, `${{ steps.setup-cobol-2.outputs.installed }}`, and `${{ matrix.force }}` into `env:` variables (`INSTALLED_1`, `INSTALLED_2`, `MATRIX_FORCE`) and referenced them as plain shell variables.

3. **unpinned-uses**: Pinned `actions/checkout@v5` → `@fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09 # v5` in functional-tests.yml (both jobs). Pinned all `fabasoad/reusable-workflows@main` references → `@10062f8186847226cb4865efbb8047795d372bae # main` in linting.yml, release.yml, security.yml, sync-labels.yml, and update-license.yml.

4. **missing-permissions**: Added job-level `permissions:` blocks to functional-tests.yml (both jobs: `contents: read`), linting.yml (`contents: read`), release.yml (`contents: write`), sync-labels.yml (`issues: write, contents: read`), and update-license.yml (`contents: write`). security.yml already had permissions defined.

