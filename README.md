# Agent Loop Streaming Skill

这是一个用于设计和实现 **智能体 loop 流式输出体验** 的 skill。它帮助产品经理、独立开发者、vibe coder 和 coding agent，把“用户应该看到智能体正在工作”这类模糊需求，转成可以落地的前端体验、事件契约、UI 状态、结构化 artifact 渲染规则和验收标准。

适用场景包括：

- 你不想让用户只看到一个 spinner，希望展示 agent 正在理解、检索、调用工具、生成结果或等待确认。
- 你的 agent 会调用工具、查资料、生成报告卡片、表单、计划、草稿或工单。
- 你的前端现在把工具 JSON、中间文本和最终回答混在一起，用户看不懂 agent 到底在做什么。
- 你想把产品想法整理成 Codex、Claude Code、Cursor 等代码助手可以直接实现的需求。

## 目录结构

```text
agent-loop-streaming/
├── SKILL.md
├── README.md
├── evals/
│   └── evals.json
└── references/
    ├── debugging-and-tests.md
    ├── event-contract.md
    └── frontend-work-panel.md
```

各文件作用：

- `SKILL.md`：skill 的触发说明、核心心智模型、使用方式、输出格式和体验原则。
- `references/frontend-work-panel.md`：前端体验模式，包括 chat transcript、work panel、artifact area、action area 的信息层级。
- `references/event-contract.md`：应用侧事件模型、事件到 UI 的映射、按 `tool_call_id` 合并 work step 的建议。
- `references/debugging-and-tests.md`：给 vibe coder 和 coding agent 的实现 brief 模板、调试建议和验收清单。
- `evals/evals.json`：用于检查这个 skill 是否按预期工作的示例评测 prompt。

## 快速安装思路

最适合非专业程序员的方式不是自己手动移动文件，而是：

1. 先把这个仓库 clone 到电脑里。
2. 打开你正在使用的 Codex、Claude Code 或 Cursor。
3. 把下面对应工具的安装 prompt 发给它。
4. 让它帮你检查目录、移动文件、确认是否安装成功。

先 clone 仓库：

```bash
git clone https://github.com/hensonzh/agent-loop-streaming.git
```

如果你不知道 clone 到了哪里，可以直接把这句话发给你的 AI 助手：

```text
我刚刚 clone 了 https://github.com/hensonzh/agent-loop-streaming.git，但我不知道它在哪个目录。请帮我在常见下载目录和当前项目附近查找 `agent-loop-streaming` 文件夹，然后按你的工具要求帮我安装。
```

## 添加到 Codex

Codex 的本地 skill 通常放在：

```text
~/.codex/skills/
```

最终目录应长这样：

```text
~/.codex/skills/agent-loop-streaming/SKILL.md
~/.codex/skills/agent-loop-streaming/references/frontend-work-panel.md
~/.codex/skills/agent-loop-streaming/references/event-contract.md
~/.codex/skills/agent-loop-streaming/references/debugging-and-tests.md
~/.codex/skills/agent-loop-streaming/evals/evals.json
```

### 推荐方式：让 Codex 帮你安装

如果你已经在 Codex 里，可以把下面这段话直接发给 Codex：

```text
请帮我安装 agent-loop-streaming skill。

我已经 clone 了这个仓库：
https://github.com/hensonzh/agent-loop-streaming.git

请你做这些事：
1. 在当前项目、`~/Downloads`、`~/Desktop`、`~/project`、`~/projects` 等常见位置查找 `agent-loop-streaming` 文件夹。
2. 确认里面有 `SKILL.md`、`references/` 和 `evals/`。
3. 创建 `~/.codex/skills/` 目录。
4. 把整个 `agent-loop-streaming` 文件夹复制到 `~/.codex/skills/agent-loop-streaming`。
5. 如果目标目录已经存在，先告诉我，再询问是否覆盖。
6. 安装后列出最终目录结构，确认 `~/.codex/skills/agent-loop-streaming/SKILL.md` 存在。
```

安装完成后，重启 Codex，或开一个新的 Codex 会话。

### 手动方式

