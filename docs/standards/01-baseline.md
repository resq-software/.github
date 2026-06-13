# Tier 1 — Repo-wide Baseline

Applies to **every** repository, regardless of language. If a repo can't meet a
baseline rule, that's a written deviation in its `AGENTS.md`, not a silent skip.

## Required toolchain

Every repo must have, wired into CI via the org `required` check:

- **Formatter** — runs in CI in check mode; no unformatted code on `main`.
- **Linter** — runs in CI; warnings are errors in CI (`-D warnings`-style).
- **Type checker** — where the language has one (TS, Python, C#); enabled in
  strict mode.
- **Tests** — for any non-trivial logic. New behavior ships with a test.
- **Security scan** — the org `security-scan.yml` (CodeQL/OSV/Dependency Review/
  zizmor/actionlint, opt-in Gitleaks/Semgrep/Snyk).

## Hard rules

```text
No secrets in code.            Use env vars or a secret manager; validate at startup.
No unchecked external input.   Validate at every trust boundary; fail closed.
No unbounded retries.          Every retry loop has a max attempt count + backoff.
No missing timeouts.           Every network / IO call has an explicit timeout.
No silent catch/except.        Handle, wrap, or propagate — never swallow.
No TODOs in production paths.  Unless linked to a tracked issue.
No hand-edited generated files. Regenerate; edit the source/template.
No mutation where avoidable.   Prefer immutable data; localize necessary mutation.
```

## Code shape

These are defaults; languages may override in [Tier 2](./02-languages.md) where
idioms differ (e.g. Go pointer receivers).

- **Functions** focused — aim < 50 lines; extract when responsibilities stack.
- **Files** cohesive — 200–400 lines typical, 800 max; many small files over few
  large ones.
- **Nesting** shallow — prefer early returns over > 4 levels of nesting.
- **Names** descriptive — `camelCase`/`snake_case` per language; booleans read as
  `is`/`has`/`should`/`can`; constants `UPPER_SNAKE_CASE`.
- **No magic numbers** — name meaningful thresholds, delays, limits.

## Errors & logging

- Handle errors explicitly at every layer; user-facing messages stay friendly,
  server-side logs carry the detail.
- Structured logs (key/value or JSON), not `print`-debugging left in.
- Never leak secrets, tokens, or PII into logs or error messages.

## Repository hygiene

Enforced by [`repo-standards.yml`](../../.github/workflows/repo-standards.yml):

- A detectable `LICENSE` (Apache-2.0 unless a repo declares otherwise).
- A real `README.md` — start from [`README.template.md`](../../README.template.md);
  no leftover `{{PLACEHOLDER}}` tokens.
- Org community-health files (`CONTRIBUTING`, `SECURITY`, `CODE_OF_CONDUCT`,
  `SUPPORT`) are inherited org-wide from this repo — don't duplicate unless
  overriding.
- Conventional Commits + the branch-name pattern (see [`CONTRIBUTING.md`](../../CONTRIBUTING.md)).

## Definition of done

- [ ] Formatter, linter, type-checker, tests, security scan green (`required` ✓)
- [ ] No secrets, no swallowed errors, no unbounded loops/timeouts
- [ ] New behavior covered by tests
- [ ] Public API / behavior change reflected in docs and CHANGELOG
- [ ] Any standards deviation documented in `AGENTS.md`
