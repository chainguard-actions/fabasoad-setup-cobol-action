<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-cobol-action/v1.7.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): a GitHub Actions expression is placed directly inside a run: script in the 'Install COBOL' step: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`. The step output is pasted in by YAML template substitution before the shell parses the line. Fix: pass it through env (e.g. BIN_PATH: ${{ steps.info.outputs.bin-path }}) and use "${BIN_PATH}" in the script.

Locations:

- `action.yml:54`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Moved steps.info.outputs.bin-path into the BIN_PATH env var in the 'Install COBOL' step; the script now receives it as "${BIN_PATH}".