如果你熟悉命令行，也可以直接执行：

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/hensonzh/agent-loop-streaming.git ~/.codex/skills/agent-loop-streaming
```

如果你已经把仓库下载到了 `~/Downloads/agent-loop-streaming`：

```bash
mkdir -p ~/.codex/skills
cp -R ~/Downloads/agent-loop-streaming ~/.codex/skills/agent-loop-streaming
```

### 在 Codex 里如何触发

你可以直接在对话里提到这个 skill 的主题，例如：

```text
请用 agent-loop-streaming skill，帮我设计一个 AI CRM assistant 的流式工作过程。
```

或描述具体问题：

```text
我的 agent 会先输出一句话，然后调用工具，最后继续回答。前端现在把第一句话当最终答案了，请帮我设计正确的 loop streaming UI。
```

如果 Codex 已正确加载，它会在相关任务里读取 `SKILL.md` 和 `references/` 里的补充资料。

### Codex 常见问题

- **看起来没有加载 skill**：确认路径是不是 `~/.codex/skills/agent-loop-streaming/SKILL.md`，不要多套一层目录。
- **复制后仍然没有生效**：重启 Codex 或新开一个会话。
- **只复制了 `SKILL.md`**：可以运行，但效果会变弱；建议把 `references/` 和 `evals/` 一起复制。
- **路径里的 `~` 是什么**：`~` 表示你的用户主目录，例如 macOS 上通常是 `/Users/你的用户名`。

## 添加到 Claude Code

Claude Code 的个人 skill 通常放在：

```text
~/.claude/skills/
```

最终目录应长这样：

```text
~/.claude/skills/agent-loop-streaming/SKILL.md
~/.claude/skills/agent-loop-streaming/references/frontend-work-panel.md
~/.claude/skills/agent-loop-streaming/references/event-contract.md
~/.claude/skills/agent-loop-streaming/references/debugging-and-tests.md
~/.claude/skills/agent-loop-streaming/evals/evals.json
```

### 推荐方式：让 Claude Code 帮你安装

如果你已经在 Claude Code 里，可以把下面这段话直接发给 Claude Code：

```text
请帮我安装 agent-loop-streaming skill。

我已经 clone 了这个仓库：
https://github.com/hensonzh/agent-loop-streaming.git

请你做这些事：
1. 在当前项目、`~/Downloads`、`~/Desktop`、`~/project`、`~/projects` 等常见位置查找 `agent-loop-streaming` 文件夹。
2. 确认里面有 `SKILL.md`、`references/` 和 `evals/`。
3. 创建 `~/.claude/skills/` 目录。
4. 把整个 `agent-loop-streaming` 文件夹复制到 `~/.claude/skills/agent-loop-streaming`。
5. 如果目标目录已经存在，先告诉我，再询问是否覆盖。
6. 安装后列出最终目录结构，确认 `~/.claude/skills/agent-loop-streaming/SKILL.md` 存在。
```

安装完成后，重启 Claude Code，或退出当前会话后重新进入项目。

### 手动方式

如果你熟悉命令行，也可以直接执行：

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/hensonzh/agent-loop-streaming.git ~/.claude/skills/agent-loop-streaming
```

如果你已经下载到 `~/Downloads/agent-loop-streaming`：

```bash
mkdir -p ~/.claude/skills
cp -R ~/Downloads/agent-loop-streaming ~/.claude/skills/agent-loop-streaming
```

### 项目级安装

如果你只想让某一个项目使用这个 skill，可以让 Claude Code 放在项目目录下：

```text
your-project/
└── .claude/
    └── skills/
        └── agent-loop-streaming/
            ├── SKILL.md
            ├── references/
            └── evals/
```

可以把下面这段话发给 Claude Code：

```text
请把 agent-loop-streaming 安装成当前项目专用的 Claude Code skill。

请你找到我 clone 的 `agent-loop-streaming` 文件夹，然后复制到当前项目的 `.claude/skills/agent-loop-streaming/`。
如果 `.claude/skills/agent-loop-streaming/` 已经存在，请先问我是否覆盖。
安装后请确认 `.claude/skills/agent-loop-streaming/SKILL.md` 存在。
```

### 在 Claude Code 里如何触发

你可以直接说：

```text
使用 agent-loop-streaming skill，帮我把这个 AI 售后 agent 的前端流式体验设计成可实现的 brief。
```

也可以描述问题，让 Claude Code 自动判断是否相关：

```text
我们现在把 tool call 的 JSON 全部显示在聊天里，最终答案和过程混在一起。请帮我改成更好的 agent loop UI。
```

### Claude Code 常见问题

- **skill 没出现或没被使用**：确认文件名必须是大写的 `SKILL.md`。
- **目录名重要吗**：目录名建议使用 `agent-loop-streaming`，Claude Code 会把目录作为 skill 的可识别名称之一。
- **只想当前项目可用**：放到项目的 `.claude/skills/agent-loop-streaming/`。
- **想所有项目可用**：放到 `~/.claude/skills/agent-loop-streaming/`。
- **安装后不生效**：重启 Claude Code 或新开会话。

## 添加到 Cursor

Cursor 目前更常用的是 **Rules** 机制，而不是直接读取 `SKILL.md` 作为 Claude/Codex 风格的 skill。因此建议把这个 skill 转成 Cursor 项目规则。

Cursor 的项目规则通常放在：

```text
your-project/.cursor/rules/
```

建议创建：

```text
your-project/.cursor/rules/agent-loop-streaming.mdc
```

### 推荐方式：让 Cursor 帮你安装

如果你已经在 Cursor 里打开了自己的项目，可以把下面这段话发给 Cursor Chat：

