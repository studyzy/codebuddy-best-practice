# 计划：将项目改造为 CodeBuddy 最佳实践

## 目标

将当前面向 CodeBuddy Code 的最佳实践项目改造为 CodeBuddy Code 版本。核心差异：

| 项目 | CodeBuddy Code | CodeBuddy Code |
|------|-------------|----------------|
| 配置目录 | `.codebuddy/` | `.codebuddy/` |
| 记忆文件 | `CODEBUDDY.md` | `CODEBUDDY.md` |
| 环境变量前缀 | `CLAUDE_` | `CODEBUDDY_` |
| CLI 命令 | `claude` | `codebuddy` |
| 用户配置 | `~/.codebuddy/` | `~/.codebuddy/` |
| 项目设置文件 | `.codebuddy/settings.json` | `.codebuddy/settings.json` |
| 本地设置文件 | `.codebuddy/settings.local.json` | `.codebuddy/settings.local.json` |
| 代理目录 | `.codebuddy/agents/` | `.codebuddy/agents/` |
| 规则目录 | `.codebuddy/rules/` | `.codebuddy/rules/` |
| 命令目录 | `.codebuddy/commands/` | `.codebuddy/commands/` |
| 技能目录 | `.codebuddy/skills/` | `.codebuddy/skills/` |
| Hooks 目录 | `.codebuddy/hooks/` | `.codebuddy/hooks/` |
| 子代理工具 | `Task` / `Agent` 工具 | `Agent` 工具 |
| 官方文档 | `code.claude.com/docs` | `www.codebuddy.cn/docs/cli` |

## 改造范围

### 1. 目录结构迁移

- 保留 `.codebuddy/` 作为参考（原 CodeBuddy Code 配置）
- 创建 `.codebuddy/` 目录，复制并修改内容
- 两个目录共存，README 中说明

### 2. 文件改造清单

#### 2.1 根目录文件

| 文件 | 操作 | 说明 |
|------|------|------|
| `CODEBUDDY.md` | 修改 → `CODEBUDDY.md` | 将 CodeBuddy Code 引用改为 CodeBuddy Code |
| `README.md` | 修改 | 全局替换 CodeBuddy Code → CodeBuddy Code |
| `TRANSLATION.md` | 修改 | 更新翻译说明 |

#### 2.2 `.codebuddy/settings.json`

基于 `.codebuddy/settings.json` 创建，差异：
- 环境变量 `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` → `CODEBUDDY_AUTOCOMPACT_PCT_OVERRIDE`
- 环境变量 `CLAUDE_PROJECT_DIR` → `CODEBUDDY_PROJECT_DIR`
- CodeBuddy 的权限模型基本兼容
- 移除 Claude 专属的 `managed-settings.json` 相关引用
- CodeBuddy 有额外字段：`autoMemoryEnabled`、`typedMemory`、`language`、`autoMode` 等

#### 2.3 `.codebuddy/hooks/`

- 复制 hooks 系统
- 将 `CLAUDE_PROJECT_DIR` 环境变量改为 `CODEBUDDY_PROJECT_DIR`
- hooks 事件列表基本一致（PreToolUse, PostToolUse, SessionStart, SubagentStart 等）

#### 2.4 `.codebuddy/agents/`

- 复制 agents 定义
- 工具名保持一致（Agent 工具语法通用）
- frontmatter 字段兼容

#### 2.5 `.codebuddy/commands/`

- 复制命令定义
- 命令系统兼容

#### 2.6 `.codebuddy/skills/`

- 复制技能定义
- SKILL.md frontmatter 字段兼容
- Skill 工具调用语法一致

#### 2.7 `.codebuddy/rules/`

- 复制规则文件
- 规则系统兼容

#### 2.8 `best-practice/` 目录

| 文件 | 操作 |
|------|------|
| `claude-subagents.md` | 重命名 → `codebuddy-subagents.md` + 内容更新 |
| `claude-commands.md` | 重命名 → `codebuddy-commands.md` + 内容更新 |
| `claude-skills.md` | 重命名 → `codebuddy-skills.md` + 内容更新 |
| `claude-memory.md` | 重命名 → `codebuddy-memory.md` + 内容更新 |
| `claude-settings.md` | 重命名 → `codebuddy-settings.md` + 内容更新 |
| `claude-mcp.md` | 重命名 → `codebuddy-mcp.md` + 内容更新 |
| `claude-cli-startup-flags.md` | 重命名 → `codebuddy-cli-startup-flags.md` + 内容更新 |
| `claude-power-ups.md` | 重命名 → `codebuddy-power-ups.md` + 内容更新 |

#### 2.9 `implementation/` 目录

| 文件 | 操作 |
|------|------|
| `claude-*-implementation.md` | 重命名 → `codebuddy-*-implementation.md` + 内容更新 |

#### 2.10 `reports/` 目录

| 文件 | 操作 |
|------|------|
| `claude-*.md` | 重命名 → `codebuddy-*.md` + 内容更新 |

#### 2.11 `tips/` 目录

- Boris/Thariq 的原始技巧保持不变（是历史内容）
- 标题和说明中的 "CodeBuddy Code" 改为 "CodeBuddy Code"

#### 2.12 `tutorial/` 目录

- 安装命令 `claude` → `codebuddy`
- 环境变量前缀更新

#### 2.13 其他引用

- `orchestration-workflow/` 中的文档更新
- `development-workflows/` 中的引用更新
- `changelog/` 中的引用更新

## 实施步骤

1. 创建 `CODEBUDDY.md`（基于 CODEBUDDY.md 修改）
2. 创建 `.codebuddy/` 目录结构（settings.json, hooks/, agents/, commands/, skills/, rules/）
3. 批量重命名 `best-practice/codebuddy-*.md` → `best-practice/codebuddy-*.md`
4. 批量重命名 `implementation/codebuddy-*-implementation.md` → `implementation/codebuddy-*-implementation.md`
5. 批量重命名 `reports/codebuddy-*.md` → `reports/codebuddy-*.md`
6. 更新 README.md（全局替换 + 重写标题/说明）
7. 更新 TRANSLATION.md
8. 更新其他文档中的引用路径
9. 更新 `.codebuddy/settings.json` 中的环境变量前缀
10. 添加 CodeBuddy 特有功能的说明（docs/ 已有完整文档）

## 不改造的内容

- `!/` 目录（SVG/PNG 图标资源，与品牌无关，保留原样）
- `docs/` 目录（已是 CodeBuddy 文档，无需修改）
- `graphify-out/` 目录（知识图谱输出，重新运行即可）
- `.serena/` 目录（Serena 工具配置，独立于平台）
- `.kilo/` 目录（Kilo 计划文件，独立于平台）

## 验证

- 所有 `.codebuddy/` 路径正确
- 所有 `CODEBUDDY.md` / `CODEBUDDY_` 前缀正确
- README 中所有链接和引用更新
- 保留 `.codebuddy/` 作为原始参考
