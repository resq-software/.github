# Contributing to ResQ

Thanks for your interest. This guide applies to every repository under [`resq-software`](https://github.com/resq-software). Per-repo specifics (commands, architecture, standards) live in each repo's `AGENTS.md`.

## Onboarding

One curl gets you from a bare machine to a working ResQ dev loop:

```bash
curl -fsSL https://get.resq.software | sh
```

This installs the SHA256-verified `resq` CLI (with a `cargo install --git` fallback), optionally clones a repo, and — when run inside a repo — installs the canonical git hooks: six hooks (pre-commit, commit-msg, prepare-commit-msg, pre-push, post-checkout, post-merge) that delegate to `resq pre-commit`, plus an offer to scaffold a repo-type-aware `local-pre-push` (Rust / Python / Node / .NET / C++ / Nix).

Full contract: [`resq-software/dev/AGENTS.md#git-hooks`](https://github.com/resq-software/dev/blob/main/AGENTS.md#git-hooks).

## Commit messages

Conventional Commits, with support for the `!` breaking-change marker:

```
feat(scope): short subject                   # new feature
fix(scope): short subject                    # bug fix
feat!: remove deprecated API                 # breaking change
fix(api)!: rename /v1/foo → /v2/foo          # breaking + scoped

docs(readme): …       style(…): …      refactor(…): …
perf(…): …            test(…): …       build(…): …
ci(…): …              chore(…): …      revert(…): …
```

`WIP:` / `fixup!` / `squash!` are blocked on `main` / `master`. The `commit-msg` hook enforces this automatically after installation.

If your branch name matches `[A-Z]{2,}-[0-9]+` (e.g. `feat/RESQ-123-thing`), the ticket prefix is auto-prepended to the commit message.

## Branch names

```
feat/<topic>      fix/<topic>      docs/<topic>
chore/<topic>     refactor/<topic> test/<topic>
ci/<topic>        perf/<topic>     release/<version>
```

The `pre-push` hook rejects anything else on non-default branches. `changeset-release/*` (the release-plz bot branches) are allowed.

## Running checks locally

```bash
resq pre-commit           # full pipeline: copyright, secrets, audit, format
resq format               # only the polyglot formatter
resq format --check       # exit 1 on any issue — useful in CI
resq hooks doctor         # report drift between installed and canonical hooks
resq hooks update         # rewrite installed hooks from embedded templates
```

## Engineering standards

Org-wide code standards live in [`docs/standards/`](./docs/standards/) — a
three-tier model:

- [**Tier 1 — Baseline**](./docs/standards/01-baseline.md): required toolchain,
  hard rules, code shape (every repo).
- [**Tier 2 — Language enforcement**](./docs/standards/02-languages.md): per-language
  tooling and idioms (TS, Python, C#, Rust, C/C++, Shell, SQL, …).
- [**Tier 3 — Safety overlay**](./docs/standards/03-safety-overlay.md): JSF / MISRA /
  NASA Power of Ten for device- and flight-adjacent code.
- [**Security overlay**](./docs/standards/04-security.md): untrusted input, secrets,
  auth, crypto.

Per-repo specifics (commands, architecture, deliberate deviations) still live in
each repo's `AGENTS.md`.

## Opening a PR

1. Branch from the default branch; name it per the pattern above.
2. Make atomic commits following Conventional Commits.
3. Push; the pre-push hook runs `cargo check` / `ruff check` / `dotnet build` / etc. per the repo's `.git-hooks/local-pre-push`.
4. Open the PR; fill out every section of the PR template.
5. CI runs the org-wide `security-scan` workflow (CodeQL, Gitleaks, OSV-Scanner, Dependency Review) plus any repo-specific jobs. All bot reviewers (CodeRabbit, Gemini, GitHub Advanced Security) post asynchronously.
6. Address feedback; maintainers approve and merge.

## Security issues

Do **not** open a public issue for a suspected vulnerability — see [SECURITY.md](./SECURITY.md) for the private reporting process.

## License

Contributions are accepted under each repository's declared license (Apache-2.0 unless noted otherwise). By opening a PR you confirm you have the right to license your contribution under those terms.
