# Tier 2 — Language Enforcement

Per-language tooling and idioms. A repo's `lang` custom property selects which
section applies; polyglot repos apply every relevant section. These extend
[Tier 1](./01-baseline.md) and override it where the language idiom differs.

---

## TypeScript

The most important move is **strictness**, not style.

- `tsconfig.json` extends a strict base:
  ```jsonc
  {
    "compilerOptions": {
      "strict": true,
      "noImplicitOverride": true,
      "noUncheckedIndexedAccess": true,
      "exactOptionalPropertyTypes": true,
      "noFallthroughCasesInSwitch": true
    }
  }
  ```
- No `any`. Use `unknown` at trust boundaries and narrow.
- Validate all external data (API responses, env, user input) with Zod / Valibot
  / TypeBox before it enters typed code.
- Lint with [typescript-eslint](https://typescript-eslint.io/) recommended +
  type-checked configs. Format with Prettier/Biome. Base style: Google TS guide.

## JavaScript (legacy / scripts / Node tooling)

- ESLint recommended; prefer migrating to TypeScript when a file grows logic.
- Google JS style guide as the base; prefer ESM.

## Python

- [PEP 8](https://peps.python.org/pep-0008/) + [PEP 257](https://peps.python.org/pep-0257/) baseline; Google Python style for structure.
- Tooling: **Ruff** (lint + format), **Pyright** or **mypy** (strict), **pytest**,
  **Bandit** (SAST), **pip-audit** (deps). Manage with **uv**.
- Type-hint public functions; `from __future__ import annotations` where helpful.

## C\#

- [Microsoft .NET coding conventions](https://learn.microsoft.com/dotnet/csharp/fundamentals/coding-style/coding-conventions); Google C# guide only when aligning a polyglot repo to Google style.
- `dotnet format` + `.editorconfig`; `<Nullable>enable</Nullable>`; treat warnings
  as errors where practical; Roslyn analyzers (`Microsoft.CodeAnalysis.NetAnalyzers`).
- Prefer `async`/`await` end-to-end; no `.Result`/`.Wait()` deadlock traps.

## Rust

Discipline is mostly compiler-enforced.

```text
cargo fmt --check
cargo clippy -- -D warnings
cargo test
cargo audit
cargo deny check
#![forbid(unsafe_code)]   // where the crate allows it
```

- Follow the [Rust API Guidelines](https://github.com/rust-lang/api-guidelines)
  for public surfaces.
- Treat every `unsafe` block as a mini C island: document the invariants,
  isolate it, review it, test it, fuzz it. See [Tier 3](./03-safety-overlay.md).

## C / C++

Tooling baseline; the analyzability rules live in [Tier 3](./03-safety-overlay.md).

- `clang-format` + `clang-tidy`; build clean at `-Wall -Wextra -Werror`.
- Sanitizers (ASan/UBSan/TSan) in test builds; static analysis in CI.
- C++: modern, RAII, smart pointers, STL; avoid raw `new`/`delete`, RTTI-heavy
  designs, and clever template metaprogramming without a systems reason.

## Shell

- [Google Shell Style Guide](https://google.github.io/styleguide/shellguide.html);
  **ShellCheck** clean. `set -euo pipefail` (bash) / `set -eu` (POSIX sh).
- Quote every expansion. Prefer `printf` over `echo` for data. Bound every loop.

## SQL

- **SQLFluff** for lint/format; consistent naming (snake_case tables/columns).
- Parameterized queries only — never string-concatenate user input.
- Migrations are forward-only and reviewed; every query that can grow is bounded
  (`LIMIT`, pagination).

## Markdown / JSON / YAML

- Prettier (md/json/yaml) + **yamllint**; Google Markdown guide for docs.
- Workflow YAML: **actionlint** + **zizmor** (already in `security-scan.yml`).

---

## Release & versioning

Conventional Commits drive versioning. Per-ecosystem tooling (release-it, PSR,
cargo-release/release-plz, MinVer) is documented in the
[README template's automation appendix](../../README.template.md).
