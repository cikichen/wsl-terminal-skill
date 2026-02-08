# wsl-shell-reliability

[English](README.md)

面向 Windows 场景的 AI Agent Skill：
以 **WSL 为核心能力**，但以“执行成功率”为最高优先级。

核心原则：

- 不强制只用 WSL。
- 根据任务语义与失败风险，在 WSL / PowerShell 间做可靠性优先选择。

## 目录结构

```text
wsl-shell-reliability/
  SKILL.md
  references/
    REFERENCE.md
    SCENARIOS.md
```

## 功能说明

- 提供“何时用 WSL、何时用 PowerShell”的决策规则。
- 处理 bash 与 PowerShell 语法不兼容问题。
- 要求回退（fallback）必须显式说明，且保持命令语义一致。
- 覆盖常见风险：路径/引号、行尾差异、跨文件系统、环境泄漏等。

## 适用场景

- 适用于 AI 在 Windows 上执行终端命令且稳定性要求高的场景。
- 通过规则判断应使用 WSL 还是 PowerShell，而不是固定单一终端。
- 遇到失败时要求显式回退与说明，避免“静默切换”导致不可解释行为。

## 使用方式

将 `wsl-shell-reliability/` 放到你的 skill 搜索路径下：

- OpenCode: `.opencode/skills/wsl-shell-reliability/SKILL.md`
- Claude Code: `.claude/skills/wsl-shell-reliability/SKILL.md`
- Cursor: `.cursor/skills/wsl-shell-reliability/SKILL.md`

## 许可证

[MIT](LICENSE)
