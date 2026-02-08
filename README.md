# wsl-shell-reliability

[中文说明](README_CN.md)

Reliability-first shell selection skill for AI agents on Windows.

Core principle:

- Do **not** force WSL.
- Choose WSL or PowerShell based on failure risk and task semantics.

## Project layout

- `wsl-shell-reliability/SKILL.md`: normative policy used by the agent.
- `wsl-shell-reliability/references/REFERENCE.md`: deeper reliability notes.
- `wsl-shell-reliability/references/SCENARIOS.md`: shell-choice examples.

## Intended outcomes

- Fewer command failures from shell mismatch.
- Predictable fallback behavior.
- Better parity with task-native environments.

## What this skill is for

- Use when AI tools run terminal commands on Windows and reliability matters.
- Decide between WSL and PowerShell based on command semantics and failure risk.
- Enforce explicit fallback behavior instead of silent shell switching.
