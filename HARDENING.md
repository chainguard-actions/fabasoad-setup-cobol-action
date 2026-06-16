<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.6.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fabasoad--setup-cobol-action/v1.6.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): In the 'Install COBOL' step, the run: block directly interpolates the expression `${{ steps.info.outputs.bin-path }}` inside the shell command string: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`.

Any `${{ ... }}` expression interpolated directly into a run: block is a script injection risk because YAML template substitution occurs before the shell ever sees the value. The `steps.*.outputs.*` context is a workflow-controllable source. The value should instead be passed via an `env:` variable and then double-quoted in the shell script (e.g., `env: BIN_PATH: ${{ steps.info.outputs.bin-path }}` and `run: ./install-cobc.sh "${INPUT_VERSION}" "${BIN_PATH}"`).

Offending line: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`

Locations:

- `action.yml:54`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in the 'Install COBOL' step of action.yml. Moved `${{ steps.info.outputs.bin-path }}` from the `run:` block into the `env:` block as `BIN_PATH: "${{ steps.info.outputs.bin-path }}"`, and updated the shell command to reference it as `"${BIN_PATH}"` instead of the direct expression interpolation.

