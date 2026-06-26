# CODEBUDDY.md

本文件为 CodeBuddy Code 在本仓库中工作时提供指导。

## 仓库概述

这是 **CodeBuddy Code 最佳实践**仓库，由 [codebuddy-code-best-practice](https://github.com/shanraisshan/codebuddy-code-best-practice) 改造而来。它演示了 Agents、Skills、Hooks、Commands、Rules 的完整模式，作为 CodeBuddy Code 配置的参考实现。

仓库已从 CodeBuddy Code 的 `.codebuddy/` 改造为 CodeBuddy Code 的 `.codebuddy/` 目录结构。

## 项目结构

```
├── CODEBUDDY.md              # CodeBuddy Code 启动时加载的指导文件
├── .codebuddy/               # 项目级 CodeBuddy 配置
│   ├── settings.json         # 权限、hooks、环境变量等
│   ├── agents/               # 自定义子代理
│   ├── commands/             # 自定义斜杠命令
│   ├── hooks/                # 生命周期 hook 脚本
│   ├── rules/                # 项目规则文件
│   └── skills/               # 可复用技能
├── best-practice/            # 最佳实践文档（codebuddy-*.md）
├── implementation/           # 实现指南（codebuddy-*.md）
├── reports/                  # 分析报告（codebuddy-*.md）
├── tips/                     # Boris/Thariq 等人分享的技巧
├── tutorial/                 # 入门教程（day0-day2）
├── orchestration-workflow/   # 天气系统编排示例
├── docs/                     # CodeBuddy 官方文档（从 codebuddy.cn 抓取）
└── development-workflows/    # 开发工作流参考
```

## 关键组件

### 天气系统（示例工作流）
演示 **命令 → Agent → Skill** 架构：

- `/weather-orchestrator` (`.codebuddy/commands/weather-orchestrator.md`)：入口命令，询问温度单位，调用 agent
- `weather-agent` (`.codebuddy/agents/weather-agent.md`)：预加载 `weather-fetcher` skill
- `weather-fetcher` (`.codebuddy/skills/weather-fetcher/SKILL.md`)：从 Open-Meteo 获取温度
- `weather-svg-creator` (`.codebuddy/skills/weather-svg-creator/SKILL.md`)：创建 SVG 天气卡片

完整流程见 `orchestration-workflow/orchestration-workflow.md`。

### Agent 系统
`.codebuddy/agents/*.md` 中的 agent 定义使用 YAML frontmatter：

| 字段 | 说明 |
|------|------|
| `name` | Agent 标识符 |
| `description` | 何时调用（用 "PROACTIVELY" 表示自动触发） |
| `tools` | 允许的工具列表（逗号分隔） |
| `model` | 模型别名：`haiku`、`sonnet`、`opus` 或 `inherit` |
| `skills` | 预加载到 agent 上下文中的 skill 名称 |
| `hooks` | Agent 作用域内的生命周期 hooks |
| `permissionMode` | 权限模式（`acceptEdits`、`plan`、`bypassPermissions`） |
| `maxTurns` | 最大回合数 |

调用方式：`Agent(subagent_type="agent-name", description="...", prompt="...")`

### Hooks 系统
`.codebuddy/hooks/` 中的跨平台声音通知系统，通过 `settings.json` 配置 20+ hook 事件。

### Skills 系统
`.codebuddy/skills/<name>/SKILL.md` 定义可复用技能，支持自动发现和手动调用。

## 回答最佳实践问题

当用户询问 CodeBuddy Code 最佳实践时，**优先搜索本仓库**（`best-practice/`、`reports/`、`tips/`、`implementation/`），再参考外部文档或 `docs/` 目录。

## 工作流最佳实践

- CODEBUDDY.md 控制在 200 行以内，确保可靠遵从
- 用 commands 编排工作流，而非独立 agents
- 创建带 Skills 的专用 subagents（渐进式披露），而非通用 agents
- 上下文使用量达 50% 时执行 `/compact`
- 复杂任务先用 plan mode
- 多步骤任务使用任务列表 + 人工审核

## 调试技巧

- 用 `/doctor` 诊断
- 长命令作为后台任务运行
- 使用 MCP 浏览器工具（Playwright、Chrome DevTools）检查控制台
- 报告视觉问题时提供截图

## 关键文档

- `best-practice/codebuddy-subagents.md`：Subagent frontmatter 和 hooks
- `best-practice/codebuddy-commands.md`：斜杠命令模式
- `best-practice/codebuddy-skills.md`：Skills 系统详解
- `best-practice/codebuddy-memory.md`：记忆系统
- `best-practice/codebuddy-settings.md`：配置参考
- `best-practice/codebuddy-mcp.md`：MCP 集成
- `orchestration-workflow/orchestration-workflow.md`：编排流程图

## 文档标准

参见 `.codebuddy/rules/markdown-docs.md`。
