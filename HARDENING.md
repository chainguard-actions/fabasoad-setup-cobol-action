<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-cobol-action/v1.7.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): a GitHub Actions expression is placed directly inside a run: script in the 'Install COBOL' step: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`. The template is filled in before the shell parses the line, so whatever is in steps.info.outputs.bin-path becomes part of the command. Fix: pass it through env (e.g. BIN_PATH: ${{ steps.info.outputs.bin-path }}) and use "${BIN_PATH}" in the script.

Locations:

- `action.yml:54`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 9aa1c38..72c0f96 100644
--- a/action.yml
+++ b/action.yml
@@ -23,6 +23,23 @@ outputs:
 runs:
   using: "composite"
   steps:
     - name: Fail
       if: ${{ runner.os != 'Linux' }}
       run: |
@@ -51,7 +68,8 @@ runs:
       id: install-cobc
       env:
         INPUT_VERSION: "${{ inputs.version }}"
-      run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"
+        BIN_PATH: "${{ steps.info.outputs.bin-path }}"
+      run: ./install-cobc.sh "${INPUT_VERSION}" "${BIN_PATH}"
       shell: sh
       working-directory: ${{ github.action_path }}/src
 

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, prior-fix

**Notes:**

Fixed script injection in the 'Install COBOL' step in action.yml. The steps.info.outputs.bin-path expression no longer sits inside the run: line. It is now passed in through env as BIN_PATH, and the script uses "${BIN_PATH}" in its place. This is the same change the previous harden made, so it also covers the prior-fix finding. The step runs under shell: sh, so the $VAR syntax is correct. Nothing was run to test it.

