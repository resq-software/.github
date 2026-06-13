# ResQ Engineering Standards

The org-wide engineering constitution for [`resq-software`](https://github.com/resq-software).
These standards apply to **every** repository unless a repo's own `AGENTS.md`
documents a deliberate, written deviation.

> **Philosophy.** Style guides make code *consistent*. Secure-coding standards
> prevent *bug classes*. Safety-critical standards make behavior *analyzable*.
> We apply each at the altitude it earns: boring-and-typed everywhere,
> aerospace-grade only where undefined behavior, memory safety, timing, or
> hardware control actually matter.

## The three tiers

Standards are layered. Every repo gets Tier 1. Tier 2 is selected by the repo's
`lang` custom property. Tier 3 is reserved for code that touches physical
devices, telemetry, auth, crypto, or runs unattended.

| Tier | Scope | Document |
|------|-------|----------|
| **1 — Baseline** | Every repo, every language | [`01-baseline.md`](./01-baseline.md) |
| **2 — Language enforcement** | Per-language tooling & idioms | [`02-languages.md`](./02-languages.md) |
| **3 — Safety/critical overlay** | C/C++/Rust, device- & flight-adjacent | [`03-safety-overlay.md`](./03-safety-overlay.md) |
| **Security overlay** | Anything handling untrusted input, secrets, or auth | [`04-security.md`](./04-security.md) |

## The standard stack

| Area | Standard |
|------|----------|
| General code quality | [Google Style Guides](https://google.github.io/styleguide/) |
| TypeScript / JS | `strict` mode + [typescript-eslint](https://typescript-eslint.io/) |
| Python | [PEP 8](https://peps.python.org/pep-0008/) + Ruff + Pyright |
| C# | [Microsoft .NET conventions](https://learn.microsoft.com/dotnet/csharp/fundamentals/coding-style/coding-conventions) + analyzers |
| Rust | rustfmt + Clippy + [Rust API Guidelines](https://github.com/rust-lang/api-guidelines) |
| C / C++ | [JSF AV C++](https://www.stroustrup.com/JSF-AV-rules.pdf) + [MISRA](https://www.misra.org.uk/) + CERT + [Power of Ten](https://spinroot.com/gerard/pdf/P10.pdf) |
| Security | [OWASP ASVS / Cheat Sheets](https://owasp.org/), CERT, Semgrep |
| Critical-systems mindset | NASA/JPL Power of Ten, NPR 7150.2D-style traceability |
| Documentation | Google Markdown + ADRs + runbooks + the [README template](../../README.template.md) |
| CI enforcement | the org-wide `required` status check ([`.github/workflows/required.yml`](../../.github/workflows/required.yml)) |

## Rules of thumb

- **Normal apps:** Google style + formatter + linter + tests.
- **Backend / platform:** add type-checking, static analysis, dependency
  scanning, structured errors, observability.
- **Edge / embedded / security-sensitive:** add the Tier 3 overlay — bounded
  execution, no cleverness, explicit ownership, deterministic failure,
  documented deviations.

## How this is enforced

- **Mechanically, today:** formatter + linter + type-checker + security scan run
  in CI via the reusable [`required.yml`](../../.github/workflows/required.yml)
  workflow; [`repo-standards.yml`](../../.github/workflows/repo-standards.yml)
  checks LICENSE/README/template conformance. Both feed the single `required`
  status check gated by the org ruleset `default-branch-baseline`.
- **By review:** PR reviewers (human + CodeRabbit/Gemini/GHAS) check Tier 2/3
  adherence that tooling can't express.
- **By convention:** anything not yet machine-checkable lives here as a written
  rule. Cite the rule in review; file an issue to automate it.

## Deviations

Deviating is allowed when there's a clear systems-level reason. Record it:
document the rule waived, why, and the compensating control in the repo's
`AGENTS.md` (or an ADR under `docs/adr/`). An undocumented deviation is a bug.
