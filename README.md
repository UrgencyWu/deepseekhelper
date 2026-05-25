# DeepSeekHelper

DeepSeekHelper is a Codex plugin that exposes DeepSeek-powered MCP tools for supervised collaboration.

Codex can use DeepSeekHelper to delegate low or medium difficulty coding, documentation, and design work to DeepSeek. For higher difficulty work, Codex can use DeepSeek for review, discussion, and verification while keeping final file edits, tests, and judgment inside Codex.

## Features

- Supervised low and medium difficulty task assistance.
- Independent review for code, documentation, plans, prompts, and implementation summaries.
- Design and architecture discussion with trade-offs.
- Verification of claims, assumptions, edge cases, and test gaps.
- Automatic Flash/Pro model selection from the DeepSeek `/models` list.
- Token, cache hit/miss, and estimated cost tracking.

## Requirements

- Codex with local plugin support.
- Node.js 18 or newer.
- A DeepSeek API key.

## Installation

Clone the repository and install dependencies:

```bash
git clone https://github.com/YOUR_USER_OR_ORG/deepseekhelper.git
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

## Configuration

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

## Tools

- `deepseekhelper_status`: Show configuration, model policy, data paths, and available models.
- `deepseek_models`: Fetch available DeepSeek models and show profile classification.
- `deepseek_auth_check`: Verify the key can call both `/models` and chat completions.
- `deepseek_task`: Ask DeepSeek to handle low or medium difficulty work under Codex supervision.
- `deepseek_review`: Ask DeepSeek for independent review.
- `deepseek_discuss`: Ask DeepSeek to compare options and risks.
- `deepseek_verify`: Ask DeepSeek to verify claims, assumptions, edge cases, and test gaps.
- `deepseek_prompt`: Ask DeepSeek to create a strict delegation or review prompt.
- `deepseek_usage_summary`: Summarize calls, tokens, cache hit rate, and estimated cost.
- `deepseek_usage_tail`: Show recent usage records without prompt or response content.

## Model Selection

Selection order:

1. Explicit `model` argument on a tool call.
2. `DEEPSEEKHELPER_MODEL` or legacy `DEEPSEEK_MODEL`, if configured and not `auto`.
3. Automatic policy using the daily cached DeepSeek `/models` list.

Automatic policy:

- `deepseek_task` low/medium: latest Flash-class model, thinking disabled by default.
- `deepseek_prompt`: latest Flash-class model, thinking disabled by default.
- `deepseek_review`, `deepseek_discuss`, `deepseek_verify`: latest Pro-class model, thinking enabled with high effort by default.

The model list is cached for one day by default in `data/models-cache.json`. Use the refresh arguments on `deepseekhelper_status` or `deepseek_models` to force a fresh lookup.

## Usage Tracking

Each DeepSeek API call appends metadata to:

```text
data/usage.jsonl
```

The log records timestamps, tool name, model, model tier, thinking mode, token counts, cache hit/miss tokens, and estimated cost. It does not record prompts or responses.

`data/` is ignored by git and should remain local.

## Development Checks

Run:

```bash
node --check scripts/server.mjs
npm audit --omit=dev
```

If you have the Codex plugin creator validation script available, run:

```bash
python3 /path/to/plugin-creator/scripts/validate_plugin.py .
```

## Security

Never commit API keys, `.env`, `data/`, `node_modules/`, usage logs, or model cache files. See `SECURITY.md`.

## Contributing

See `CONTRIBUTING.md` and `CODE_OF_CONDUCT.md`.

## License

MIT