```text
请帮我把 agent-loop-streaming skill 安装成当前项目的 Cursor Rule。

我已经 clone 了这个仓库：
https://github.com/hensonzh/agent-loop-streaming.git

请你做这些事：
1. 在当前项目、`~/Downloads`、`~/Desktop`、`~/project`、`~/projects` 等常见位置查找 `agent-loop-streaming` 文件夹。
2. 确认里面有 `SKILL.md`、`references/` 和 `evals/`。
3. 在当前项目创建 `.cursor/rules/` 目录。
4. 基于 `agent-loop-streaming/SKILL.md` 创建 `.cursor/rules/agent-loop-streaming.mdc`。
5. 在这个 `.mdc` 文件顶部加入 Cursor rule frontmatter：
   `description: 设计、实现或调试 agent loop streaming 前端体验时使用，包括 work panel、应用侧事件、artifact 渲染、确认动作和验收标准。`
   `alwaysApply: false`
6. 把整个 `agent-loop-streaming` 文件夹复制到当前项目的 `docs/skills/agent-loop-streaming/`，让 references 也能被 Cursor 读取。
7. 在 `.cursor/rules/agent-loop-streaming.mdc` 里补一句：需要更多细节时，读取 `docs/skills/agent-loop-streaming/references/` 下的参考文档。
8. 安装后列出 `.cursor/rules/agent-loop-streaming.mdc` 和 `docs/skills/agent-loop-streaming/SKILL.md`，确认它们存在。
```

### 手动方式

如果你熟悉命令行，可以进入你的项目目录后执行：

```bash
mkdir -p .cursor/rules
cp ~/Downloads/agent-loop-streaming/SKILL.md .cursor/rules/agent-loop-streaming.mdc
```

然后打开 `.cursor/rules/agent-loop-streaming.mdc`，在文件顶部补上 Cursor rule 的说明：

```md
---
description: 设计、实现或调试 agent loop streaming 前端体验时使用，包括 work panel、应用侧事件、artifact 渲染、确认动作和验收标准。
alwaysApply: false
---
```

接着把原 `SKILL.md` 的正文保留在下面。

如果你希望 Cursor 能读到完整参考资料，可以把整个 skill 放到项目文档目录：

```bash
mkdir -p docs/skills
cp -R ~/Downloads/agent-loop-streaming docs/skills/agent-loop-streaming
```

然后在 `.cursor/rules/agent-loop-streaming.mdc` 里加一句：

```md
需要更多细节时，读取 `docs/skills/agent-loop-streaming/references/` 下的参考文档。
```

这样 Cursor 在处理相关任务时，可以通过项目文件读取 `references/frontend-work-panel.md`、`references/event-contract.md` 和 `references/debugging-and-tests.md`。

### 在 Cursor 里如何触发

你可以在聊天里明确引用规则：

```text
请按 agent-loop-streaming 规则，帮我把当前 AI assistant 的 loading UI 改造成 work panel + artifact area。
```

也可以在任务里描述目标：

```text
这个页面现在只有 spinner。agent 会查客户记录、生成邮件草稿、确认后发送。请实现一个前端流式过程 UI。
```

如果规则没有自动进入上下文，可以手动把 `.cursor/rules/agent-loop-streaming.mdc` 或 `docs/skills/agent-loop-streaming/SKILL.md` 加到聊天上下文里。

### Cursor 常见问题

- **Cursor 没有自动读取 `SKILL.md`**：这是正常的。Cursor 主要使用 `.cursor/rules/*.mdc`。
- **规则没有生效**：确认文件在项目根目录的 `.cursor/rules/` 下，并且扩展名是 `.mdc`。
- **不知道项目根目录在哪里**：通常是你在 Cursor 里打开的那个文件夹，里面可能有 `package.json`、`.git`、`src/` 等文件。
- **想让规则一直生效**：可以把 `alwaysApply` 改成 `true`，但不建议一开始就这样做；这个 skill 只适合 agent loop streaming 相关任务。
- **references 没被读到**：把整个 `agent-loop-streaming` 文件夹放进项目，比如 `docs/skills/agent-loop-streaming/`，然后在 prompt 里明确让 Cursor 读取该目录。

## 安全建议

安装任何第三方 skill、rule 或提示词包之前，都建议先打开 `SKILL.md` 看一遍。它会影响 coding agent 如何理解任务、读取文件、提出修改建议，甚至可能影响它是否建议执行某些命令。

这个 skill 不包含可执行脚本，主要是 Markdown 指令和参考文档；但你仍然应该在安装前确认内容符合你的团队习惯和安全边界。

## 适用范围

这个 skill 不绑定任何具体前端框架，也不要求你使用某一家模型服务商。它关注的是更稳定的产品和实现契约：

- 用户应该看到哪些阶段。
- 中间文本和最终回答如何分离。
- 工具调用如何映射成用户能理解的 work step。
- artifact 应该在哪里渲染。
- 有副作用的动作如何 preview 和 confirmation。
- 前端如何通过应用侧事件更新状态，而不是直接展示 provider 原始 event。

它可以用于 React、Vue、Svelte、Next.js、原生 Web、移动端 WebView，以及任何需要展示 agent loop 过程的产品界面。
