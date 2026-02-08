---
name: wsl-terminal-priority
description: On Windows, prefer WSL for terminal commands. Uses Homebrew/Linuxbrew-first toolchain, stable command templates, and explicit fallback rules. Covers path translation, DNS fixes, credential sharing, and Windows-only exclusions.
license: MIT
compatibility: Windows with WSL2 installed. Requires bash in WSL.
metadata:
  author: simon
  version: "1.0"
---

# WSL Terminal Priority

On Windows, default to **WSL** for terminal commands. Fall back to native Windows shells only when WSL is unavailable or the task requires Windows-native tools.

## Triggers

- Terminal commands on Windows (git, build, test, package managers, scripts)
- Requests for Linux-like behavior or path handling
- "在 Windows 上跑命令" / "用终端执行" / "安装 CLI 工具"

## Windows-only exclusions (do NOT use WSL)

Run these in PowerShell/CMD directly:
- `winget`, `scoop`, `choco`, PowerShell cmdlets, registry ops (`reg`, `regedit`)
- `sc.exe`, `net start/stop`, `systeminfo`, `ipconfig`, `netsh`, `dism`, `sfc`
- `.msi`/`.exe` installers, Windows GUI apps (`explorer.exe`, `code.exe`)
- `.NET CLI` (`dotnet`), `msbuild` when targeting Windows

**Rule**: Windows-native tool with no WSL equivalent → run in PowerShell/CMD.

## Command templates

- Stable (preferred): `wsl.exe -e bash --noprofile --norc -lc "<command>"`
- With working dir: `wsl.exe -e bash --noprofile --norc -lc "cd /mnt/<drive>/<path> && <command>"`
- Use `--noprofile --norc` to avoid profile/path pollution.

## Core behavior

1. **Default to WSL** for terminal execution.
2. Verify WSL availability first if uncertain (`wsl.exe --status`).
3. If WSL fails: explain why, then use Windows shell as fallback.
4. Use default distro (`wsl.exe` without `-d`). Check `wsl.exe -l -v` if unsure.

## Toolchain preference

- Prefer Homebrew (Linuxbrew) over system packages. No sudo needed.
- Discovery order: `command -v <tool>` → `/home/linuxbrew/.linuxbrew/bin/<tool>` → fallback `apt` with reason.
- `brew` not in PATH? Use absolute path: `/home/linuxbrew/.linuxbrew/bin/<tool>`.

## Path translation

- `D:\foo\bar` → `/mnt/d/foo/bar` (drive letter lowercase, forward slashes).
- Quote paths with spaces on both sides of the boundary.

## Timeout & sudo

- Split slow commands into smaller steps. Check partial completion before retry.
- Use `sudo -n` (non-interactive) to fail fast. If password needed, stop and ask user.

## Fallback boundary

- Fallback to PowerShell/CMD only when: WSL unavailable, tool unresolvable in WSL, or task is Windows-only.
- **Never fallback silently.** State what failed and which shell is used instead.

## Guardrails

- Do not change command semantics when switching shells.
- Do not introduce extra dependencies just to enforce WSL.
- Do not force-install via `apt` if Brew equivalent exists.

## Decision flow

1. Windows terminal command needed.
2. Windows-only tool/task? → Yes → PowerShell/CMD.
3. No → WSL first → Success → continue in WSL.
4. WSL fails → inform user → fallback to Windows shell.

## References

See [references/REFERENCE.md](references/REFERENCE.md) for detailed cross-boundary considerations including DNS fixes, file system performance, CRLF handling, credential sharing, environment variable isolation, WSL config files, and networking modes.
