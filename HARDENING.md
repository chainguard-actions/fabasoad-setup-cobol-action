<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fabasoad--setup-cobol-action/v1.5.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): In the 'Install COBOL' step of action.yml, the expression `${{ steps.info.outputs.bin-path }}` is directly interpolated inside a `run:` shell command string: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`.

Any `${{ ... }}` expression embedded directly in a `run:` block is substituted by the Actions runner before the shell ever sees the command, allowing an attacker who can influence the step output value to inject arbitrary shell metacharacters. The value should instead be passed via an `env:` variable and then referenced as a quoted shell variable (e.g., `"$BIN_PATH"`) inside the script.

Locations:

- `action.yml:50`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script-injection in the 'Install COBOL' step of action.yml (line 50). Moved `${{ steps.info.outputs.bin-path }}` out of the `run:` shell command string and into the `env:` block as `BIN_PATH: "${{ steps.info.outputs.bin-path }}"`. The shell command now safely references it as `"${BIN_PATH}"` instead of directly interpolating the expression.

