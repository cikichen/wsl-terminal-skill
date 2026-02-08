# wsl-terminal-priority

Reliability-first shell selection skill for AI agents on Windows.

Core principle:

- Do **not** force WSL.
- Choose WSL or PowerShell based on failure risk and task semantics.

## Project layout

- `SKILL.md`: normative policy used by the agent.
- `references/REFERENCE.md`: deeper reliability notes and fallback contract.
- `references/SCENARIOS.md`: practical examples and shell-choice patterns.

## Intended outcomes

- Fewer command failures from shell mismatch.
- Predictable fallback behavior.
- Better parity with task-native environments.
