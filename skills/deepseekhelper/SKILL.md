---
name: deepseekhelper
description: Use when Codex should collaborate with DeepSeek for supervised low/medium difficulty code, documentation, or design tasks, or use DeepSeek to review, discuss, or verify higher difficulty work.
---

# DeepSeekHelper

DeepSeekHelper provides MCP tools for controlled DeepSeek collaboration.

Use it when the user asks for DeepSeek collaboration, DeepSeek verification, independent review, second-model checks, delegated drafts, or model-assisted discussion.

## Boundaries

- Codex remains responsible for reading files, editing files, running commands, testing, and final judgment.
- DeepSeek works only from the context Codex passes to the tool.
- For low or medium difficulty work, Codex may use `deepseek_task` to get plans, snippets, diffs, documentation drafts, or design drafts.
- For high difficulty work, Codex should use `deepseek_review`, `deepseek_discuss`, or `deepseek_verify`, then decide which feedback is technically valid.
- Do not pass secrets unless the user explicitly asks and the task requires it.

## Tools

- `deepseekhelper_status`: confirm configuration, automatic model policy, data paths, and available models.
- `deepseek_models`: fetch currently available DeepSeek API models and profile classification.
- `deepseek_auth_check`: verify the configured key works for both model listing and chat completions.
- `deepseek_usage_summary`: summarize calls, tokens, cache hit rate, and estimated cost.
- `deepseek_usage_tail`: inspect recent usage records without prompt or response content.
- `deepseek_task`: supervised low/medium task assistance.
- `deepseek_review`: independent review of code, docs, plans, prompts, or summaries.
- `deepseek_discuss`: trade-off discussion for higher difficulty decisions.
- `deepseek_verify`: claim, assumption, edge-case, and test-gap verification.
- `deepseek_prompt`: generate strict prompts for later delegation or review.

## Model Selection

Each DeepSeek call should disclose the model it used and why.

Selection order:

1. Explicit `model` argument on the tool call.
2. `DEEPSEEKHELPER_MODEL` or legacy `DEEPSEEK_MODEL` if configured and not `auto`.
3. Automatic policy, based on the daily cached DeepSeek `/models` list:
   - `deepseek_task` low/medium: latest flash-class model, thinking disabled by default
   - `deepseek_prompt`: latest flash-class model, thinking disabled by default
   - review, discussion, verification, or high-complexity support: latest pro-class model, thinking enabled with high effort by default

Use `deepseekhelper_status` or `deepseek_models` before important work if model availability matters.

The model list is cached for one day by default. Use the refresh arguments on status/model tools when a fresh lookup is required.

## Usage Tracking

The plugin records usage metadata in `data/usage.jsonl`: timestamp, tool, model, model tier, model selection source, thinking mode, token counts, cache hit/miss tokens, and estimated cost. It does not store full prompts or responses.

## Review Handling

After receiving DeepSeek feedback, evaluate each point against local evidence. Apply only valid feedback. Briefly explain any important suggestion that Codex rejects.
