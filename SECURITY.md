# Security Policy

ResQ takes the safety of its users and contributors seriously. This policy applies to every repository under [`resq-software`](https://github.com/resq-software).

## Supported versions

For libraries and binaries cut via `release-plz`, only the latest minor release line receives patches. If you're running an older line, upgrade before reporting — the issue may already be resolved.

| Product | Supported |
|---|---|
| `resq-cli` (via `crates`) | latest `0.x.y` |
| `resq-dsa` (library) | latest `0.x.y` |
| `@resq-sw/*` npm packages | latest `0.x.y` |
| Other packages | latest published version |

## Reporting a vulnerability

**Do not open a public issue for a suspected vulnerability.** Public disclosure before a fix is available puts users at risk.

Use one of the private channels below:

1. **GitHub Security Advisories** (preferred): open a draft advisory on the affected repo at `https://github.com/resq-software/<repo>/security/advisories/new`. If you're unsure which repo, file against [`resq-software/.github`](https://github.com/resq-software/.github/security/advisories/new) and we'll route internally.
2. **Email**: `security@resq.software`.

Please include:

- Affected repo + commit SHA or release tag
- A short description of the issue and its impact
- Steps to reproduce (a minimal proof-of-concept is ideal)
- Any suggested mitigation if you have one

## Our response

| Window | What happens |
|---|---|
| 48 hours | Initial acknowledgement from a maintainer |
| 7 days | Triage decision: confirmed / needs-info / not-applicable |
| 30 days | Fix landed on `main` for confirmed issues; coordinated disclosure window opens |
| 90 days | Public disclosure (GitHub Security Advisory published + CVE requested where applicable), unless we've agreed to a longer embargo |

We credit reporters in advisory text unless they ask to remain anonymous.

## What's in scope

- Code, configuration, and infrastructure manifests in any public `resq-software` repository.
- The `resq` CLI binary distributed via `resq-software/crates` GitHub Releases.
- The canonical git hooks shipped via `resq-software/dev`.

## What's out of scope

- Dependencies maintained by third parties — report upstream; we'll consume the fix when it lands. (That said, if an advisory affects us materially, we want to know.)
- Social engineering, physical access, denial-of-service via traffic volume.
- Self-XSS, missing security headers on non-sensitive marketing pages.

## Automated scanning

Every repo runs the reusable [`security-scan`](.github/workflows/security-scan.yml) workflow on push + PR + weekly schedule. What actually runs is narrower than the job list, so it is written out rather than summarised:

| Scan | With no caller overrides |
| ---- | ------------------------ |
| OSV-Scanner, zizmor, actionlint | on, every repo, every trigger |
| Dependency Review | on, but **pull requests only** — never on push or the weekly schedule |
| CodeQL | **off** unless the caller passes `languages` **and** the repo has CodeQL *default setup* disabled — with default setup on, GitHub rejects the advanced configuration, so passing `languages` alone is not enough; also needs GitHub Code Security on a private repo |
| Gitleaks, Semgrep, Snyk, vet | **off** unless the caller opts in (Semgrep and Snyk also need a token) |

Three further limits worth knowing before relying on a green tick:

- **CodeQL findings cannot fail a build, but CodeQL can.** Only the `Analyze`
  step carries `continue-on-error`, so a finding is reported and not enforced
  — yet `Initialize CodeQL` and `Autobuild` do not, and a failure in either
  fails the job and the `required` gate. Autobuild failure is the common case
  for the compiled languages the input advertises (`c-cpp`, `csharp`, `go`,
  `java-kotlin`, `swift`).
- **zizmor findings cannot fail a build either.** Both its scan step and its
  SARIF upload are `continue-on-error`, so the row above marking it "on, every
  repo" means it always *runs*, not that it can ever block a merge.
- **Dependency Review fails rather than skips.** On a private repo without
  GitHub Code Security the job runs and *fails* on every pull request, until
  the caller passes `enable-dependency-review: false`.

Findings are triaged by the maintainers listed in each repo's `CODEOWNERS`.
