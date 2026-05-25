# Changelog

## 0.3.2 — 2026-05-25

- First public release.
- Ten MCP tools: `deepseekhelper_status`, `deepseek_models`, `deepseek_auth_check`, `deepseek_task`, `deepseek_review`, `deepseek_discuss`, `deepseek_verify`, `deepseek_prompt`, `deepseek_usage_summary`, `deepseek_usage_tail`.
- Automatic Flash/Pro model selection from the cached DeepSeek `/models` list.
- Configurable model override via `DEEPSEEKHELPER_MODEL` and legacy `DEEPSEEK_MODEL`.
- Thinking mode control: disabled by default for Flash-class tasks, enabled with high reasoning effort for Pro-class review/discussion/verification.
- Token, cache hit/miss, and estimated cost tracking in `data/usage.jsonl`.
- Local model cache with configurable TTL (`DEEPSEEKHELPER_MODEL_CACHE_DAYS`).
- macOS `launchctl` fallback for `DEEPSEEK_API_KEY`.
- `sanitizeErrorMessage` redacts API keys and bearer tokens from error output.
