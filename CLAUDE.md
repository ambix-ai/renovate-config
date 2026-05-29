@../claude-config/shared.md

# CLAUDE.md

**Org-wide Renovate preset** for ambix-ai repos. Pure config — no service to run, no cluster footprint. Repos opt in by extending it from their own `renovate.json`:

```json
{ "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>ambix-ai/renovate-config"] }
```

No expert agent owns this beyond `conventions.md`. Be conservative — every repo inherits these settings.

## Layout

- `default.json` — the shared preset
- `renovate.json` — config for this repo's own Renovate runs
- `README.md` — usage docs for consumer repos

## Editing

- Validate JSON locally before pushing.
- Test changes against a consumer repo with `local>ambix-ai/renovate-config#<branch>` in a draft PR.
- Document non-obvious choices inline in `default.json` with `// description` keys where Renovate allows.
