<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.5.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fabasoad--setup-cobol-action/v1.5.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): In the 'Install COBOL' step, the expression `${{ steps.info.outputs.bin-path }}` is directly interpolated inside a `run:` shell command string. The `steps.*.outputs.*` context flows through YAML template substitution before the shell processes it, allowing an attacker who can influence that output value to inject arbitrary shell commands. The offending line is: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`

Fix: move the value into an `env:` variable and double-quote it in the script, e.g.:
```yaml
env:
  INPUT_VERSION: "${{ inputs.version }}"
  BIN_PATH: "${{ steps.info.outputs.bin-path }}"
run: ./install-cobc.sh "${INPUT_VERSION}" "${BIN_PATH}"
```

Locations:

- `action.yml:54`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in the 'Install COBOL' step of action.yml. Moved `${{ steps.info.outputs.bin-path }}` from the `run:` shell command string into a new `BIN_PATH` env variable, then referenced it as `${BIN_PATH}` in the shell script. This prevents the GitHub Actions expression from being interpolated directly into the shell command, eliminating the risk of shell command injection via attacker-controlled step output values.

