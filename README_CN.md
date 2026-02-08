# wsl-terminal-skill

在 Windows 环境下优先使用 WSL 终端的 AI Agent Skill。

## 目录结构

```text
skills/
  wsl-terminal-priority/
    SKILL.md              # 核心 skill（含 YAML frontmatter）
    references/
      REFERENCE.md        # 详细跨边界参考
```

## 功能说明

- 在 Windows 上，所有终端命令优先通过 WSL 执行。
- 优先使用 Homebrew (Linuxbrew) 安装的工具，而非系统自带工具。
- WSL 不可用时，明确说明原因后回退到 PowerShell/CMD。
- 使用稳定命令模板（`--noprofile --norc`），避免环境污染。
- 工具找不到时按探测顺序查找：`command -v` → Linuxbrew 绝对路径 → 回退包管理器。

## 使用方式

将 `skills/wsl-terminal-priority/` 复制到你的 skill 加载器搜索路径下：
- OpenCode: `.opencode/skills/wsl-terminal-priority/SKILL.md`
- Claude Code: `.claude/skills/wsl-terminal-priority/SKILL.md`
- Cursor: `.cursor/skills/wsl-terminal-priority/SKILL.md`

## 许可证

[MIT](LICENSE)
