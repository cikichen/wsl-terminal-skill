# WSL Terminal Priority - Detailed Reference

## Line endings (CRLF vs LF)
- WSL defaults to LF; Windows defaults to CRLF.
- Git in WSL: set `core.autocrlf=input`.
- Scripts edited on Windows may fail in WSL due to `\r`. Use `dos2unix` if needed.
- Commit from WSL with LF to avoid mixed-ending commits.

## File permissions
- Files on `/mnt/` show `777` permissions by default.
- SSH keys on `/mnt/` trigger "permissions too open" errors. Keep in WSL-native `~/.ssh/`.
- Do not rely on Unix permission bits for files on Windows drives.

## Environment variable isolation
- Windows `PATH` leaks into WSL by default (via `WSLENV` / `interop`).
- Can cause unexpected `.exe` resolution or path-with-spaces errors.
- With `--noprofile --norc`, explicitly set needed variables.
- Suppress leakage: `export PATH=/usr/local/bin:/usr/bin:/bin` before command.
- Permanent fix: `[interop] appendWindowsPath=false` in `/etc/wsl.conf`.

## Mixed scenarios
- **Git + VS Code**: `code .` from WSL uses VS Code Remote - WSL.
- **Docker**: Prefer WSL-side `docker` CLI over `docker.exe`.
- **Node/Python**: Run dev servers in WSL; access via `localhost` from Windows browser.

## DNS resolution (WSL2)
- VPN/proxy may break DNS (`Temporary failure in name resolution`).
- Quick fix: `echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf`.
- Persistent: `/etc/wsl.conf` → `[network] generateResolvConf=false`, then set `/etc/resolv.conf` manually.
- Diagnose before assuming network is down.

## File system performance
- `/mnt/` is **10-100x slower** than WSL-native fs for I/O-heavy operations.
- Performance-sensitive projects → store in `~/projects/` inside WSL.
- Use `/mnt/` only for cross-OS file sharing.

## Git credential sharing
- Share Windows credentials: `git config --global credential.helper "/mnt/c/Program\ Files/Git/mingw64/bin/git-credential-manager.exe"`
- SSH keys: keep in WSL-native `~/.ssh/`, or use Windows SSH agent forwarding.

## WSL configuration files
- **`.wslconfig`** (`%USERPROFILE%\.wslconfig`): memory limit, swap, processors, networking mode.
- **`wsl.conf`** (`/etc/wsl.conf`): automount, network, interop, boot commands.
- Key: `[interop] appendWindowsPath=false` stops PATH leakage; `[network] generateResolvConf=false` for custom DNS.

## Networking (WSL2)
- Default: NAT mode with virtual network adapter.
- `localhost` forwarding works automatically for most cases.
- Mirrored mode: `[wsl2] networkingMode=mirrored` in `.wslconfig`.
- Port forwarding issues → check Windows firewall rules.
