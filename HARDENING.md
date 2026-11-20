<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-cobol-action/v1.6.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-cobol-action/v1.6.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violated: the 'Install COBOL' step puts a GitHub Actions expression straight into its run: script: `run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"`. The steps.info.outputs.bin-path value is substituted into the shell script by template expansion before the shell parses it. Fix: map it to an env var (for example BIN_PATH: ${{ steps.info.outputs.bin-path }}) and reference it as "${BIN_PATH}".

Locations:

- `action.yml:54`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 9aa1c38..88a7930 100644
--- a/action.yml
+++ b/action.yml
@@ -1,4 +1,3 @@
----
 name: Setup COBOL
 author: Yevhen Fabizhevskyi
 description: This action sets up GnuCOBOL.
@@ -23,13 +22,29 @@ outputs:
 runs:
   using: "composite"
   steps:
     - name: Fail
       if: ${{ runner.os != 'Linux' }}
       run: |
         echo "::error title=OS is not supported::${RUNNER_OS} ${RUNNER_ARCH} is not supported"
         exit 1
       shell: sh
-
     - name: Validate inputs
       id: validate-inputs
       env:
@@ -37,7 +52,6 @@ runs:
       run: ./validate-inputs.sh "${INPUT_FORCE}"
       shell: sh
       working-directory: ${{ github.action_path }}/src
-
     - name: Collect info
       id: info
       env:
@@ -45,16 +59,15 @@ runs:
       run: ./collect-info.sh "${INPUT_FORCE}"
       shell: sh
       working-directory: ${{ github.action_path }}/src
-
     - name: Install COBOL
       if: ${{ steps.info.outputs.bin-installed == 'false' }}
       id: install-cobc
       env:
         INPUT_VERSION: "${{ inputs.version }}"
-      run: ./install-cobc.sh "${INPUT_VERSION}" "${{ steps.info.outputs.bin-path }}"
+        BIN_PATH: "${{ steps.info.outputs.bin-path }}"
+      run: ./install-cobc.sh "${INPUT_VERSION}" "${BIN_PATH}"
       shell: sh
       working-directory: ${{ github.action_path }}/src
-
     - name: Print version
       run: cobc --info
       shell: sh

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, prior-fix

**Notes:**

Moved steps.info.outputs.bin-path into env BIN_PATH in the Install COBOL step and referenced it as "${BIN_PATH}". This is the same change the previous harden made (the other parts of that diff were only whitespace).

