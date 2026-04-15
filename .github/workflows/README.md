# Reusable workflows

Org-wide CI building blocks. Callable from any `resq-software/*` repo via
`uses: resq-software/.github/.github/workflows/<name>.yml@main`.

## `security-scan.yml`

Defense-in-depth security scan. All third-party `uses:` refs are SHA-pinned
with a trailing `# <tag>` comment so Dependabot can still propose updates.

### Layers

| Job | Purpose | Default |
| :-- | :-- | :-- |
| `harden-runner` (first step in every job) | Audit/block runner egress — defense vs hijacked actions (tj-actions-class) | `audit` |
| `codeql` | SAST per language | opt-in via `languages` |
| `gitleaks` | Git history secret scan; requires `GITLEAKS_LICENSE` even on public org repos | off |
| `osv-scanner` | Dependency vuln scan (Google OSV) | on |
| `dependency-review` | PR-only; blocks high-severity CVEs | on |
| `zizmor` | Static audit of `.github/workflows/**` for security anti-patterns | on |
| `actionlint` | Workflow YAML correctness lint | on |
| `semgrep` | SAST; requires `SEMGREP_APP_TOKEN` secret | off |
| `snyk` | Dependency scan; requires `SNYK_TOKEN` secret | off |

### Caller example

```yaml
name: security
on:
  push: { branches: [main] }
  pull_request:
  schedule: [{ cron: "0 6 * * 1" }]

jobs:
  scan:
    uses: resq-software/.github/.github/workflows/security-scan.yml@main
    with:
      languages: '["rust","javascript-typescript"]'
      enable-semgrep: true
    secrets: inherit
```

### Inputs

| Input | Type | Default | Notes |
| :-- | :-- | :-- | :-- |
| `languages` | string (JSON array) | `"[]"` | CodeQL languages. Omit/empty to skip CodeQL. Valid: `actions`, `c-cpp`, `csharp`, `go`, `java-kotlin`, `javascript-typescript`, `python`, `ruby`, `swift`. |
| `enable-gitleaks` | bool | `false` | Requires `GITLEAKS_LICENSE` repo/org secret even on public repos owned by an org. GitHub's native secret scanning + push protection cover most use cases. |
| `enable-osv` | bool | `true` | |
| `enable-dependency-review` | bool | `true` | PR runs only. |
| `enable-zizmor` | bool | `true` | |
| `enable-actionlint` | bool | `true` | |
| `enable-semgrep` | bool | `false` | Requires `SEMGREP_APP_TOKEN` repo/org secret. |
| `enable-snyk` | bool | `false` | Requires `SNYK_TOKEN` repo/org secret. |

### Required secrets (opt-in jobs)

- `SEMGREP_APP_TOKEN` — from [semgrep.dev](https://semgrep.dev) → Settings → Tokens. Scope: CI.
- `SNYK_TOKEN` — from Snyk account settings.
- `GITLEAKS_LICENSE` — only needed for private-repo Gitleaks scans.

`secrets: inherit` in the caller forwards all org/repo secrets.

### Harden-Runner: audit → block migration

Every job starts with `step-security/harden-runner` in `egress-policy: audit`.
Audit mode **logs** outbound network calls without blocking them and publishes
an insights link in each run's summary.

After 2–4 weeks of observation:

1. Review the egress reports at `app.stepsecurity.io` for each repo.
2. Build a per-tool allowlist of destinations (e.g. `api.github.com`,
   `registry.npmjs.org`, `crates.io`).
3. Flip `egress-policy: block` with `allowed-endpoints:` populated. Do this
   per-job, not globally, since each tool has different outbound needs.

Do **not** skip audit mode — blocking without observation will break runs.

### zizmor findings

`zizmor` uploads SARIF to the **Security → Code scanning** tab. Common
findings and fixes:

- **template-injection** — Never interpolate `${{ github.event.* }}` into
  `run:` blocks. Pass via `env:` instead.
- **dangerous-triggers** — `pull_request_target` + checkout of PR head is
  dangerous. Use `pull_request` or a two-stage workflow.
- **unpinned-uses** — `@main`/`@master`/`@v4` tags can be moved. Pin to a
  commit SHA with a trailing `# <tag>` comment.
- **excessive-permissions** — Tighten the top-level `permissions:` block;
  grant write scopes only on the specific job that needs them.

### actionlint

Catches typos in `needs:`, invalid `uses:` refs, shell issues in `run:`
blocks (via shellcheck), and matrix misconfigurations. No SARIF; fails the
job on errors.

### Updating pinned SHAs

Dependabot opens PRs against tag-with-sha pins when the `# <tag>` comment is
present. To refresh manually:

```sh
gh api repos/<owner>/<repo>/commits/<tag> --jq .sha
```

Then update the `uses:` line and bump the `# <tag>` comment.
