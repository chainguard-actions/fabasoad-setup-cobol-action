<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.6.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fabasoad--setup-cobol-action/v1.6.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: The 'Install COBOL' step in action.yml directly interpolates the expression `${{ steps.info.outputs.bin-path }}` inside a `run:` shell command string: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`.

The `steps.*.outputs.*` context is a workflow-controllable value that flows through YAML template substitution before the shell ever sees it. If a prior step's output contains shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.), they will be interpreted by the shell, enabling command injection. The value should instead be passed via an `env:` variable and then referenced as a properly double-quoted shell variable (e.g., `"$BIN_PATH"`).

Locations:

- `action.yml:52`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script-injection in the 'Install COBOL' step of action.yml. Moved `${{ steps.info.outputs.bin-path }}` from the `run:` shell string into the step's `env:` block as `BIN_PATH`, and updated the shell command to reference it as `"${BIN_PATH}"` instead of directly interpolating the expression.

