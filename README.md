# DeepSeekHelper

[English](#english) | [中文](#中文)

A [Codex](https://github.com/anthropics/claude-code) plugin that exposes DeepSeek-powered MCP tools for supervised collaboration. Codex delegates, reviews, discusses, and verifies work with DeepSeek while keeping final edits, tests, and judgment inside Codex.

---

一个 Codex 插件，通过 MCP 工具让 Codex 与 DeepSeek 进行有监督的协作。Codex 可以将中低难度的工作委托给 DeepSeek，也可以使用 DeepSeek 进行审查、讨论和验证，同时将最终编辑、测试和判断保留在 Codex 内。

---

[![Star History Chart](https://api.star-history.com/svg?repos=UrgencyWu/deepseekhelper&type=Date)](https://star-history.com/#UrgencyWu/deepseekhelper&Date)

---

## English

### About

DeepSeekHelper adds ten local MCP tools to Codex, each wrapping the DeepSeek API with structured supervision boundaries:

- **Delegation** — `deepseek_task` sends scoped, low/medium difficulty work to DeepSeek for drafts, plans, snippets, and documentation.
- **Review** — `deepseek_review` gets an independent second pair of eyes on code, docs, plans, prompts, or implementation summaries.
- **Discussion** — `deepseek_discuss` compares trade-offs, risks, and alternatives for higher-difficulty decisions.
- **Verification** — `deepseek_verify` checks claims, assumptions, edge cases, and test gaps against the evidence Codex provides.
- **Prompt generation** — `deepseek_prompt` creates strict delegation or review prompts so Codex can hand off work with clear constraints.
- **Model management** — `deepseekhelper_status`, `deepseek_models`, and `deepseek_auth_check` surface available models, automatic Flash/Pro classification, and API connectivity.
- **Usage tracking** — `deepseek_usage_summary` and `deepseek_usage_tail` report token counts, cache hit rates, and estimated costs without logging prompt or response content.

The plugin automatically selects the right DeepSeek model for the job: Flash-class for drafting and prompt generation, Pro-class with thinking for review, discussion, and verification. Every call records anonymous usage metadata locally so you can audit cost and cache efficiency over time.

### Features

- Supervised low and medium difficulty task assistance.
- Independent review for code, documentation, plans, prompts, and implementation summaries.
- Design and architecture discussion with trade-offs.
- Verification of claims, assumptions, edge cases, and test gaps.
- Automatic Flash/Pro model selection from the DeepSeek `/models` list.
- Token, cache hit/miss, and estimated cost tracking.

### Requirements

- Codex with local plugin support.
- Node.js 18 or newer.
- A DeepSeek API key.

### Installation

Clone the repository and install dependencies:

```bash
git clone https://github.com/UrgencyWu/deepseekhelper.git
cd deepseekhelper
npm install
```

Install or register the plugin in Codex using the plugin directory. The plugin includes:

```text
.codex-plugin/plugin.json
.mcp.json
skills/deepseekhelper/SKILL.md
scripts/server.mjs
```

The included `.mcp.json` starts the MCP server with:

```json
{
  "mcpServers": {
    "deepseekhelper": {
      "command": "node",
      "args": ["scripts/server.mjs"]
    }
  }
}
```

If your Codex plugin host does not start MCP servers from the plugin root, replace `scripts/server.mjs` with the absolute path to your local `scripts/server.mjs`.

### Configuration

Set your DeepSeek API key before starting Codex:

```bash
export DEEPSEEK_API_KEY="your-deepseek-api-key"
```

On macOS, Codex launched as a desktop app may not inherit shell variables. You can set the key through `launchctl` and restart Codex:

```bash
launchctl setenv DEEPSEEK_API_KEY "your-deepseek-api-key"
```

DeepSeekHelper falls back to `launchctl getenv DEEPSEEK_API_KEY` on macOS if the variable is not present in the direct process environment.

Optional settings:

```bash
export DEEPSEEKHELPER_MODEL=auto
export DEEPSEEKHELPER_MODEL_CACHE_DAYS=1
export DEEPSEEK_BASE_URL=https://api.deepseek.com
```

Do not commit `.env` files or real API keys. See `.env.example` for a template.

### Tools

| Tool | Purpose |
|---|---|
| `deepseekhelper_status` | Show configuration, model policy, data paths, and available models. |
| `deepseek_models` | Fetch available DeepSeek models and show profile classification. |
| `deepseek_auth_check` | Verify the key can call both `/models` and chat completions. |
| `deepseek_task` | Ask DeepSeek to handle low or medium difficulty work under Codex supervision. |
| `deepseek_review` | Ask DeepSeek for independent review. |
| `deepseek_discuss` | Ask DeepSeek to compare options and risks. |
| `deepseek_verify` | Ask DeepSeek to verify claims, assumptions, edge cases, and test gaps. |
| `deepseek_prompt` | Ask DeepSeek to create a strict delegation or review prompt. |
| `deepseek_usage_summary` | Summarize calls, tokens, cache hit rate, and estimated cost. |
| `deepseek_usage_tail` | Show recent usage records without prompt or response content. |

### Model Selection

Selection order:

1. Explicit `model` argument on a tool call.
2. `DEEPSEEKHELPER_MODEL` or legacy `DEEPSEEK_MODEL`, if configured and not `auto`.
3. Automatic policy using the daily cached DeepSeek `/models` list.

Automatic policy:

- `deepseek_task` low/medium: latest Flash-class model, thinking disabled by default.
- `deepseek_prompt`: latest Flash-class model, thinking disabled by default.
- `deepseek_review`, `deepseek_discuss`, `deepseek_verify`: latest Pro-class model, thinking enabled with high effort by default.

The model list is cached for one day by default in `data/models-cache.json`. Use the refresh arguments on `deepseekhelper_status` or `deepseek_models` to force a fresh lookup.

### Usage Tracking

Each DeepSeek API call appends metadata to:

```text
data/usage.jsonl
```

The log records timestamps, tool name, model, model tier, thinking mode, token counts, cache hit/miss tokens, and estimated cost. It does not record prompts or responses.

`data/` is ignored by git and should remain local.

### Development Checks

Run:

```bash
node --check scripts/server.mjs
npm audit --omit=dev
```

If you have the Codex plugin creator validation script available, run:

```bash
python3 /path/to/plugin-creator/scripts/validate_plugin.py .
```

### Security

Never commit API keys, `.env`, `data/`, `node_modules/`, usage logs, or model cache files. See `SECURITY.md`.

### Contributing

See `CONTRIBUTING.md` and `CODE_OF_CONDUCT.md`.

### License

MIT

---

## 中文

### 关于

DeepSeekHelper 为 Codex 提供了十个本地 MCP 工具，每个工具封装了 DeepSeek API，并带有结构化的监督边界：

- **委托** — `deepseek_task` 将限定范围的中低难度工作发送给 DeepSeek，获取草稿、计划、代码片段和文档。
- **审查** — `deepseek_review` 让 DeepSeek 作为独立的第二双眼睛，审查代码、文档、计划、提示词或实现总结。
- **讨论** — `deepseek_discuss` 对比权衡、风险和替代方案，辅助高难度决策。
- **验证** — `deepseek_verify` 根据 Codex 提供的证据，检查声明、假设、边界情况和测试盲区。
- **提示词生成** — `deepseek_prompt` 创建严格的委托或审查提示词，让 Codex 以清晰的约束交付工作。
- **模型管理** — `deepseekhelper_status`、`deepseek_models` 和 `deepseek_auth_check` 展示可用模型、自动 Flash/Pro 分类以及 API 连通性。
- **用量跟踪** — `deepseek_usage_summary` 和 `deepseek_usage_tail` 报告 token 数量、缓存命中率和预估费用，不记录提示词或响应内容。

插件会自动为任务选择合适的 DeepSeek 模型：草稿和提示词生成使用 Flash 级模型，审查、讨论和验证使用带思考模式的 Pro 级模型。每次调用都会在本地匿名记录用量元数据，方便随时审计成本和缓存效率。

### 功能

- 有监督的中低难度任务辅助。
- 对代码、文档、计划、提示词和实现总结的独立审查。
- 带权衡分析的设计和架构讨论。
- 对声明、假设、边界情况和测试盲区的验证。
- 基于 DeepSeek `/models` 列表的自动 Flash/Pro 模型选择。
- Token、缓存命中/未命中和预估费用跟踪。

### 环境要求

- 支持本地插件的 Codex。
- Node.js 18 或更高版本。
- DeepSeek API 密钥。

### 安装

克隆仓库并安装依赖：

```bash
git clone https://github.com/UrgencyWu/deepseekhelper.git
cd deepseekhelper
npm install
```

在 Codex 中通过插件目录安装或注册插件。插件包含：

```text
.codex-plugin/plugin.json
.mcp.json
skills/deepseekhelper/SKILL.md
scripts/server.mjs
```

插件内置的 `.mcp.json` 启动 MCP 服务器：

```json
{
  "mcpServers": {
    "deepseekhelper": {
      "command": "node",
      "args": ["scripts/server.mjs"]
    }
  }
}
```

如果你的 Codex 插件宿主不从插件根目录启动 MCP 服务器，请将 `scripts/server.mjs` 替换为本地的绝对路径。

### 配置

启动 Codex 前设置 DeepSeek API 密钥：

```bash
export DEEPSEEK_API_KEY="your-deepseek-api-key"
```

在 macOS 上，以桌面应用方式启动的 Codex 可能无法继承 Shell 环境变量。可以通过 `launchctl` 设置密钥并重启 Codex：

```bash
launchctl setenv DEEPSEEK_API_KEY "your-deepseek-api-key"
```

在 macOS 上，如果直接进程环境中没有该变量，DeepSeekHelper 会回退到 `launchctl getenv DEEPSEEK_API_KEY`。

可选设置：

```bash
export DEEPSEEKHELPER_MODEL=auto
export DEEPSEEKHELPER_MODEL_CACHE_DAYS=1
export DEEPSEEK_BASE_URL=https://api.deepseek.com
```

不要提交 `.env` 文件或真实 API 密钥。参考 `.env.example` 模板。

### 工具

| 工具 | 用途 |
|---|---|
| `deepseekhelper_status` | 显示配置、模型策略、数据路径和可用模型。 |
| `deepseek_models` | 获取可用 DeepSeek 模型及分类。 |
| `deepseek_auth_check` | 验证密钥是否能调用 `/models` 和聊天补全接口。 |
| `deepseek_task` | 委托中低难度工作给 DeepSeek，由 Codex 监督。 |
| `deepseek_review` | 请 DeepSeek 进行独立审查。 |
| `deepseek_discuss` | 请 DeepSeek 对比选项和风险。 |
| `deepseek_verify` | 请 DeepSeek 验证声明、假设、边界情况和测试盲区。 |
| `deepseek_prompt` | 请 DeepSeek 创建严格的委托或审查提示词。 |
| `deepseek_usage_summary` | 汇总调用次数、token、缓存命中率和预估费用。 |
| `deepseek_usage_tail` | 查看最近的用量记录（不含提示词和响应内容）。 |

### 模型选择

选择优先级：

1. 工具调用时显式传入的 `model` 参数。
2. `DEEPSEEKHELPER_MODEL` 或旧版 `DEEPSEEK_MODEL`（如已配置且不为 `auto`）。
3. 基于每日缓存的 DeepSeek `/models` 列表的自动策略。

自动策略：

- `deepseek_task` 中低难度：最新 Flash 级模型，默认关闭思考模式。
- `deepseek_prompt`：最新 Flash 级模型，默认关闭思考模式。
- `deepseek_review`、`deepseek_discuss`、`deepseek_verify`：最新 Pro 级模型，默认开启高力度思考模式。

模型列表默认缓存一天，存储在 `data/models-cache.json`。使用 `deepseekhelper_status` 或 `deepseek_models` 的刷新参数可强制更新。

### 用量跟踪

每次 DeepSeek API 调用的元数据追加到：

```text
data/usage.jsonl
```

日志记录时间戳、工具名、模型、模型层级、思考模式、token 数量、缓存命中/未命中 token 以及预估费用。不记录提示词或响应内容。

`data/` 已被 git 忽略，应保留在本地。

### 开发检查

运行：

```bash
node --check scripts/server.mjs
npm audit --omit=dev
```

如果你有 Codex 插件创建器验证脚本，运行：

```bash
python3 /path/to/plugin-creator/scripts/validate_plugin.py .
```

### 安全

切勿提交 API 密钥、`.env`、`data/`、`node_modules/`、用量日志或模型缓存文件。详见 `SECURITY.md`。

### 参与贡献

详见 `CONTRIBUTING.md` 和 `CODE_OF_CONDUCT.md`。

### 许可证

MIT
