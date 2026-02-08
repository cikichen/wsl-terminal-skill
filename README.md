# wsl-terminal-skill

[中文说明](README_CN.md)

WSL-first terminal execution skill for Windows environments.

## Structure

```text
skills/
  wsl-terminal-priority/
    SKILL.md              # Core skill (with YAML frontmatter)
    references/
      REFERENCE.md        # Detailed cross-boundary reference
```

## What this skill does

- On Windows, terminal commands should run in WSL first.
- Windows-only tools (winget, scoop, PowerShell cmdlets, etc.) run natively.
- Homebrew/Linuxbrew preferred over apt for CLI tools.
- If WSL is unavailable, fallback to PowerShell/CMD with explicit explanation.

## Usage

Copy `skills/wsl-terminal-priority/` into your skill loader's search path:
- OpenCode: `.opencode/skills/wsl-terminal-priority/SKILL.md`
- Claude Code: `.claude/skills/wsl-terminal-priority/SKILL.md`
- Cursor: `.cursor/skills/wsl-terminal-priority/SKILL.md`

## License

[MIT](LICENSE)
