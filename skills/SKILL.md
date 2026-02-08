# WSL Terminal Priority

## Purpose
On Windows, always prefer running command-line operations in **WSL** first. Fall back to native Windows shells only when WSL is unavailable or the task explicitly requires Windows-native tools.

## When this skill applies
- The environment is Windows (`win32` / Windows host).
- A task requires terminal commands (git, build, test, package managers, scripts, etc.).
- The user asks for Linux-like command behavior or path handling.

## Triggers
- "在 Windows 上跑命令" / "用终端执行" / "安装 CLI 工具"
- Git operations on Windows (status/diff/commit/push/rebase)
- Build/test/package tasks on Windows hosts
- Any request that explicitly asks for WSL-first behavior

## Windows-only exclusions (do NOT use WSL)
The following scenarios must run in native Windows shell directly:
- **Windows package managers**: `winget`, `scoop`, `choco`
- **PowerShell cmdlets**: `Get-Process`, `Set-ExecutionPolicy`, `Get-Service`, etc.
- **Registry operations**: `reg`, `regedit`, or PowerShell registry providers
- **Windows service management**: `sc.exe`, `net start/stop`
- **Windows system tools**: `systeminfo`, `ipconfig`, `netsh`, `tasklist`, `taskkill`
- **Windows feature management**: `dism`, `sfc`, `wsl.exe --install/--update` itself
- **Windows-native installers**: `.msi`, `.exe` installers
- **Opening Windows GUI apps**: `explorer.exe`, `notepad.exe`, `code.exe` (VS Code)
- **Windows-specific dev tools**: `.NET CLI` (`dotnet`), `msbuild` when targeting Windows

**Rule**: If the tool is Windows-native and has no WSL equivalent, or the task explicitly targets the Windows host, run it directly in PowerShell/CMD.

## Core behavior
1. **Default to WSL** for terminal execution.
2. If WSL may be unavailable, quickly verify first (for example with `wsl.exe --status` or a minimal command).
3. If WSL is not available or fails to launch:
   - Explain briefly why fallback is needed.
   - Use Windows-native shell as fallback.
4. Keep paths and quoting safe when crossing Windows/WSL boundaries.

## Multiple WSL distributions
- Default: use the system default WSL distribution (`wsl.exe` without `-d`).
- If a specific distro is needed, use `wsl.exe -d <distro> -e bash ...`.
- When unsure which distro is default, check with `wsl.exe -l -v` first.
- Do not assume distro name; verify before hardcoding.

## Timeout handling
- Long-running commands (e.g., `apt-get update`, large builds) may exceed tool timeout limits.
- For potentially slow operations:
  - Set an explicit timeout if the tool supports it.
  - Split into smaller steps when possible (e.g., update and install separately).
  - If a command times out, check whether it partially completed before retrying.
- Never silently retry a timed-out command that may have side effects (e.g., partial install).

## Sudo strategy
- In non-interactive environments, `sudo` may hang waiting for password input.
- Prefer `sudo -n` (non-interactive) to fail fast instead of hanging.
- If `sudo` is required and password is needed:
  - Stop and inform the user that manual intervention is required.
  - Do not attempt to pipe or echo passwords.
- For package installs, prefer Homebrew (no sudo needed) over `apt` (sudo needed) when possible.

## Execution policy
- Prefer invoking commands through WSL, e.g.:
  - `wsl.exe -e bash -lc "<command>"`
- For repository work, ensure the working directory maps correctly in WSL before running commands.
- Do not silently switch to PowerShell/CMD if WSL fails; state the reason.

## Command templates
- Stable command template (preferred):
  - `wsl.exe -e bash --noprofile --norc -lc "<command>"`
- Repo command template:
  - `wsl.exe -e bash --noprofile --norc -lc "cd /mnt/<drive>/<path> && <command>"`
- Use `--noprofile --norc` to avoid profile/path pollution when reliability matters.

## Package manager / toolchain preference
- If Homebrew (Linuxbrew) exists in WSL, prefer it first for CLI tools.
- Prefer `brew`-installed binaries over system binaries when both are available.
- If `brew` is not in PATH but Linuxbrew exists, use absolute path binaries when needed:
  - `/home/linuxbrew/.linuxbrew/bin/<tool>`
- Only fall back to `apt` (or other package managers) when Brew is unavailable or the package is not provided by Brew.
- Keep behavior explicit: when switching from Brew to fallback, state the reason.

## Tool discovery order
1. `command -v <tool>` in WSL
2. `/home/linuxbrew/.linuxbrew/bin/<tool>`
3. If still missing, use fallback package manager (`apt` etc.) with explicit reason

