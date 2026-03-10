# CLAUDE.md — Dean Abraham Personal Projects

## Branching Strategy

All active work happens on the `development` branch. Never commit directly to `testing` or `main`.

```
development  →  (PR)  →  testing  →  (PR)  →  main
```

| Branch | Purpose | Direct Pushes |
|---|---|---|
| `development` | All active work | ✅ Allowed |
| `testing` | QA / validation | Blocked — PR only |
| `main` | Production-ready | Blocked — PR only |

- Always work on `development`
- Promote via Pull Request: `development → testing → main`
- Keep `development` in sync with `testing` after merges

## Git

- Do not force-push or use destructive git commands without explicit instruction
- Do not commit unless explicitly asked
- Do not push unless explicitly asked
- Use descriptive commit messages
