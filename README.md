# wsl-terminal-skill

[中文说明](README_CN.md)

WSL-first terminal execution skill for Windows environments.

## Structure

```text
skills/
  SKILL.md
```

## What this skill does

- On Windows, terminal commands should run in WSL first.
- If WSL is unavailable, fallback to PowerShell/CMD with explicit explanation.
- Keep command behavior consistent when switching shells.

## Usage

Place this repository (or `skills/SKILL.md`) where your skill loader expects skills.

## License

[MIT](LICENSE)