## GitHub CLI note
- `gh` may only exist at `/home/linuxbrew/.linuxbrew/bin/gh` if installed via Brew but not in PATH.

## Path translation
- `D:\workspace\project` → `/mnt/d/workspace/project` (drive letter lowercase).
- Use forward slashes in WSL. Paths with spaces must be quoted on both sides.
- Translate direction: Windows→WSL before invoking; WSL→Windows before returning to Windows tools.

## Cross-boundary considerations

### Line endings (CRLF vs LF)
- WSL defaults to LF; Windows defaults to CRLF.
- Git in WSL: ensure `core.autocrlf` is set appropriately (recommend `input` in WSL).
- Shell scripts created/edited on Windows may fail in WSL due to `\r`. Use `dos2unix` if needed.
- Be aware when committing from WSL: files should use LF to avoid mixed-ending commits.

### File permissions
- Files on `/mnt/` (Windows drives mounted in WSL) show `777` permissions by default.
- SSH keys on `/mnt/` may trigger "permissions too open" errors. Keep SSH keys in WSL-native `~/.ssh/` instead.
- Do not rely on Unix permission bits for files stored on Windows drives.

### Environment variable isolation
- Windows `PATH` entries leak into WSL by default (via `WSLENV` / `interop`).
- This can cause unexpected `.exe` resolution or path-with-spaces errors.
- When using `--noprofile --norc`, explicitly set needed variables rather than relying on inherited ones.
- To suppress Windows PATH leakage, consider `wsl.exe -e bash --noprofile --norc -lc 'export PATH=/usr/local/bin:/usr/bin:/bin; <command>'`.

### Mixed scenarios
- **Git in WSL + VS Code on Windows**: Use `code .` from WSL (VS Code Remote - WSL handles this).
- **Docker**: Prefer WSL-side `docker` CLI over `docker.exe` to avoid path issues.
- **Node/Python projects**: Run dev servers in WSL; access via `localhost` from Windows browser.

### DNS resolution (WSL2 common issue)
- VPN/proxy may break DNS in WSL2 (`Temporary failure in name resolution`).
- Quick fix: `echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf`.
- Persistent fix: in `/etc/wsl.conf` set `[network] generateResolvConf=false`, then manually set `/etc/resolv.conf`.
- If DNS fails, diagnose before assuming network is down.

### File system performance
- `/mnt/` (Windows drives) is **10-100x slower** than WSL-native filesystem for I/O-heavy operations.
- For performance-sensitive work (large builds, `node_modules`, git operations on big repos), prefer storing projects in `~/projects/` inside WSL.
- Use `/mnt/` only for cross-OS file sharing, not as primary workspace.

### Git credential sharing
- To share Windows Git credentials with WSL, configure:
  - `git config --global credential.helper "/mnt/c/Program\ Files/Git/mingw64/bin/git-credential-manager.exe"`
- SSH keys: keep in WSL-native `~/.ssh/` (not `/mnt/`), or use Windows SSH agent forwarding.

### WSL configuration files
- **`.wslconfig`** (Windows-side, `%USERPROFILE%\.wslconfig`): controls memory limit, swap, processors, networking mode.
- **`wsl.conf`** (per-distro, `/etc/wsl.conf`): controls automount, network, interop, boot commands.
- Key settings: `[interop] appendWindowsPath=false` to stop Windows PATH leakage; `[network] generateResolvConf=false` for custom DNS.

### Networking (WSL2)
- Default: NAT mode. WSL2 has its own virtual network adapter.
- `localhost` forwarding from Windows to WSL2 works automatically for most cases.
- Mirrored networking mode (newer feature): shares host network stack. Enable in `.wslconfig` with `[wsl2] networkingMode=mirrored`.
- If port forwarding fails, check Windows firewall rules.

## Communication requirements
- Mention WSL is being used before first terminal command in a task.
- On fallback: state what failed, which shell is used instead, and any behavior differences.

## Fallback boundary
- Fallback to PowerShell/CMD is allowed only when:
  - WSL is unavailable/unusable, or
  - Required tool cannot be resolved in WSL (including Linuxbrew path), or
  - The task falls under **Windows-only exclusions** (see above).
- Never fallback silently.

## Guardrails
- Do **not** change user intent or command semantics while switching shells.
- Do **not** introduce extra dependencies just to enforce WSL.
- Keep command output handling consistent with normal workflow.
- Do **not** force-install tools with `apt` if equivalent Brew tool is already available.

## Example decision flow
1. Need to run terminal command on Windows.
2. Check: is this a Windows-only tool/task? → Yes → run in PowerShell/CMD directly.
3. No → run with WSL first.
4. Success → continue in WSL.
5. Failure due to missing/unusable WSL → inform user and fall back to Windows shell.
