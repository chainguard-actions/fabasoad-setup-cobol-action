<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.6.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-cobol-action/v1.6.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: A GitHub Actions expression `${{ steps.info.outputs.bin-path }}` is directly interpolated inside a `run:` shell command string in the 'Install COBOL' step. The offending line is:

  run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"

The `steps.info.outputs.bin-path` value is written by `collect-info.sh` and is derived from `RUNNER_TEMP` and a timestamp, but it flows through a step output and is then re-injected via YAML template substitution directly into the shell command before the shell parses it. Any workflow-level manipulation of that output (e.g. via a compromised prior step) could inject arbitrary shell metacharacters. The value must be passed via an `env:` variable and referenced as a quoted shell variable instead:

  env:
    INPUT_VERSION: "${{ inputs.version }}"
    BIN_PATH: "${{ steps.info.outputs.bin-path }}"
  run: ./install-cobc.sh "${INPUT_VERSION}" "${BIN_PATH}"

Locations:

- `action.yml:54`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script-injection in action.yml 'Install COBOL' step: moved `${{ steps.info.outputs.bin-path }}` from the `run:` shell command string into the `env:` block as `BIN_PATH`, and updated the command to reference it as the quoted shell variable `"${BIN_PATH}"`. This prevents potential shell metacharacter injection via YAML template substitution.

