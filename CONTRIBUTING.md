# Contributing

Thanks for improving DeepSeekHelper.

## Development

Install dependencies:

```bash
npm install
```

Run local checks:

```bash
node --check scripts/server.mjs
python3 /path/to/plugin-creator/scripts/validate_plugin.py .
```

## Pull Requests

- Keep changes focused.
- Do not commit secrets, `.env`, `data/`, or `node_modules/`.
- Update `README.md` and `skills/deepseekhelper/SKILL.md` when behavior changes.
- Include validation output or a short explanation if a check cannot be run.

## Conduct

Participation is governed by `CODE_OF_CONDUCT.md`.
