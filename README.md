# renovate-config

Org-wide [Renovate](https://docs.renovatebot.com/) preset for **ambix-ai** repos.
One source of truth for dependency-update policy across Go services, Docker images,
Kubernetes manifests, GitHub Actions, and pre-commit hooks.

## Usage

Add a `renovate.json` (or `.github/renovate.json`) to the repo you want managed:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>ambix-ai/renovate-config"]
}
```

That's the whole config — everything else lives here. Onboard the repo in the
[Renovate GitHub App](https://github.com/apps/renovate) (org settings) and it picks
the preset up automatically.

## What the preset does

Built on Renovate's [`config:best-practices`](https://docs.renovatebot.com/presets-config/#configbest-practices),
which already pins Docker digests, pins GitHub Action digests, enables the dependency
dashboard, and uses semantic-commit prefixes. On top of that:

| Setting | Value | Why |
|---|---|---|
| `baseBranches` | `main` | single trunk |
| `minimumReleaseAge` | `3 days` | let a release settle before we adopt it |
| `rebaseWhen` | `conflicted` | only rebase when needed — less CI churn |
| `labels` | `dependencies` | uniform triage label |
| `automergeStrategy` | `squash` | one tidy commit per update |
| `postUpdateOptions` | `gomodTidy`, `gomodUpdateImportPaths` | keep `go.mod`/`go.sum` clean on Go bumps |
| `pre-commit` manager | enabled | Renovate keeps `.pre-commit-config.yaml` current |
| `kubernetes` manager | matches `k8s/**/*.ya?ml` | manifests get image/chart updates |

### Automerge policy

- **Auto-merged** (minor / patch / digest / pinDigest): Dockerfile, Kubernetes,
  GitHub Actions, pre-commit. Low risk, high noise — let the bot handle them.
- **Manual review**: every **major** bump, and all major GitHub Actions bumps
  explicitly never automerge.
- **Go modules**: non-major updates grouped into one PR (`go modules (non-major)`).

## Overriding per repo

`extends` is just the base — add fields after it to override locally:

```json
{
  "extends": ["github>ambix-ai/renovate-config"],
  "minimumReleaseAge": "7 days",
  "packageRules": [
    { "matchManagers": ["gomod"], "matchUpdateTypes": ["major"], "dependencyDashboardApproval": true }
  ]
}
```

## Validating changes here

```bash
npx --yes --package renovate -- renovate-config-validator default.json renovate.json
```

The repo dogfoods its own preset via `renovate.json` (`local>ambix-ai/renovate-config`).
