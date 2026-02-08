# WSL Terminal Priority

## Purpose
On Windows, always prefer running command-line operations in **WSL** first. Fall back to native Windows shells only when WSL is unavailable or the task explicitly requires Windows-native tools.

## When this skill applies
- The environment is Windows (`win32` / Windows host).
- A task requires terminal commands (git, build, test, package managers, scripts, etc.).
- The user asks for Linux-like command behavior or path handling.

## Core behavior
1. **Default to WSL** for terminal execution.
2. If WSL may be unavailable, quickly verify first (for example with `wsl.exe --status` or a minimal command).
3. If WSL is not available or fails to launch:
   - Explain briefly why fallback is needed.
   - Use Windows-native shell as fallback.
4. Keep paths and quoting safe when crossing Windows/WSL boundaries.

## Execution policy
- Prefer invoking commands through WSL, e.g.:
  - `wsl.exe -e bash -lc "<command>"`
- For repository work, ensure the working directory maps correctly in WSL before running commands.
- Do not silently switch to PowerShell/CMD if WSL fails; state the reason.

## Package manager / toolchain preference
- If Homebrew (Linuxbrew) exists in WSL, prefer it first for CLI tools.
- Prefer `brew`-installed binaries over system binaries when both are available.
- If `brew` is not in PATH but Linuxbrew exists, use absolute path binaries when needed:
  - `/home/linuxbrew/.linuxbrew/bin/<tool>`
- Only fall back to `apt` (or other package managers) when Brew is unavailable or the package is not provided by Brew.
- Keep behavior explicit: when switching from Brew to fallback, state the reason.

## Communication requirements
- Before first terminal use in a task, mention that WSL is being used by default on Windows.
- If fallback is required, state:
  - What failed in WSL
  - What fallback shell is used
  - Any behavior differences that may affect results

## Guardrails
- Do **not** change user intent or command semantics while switching shells.
- Do **not** introduce extra dependencies just to enforce WSL.
- Keep command output handling consistent with normal workflow.
- Do **not** force-install tools with `apt` if equivalent Brew tool is already available.

## Example decision flow
1. Need to run terminal command on Windows.
2. Run with WSL first.
3. Success → continue in WSL.
4. Failure due to missing/unusable WSL → inform user and fall back to Windows shell.
