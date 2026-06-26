# CodeBuddy 记忆

通过 CODEBUDDY.md 文件实现持久化上下文 —— 如何编写以及它们在 monorepo 中如何加载。

<table width="100%">
<tr>
<td><a href="../">← 返回 CodeBuddy Code 最佳实践</a></td>
<td align="right"><img src="../!/codebuddy-jumping.svg" alt="CodeBuddy" width="60" /></td>
</tr>
</table>

---

## 1. 编写优秀的 CODEBUDDY.md

结构良好的 CODEBUDDY.md 是提升 CodeBuddy Code 输出质量最有效的方式。Humanlayer 有一篇优秀的指南，涵盖了包含什么内容、如何组织以及常见陷阱。

- [Humanlayer - 编写优秀的 CODEBUDDY.md](https://www.humanlayer.dev/blog/writing-a-good-codebuddy-md)

---

## 2. 大型 Monorepo 中的 CODEBUDDY.md

在 monorepo 中使用 CodeBuddy Code 时，理解 CODEBUDDY.md 文件如何加载到上下文中对于有效组织项目指令至关重要。

<p align="center">
  <a href="https://x.com/bcherny/status/2016339448863355206"><img src="assets/codebuddy-memory/codebuddy-memory-monorepo.jpg" alt="CODEBUDDY.md在monorepos中的加载" width="600"></a>
</p>

### 两种加载机制

CodeBuddy Code 使用两种不同的机制加载 CODEBUDDY.md 文件：

#### 祖先加载（向上遍历目录树）

启动 CodeBuddy Code 时，它会从当前工作目录**向上**遍历到文件系统根目录，加载沿途找到的所有 CODEBUDDY.md。这些文件在启动时**立即加载**。

#### 后代加载（向下遍历目录树）

当前工作目录的子目录中的 CODEBUDDY.md 文件**不会在启动时加载**。它们只在你会话期间读取这些子目录中的文件时才会被包含。这被称为**延迟加载**。

### 示例 Monorepo 结构

考虑一个典型的 monorepo，不同组件有独立目录：

```
/mymonorepo/
├── CODEBUDDY.md          # 根级指令（所有组件共享）
├── frontend/
│   └── CODEBUDDY.md      # 前端特定指令
├── backend/
│   └── CODEBUDDY.md      # 后端特定指令
└── api/
    └── CODEBUDDY.md      # API 特定指令
```

### 场景 1：从根目录运行 CodeBuddy Code

从 `/mymonorepo/` 运行 CodeBuddy Code 时：

```bash
cd /mymonorepo
codebuddy
```

| 文件 | 启动时加载？ | 原因 |
|------|-------------------|--------|
| `/mymonorepo/CODEBUDDY.md` | 是 | 当前工作目录 |
| `/mymonorepo/frontend/CODEBUDDY.md` | 否 | 仅在读取/编辑 `frontend/` 中的文件时加载 |
| `/mymonorepo/backend/CODEBUDDY.md` | 否 | 仅在读取/编辑 `backend/` 中的文件时加载 |
| `/mymonorepo/api/CODEBUDDY.md` | 否 | 仅在读取/编辑 `api/` 中的文件时加载 |

### 场景 2：从组件目录运行 CodeBuddy Code

从 `/mymonorepo/frontend/` 运行 CodeBuddy Code 时：

```bash
cd /mymonorepo/frontend
codebuddy
```

| 文件 | 启动时加载？ | 原因 |
|------|-------------------|--------|
| `/mymonorepo/CODEBUDDY.md` | 是 | 祖先目录 |
| `/mymonorepo/frontend/CODEBUDDY.md` | 是 | 当前工作目录 |
| `/mymonorepo/backend/CODEBUDDY.md` | 否 | 目录树的不同分支 |
| `/mymonorepo/api/CODEBUDDY.md` | 否 | 目录树的不同分支 |

### 关键要点

1. **祖先目录总是在启动时加载** —— CodeBuddy 向上遍历目录树并加载找到的所有 CODEBUDDY.md 文件。这确保你始终可以访问根级、仓库范围的指令。

2. **后代目录延迟加载** —— 子目录的 CODEBUDDY.md 文件只在你与这些子目录中的文件交互时才加载。这防止无关上下文膨胀你的会话。

3. **兄弟目录永不加载** —— 如果你在 `frontend/` 工作，`backend/CODEBUDDY.md` 或 `api/CODEBUDDY.md` 不会被加载到上下文中。

4. **全局 CODEBUDDY.md** —— 你也可以在主目录 `~/.codebuddy/CODEBUDDY.md` 放置 CODEBUDDY.md，它会应用于所有 CodeBuddy Code 会话，无论项目。

### 为什么这个设计适合 Monorepo

- **共享指令向下传播** —— 根级 CODEBUDDY.md 包含仓库范围的约定、编码标准和通用模式，适用于所有地方。

- **组件特定指令保持隔离** —— 前端开发人员不需要后端特定指令占用他们的上下文，反之亦然。

- **上下文优化** —— 通过延迟加载后代 CODEBUDDY.md 文件，CodeBuddy Code 避免在启动时加载可能数百 KB 的无关指令。

### 最佳实践

1. **将共享约定放在根 CODEBUDDY.md** —— 编码标准、提交消息格式、PR 模板和其他仓库范围指南。

2. **将组件特定指令放在组件 CODEBUDDY.md** —— 框架特定模式、组件架构、该组件独有的测试约定。

3. **使用 CODEBUDDY.local.md 存放个人偏好** —— 添加到 `.gitignore`，用于不应与团队共享的指令。

---

## 来源

- [CodeBuddy Code 文档 - CodeBuddy 如何查找记忆](https://www.codebuddy.cn/docs/cli/en/memory)
- [Boris Cherny 在 X 上 - CODEBUDDY.md 加载说明](https://x.com/bcherny/status/2016339448863355206)
- [Humanlayer - 编写优秀的 CODEBUDDY.md](https://www.humanlayer.dev/blog/writing-a-good-codebuddy-md)
