<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fabasoad--setup-cobol-action/v1.6.0** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: The `${{ steps.info.outputs.bin-path }}` expression from the `steps.*.outputs.*` context is directly interpolated inside a `run:` shell command string in the 'Install COBOL' step. This allows a workflow-controllable value to be injected into the shell before quoting, enabling command injection. The offending line is: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`

Fix: Move the value into an `env:` variable and reference it as a quoted shell variable, e.g.:
```yaml
env:
  INPUT_VERSION: "${{ inputs.version }}"
  BIN_PATH: "${{ steps.info.outputs.bin-path }}"
run: ./install-cobc.sh "${INPUT_VERSION}" "${BIN_PATH}"
```

Locations:

- `action.yml:51`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script-injection in the 'Install COBOL' step of action.yml. Moved `${{ steps.info.outputs.bin-path }}` from the `run:` shell command string into the step's `env:` block as `BIN_PATH: "${{ steps.info.outputs.bin-path }}"`, and updated the run command to use `"${BIN_PATH}"` as a safe shell variable reference instead of direct expression interpolation.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in src/collect-info.sh at line 27. The bin_path variable (constructed from $RUNNER_TEMP) was being written directly to $GITHUB_OUTPUT without sanitization. Added a sanitization step using `safe_bin_path=$(printf '%s' "${bin_path}" | tr -d '\n\r')` and changed the echo to use `safe_bin_path` instead of `bin_path`, preventing newline injection attacks via a maliciously crafted RUNNER_TEMP environment variable.

