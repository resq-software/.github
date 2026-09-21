# Canonical configs

Reference configurations that back the [engineering standards](../docs/standards/).
These are the **single source of truth** for the org's formatter/linter/policy
settings — repos adopt them so "what does conformant look like" has one answer.

> Most of these formats don't support remote inheritance, so adoption is by
> **copy** (like [`README.template.md`](../README.template.md)) unless noted.
> Keep a repo's copy in sync when this directory changes; a repo with a
> deliberate deviation documents it in its `AGENTS.md`.

| File | Tool | Adopt by |
|------|------|----------|
| [`.editorconfig`](./.editorconfig) | editors / most formatters | copy to repo root |
| [`typescript/tsconfig.base.json`](./typescript/tsconfig.base.json) | `tsc` | `extends` (vendor or publish as `@resq-software/tsconfig`) |
| [Ruff config](#python-ruff) (below) | Ruff (lint + format) | copy into `ruff.toml` / `pyproject.toml` |
| [`rust/deny.toml`](./rust/deny.toml) | cargo-deny | copy to repo root |
| [`cpp/.clang-tidy`](./cpp/.clang-tidy) | clang-tidy | copy to repo root |
| [`yamllint.yml`](./yamllint.yml) | yamllint | copy as `.yamllint.yml` |
| [`dotnet/Directory.Build.props`](./dotnet/Directory.Build.props) | MSBuild / Roslyn analyzers | drop at repo root (auto-imported) |
| [`sql/.sqlfluff`](./sql/.sqlfluff) | SQLFluff | copy to repo root |
| [`.markdownlint.jsonc`](./.markdownlint.jsonc) | markdownlint | copy to repo root |

See [`docs/standards/02-languages.md`](../docs/standards/02-languages.md) for the
per-language rules these encode, and the [standards index](../docs/standards/)
for the three-tier model.

## Python (Ruff)

Shipped inline rather than as a file: a literal `ruff.toml` here trips the local
config-protection hook (which guards against weakening linters). Copy this into
your repo's `ruff.toml`, or under `[tool.ruff]` in `pyproject.toml`:

```toml
target-version = "py311"
line-length = 100

[lint]
# E/F = pycodestyle/pyflakes, I = isort, N = naming, UP = pyupgrade,
# B = bugbear, C4 = comprehensions, SIM = simplify, S = bandit-style security,
# PIE = misc lints, RUF = ruff-specific.
select = ["E", "F", "I", "N", "UP", "B", "C4", "SIM", "S", "PIE", "RUF"]
ignore = ["E501"]            # line length is the formatter's job

[lint.per-file-ignores]
"tests/**" = ["S101"]        # asserts are expected in tests

[format]
quote-style = "double"
indent-style = "space"
```

## C / C++ (clang-tidy)

[`cpp/.clang-tidy`](./cpp/.clang-tidy) backs the Tier 3
[safety overlay](../docs/standards/03-safety-overlay.md). clang-tidy discovers
the file by walking **up** from each translation unit, so it only takes effect
at the **repo root** — a copy left at `config/cpp/` is inert unless the
invocation passes `--config-file` explicitly.

Adopt warn-only first. `WarningsAsErrors` ships empty, so clang-tidy reports
findings and still exits 0; a repo that has never been analysed will have a
large first count. Flip `WarningsAsErrors` to `'*'` in that repo's own copy
once its baseline is clean — not before, and not org-wide at once.

Project headers **are** analysed: the config deliberately does not set
`HeaderFilterRegex`, so clang-tidy's own `.*` default applies and inline,
template and header-only code is checked like anything else. Setting it to
`''` would drop those diagnostics silently while CI still reported green. A
repo vendoring third-party headers should narrow it to a project-scoped
regex rather than emptying it.

Validate a copy with `clang-tidy --config-file=.clang-tidy --verify-config`.
Do **not** validate with `--list-checks`: it exits 0 and prints nothing even
when the config names a check or option key that does not exist.

### What this actually enforces

Power of Ten rules are from
[`03-safety-overlay.md`](../docs/standards/03-safety-overlay.md). Stated
honestly — this config is not full Power of Ten conformance, and four of the
ten rules have no mechanical equivalent at all.

| Rule | Enforced by | Coverage |
| ---- | ----------- | -------- |
| 1 — no unbounded recursion; simple control flow | `misc-no-recursion`, `cppcoreguidelines-avoid-goto`, `cert-err52-cpp` | partial — recursion and `setjmp`/`longjmp` are mechanical, but `avoid-goto` permits a forward `goto` that escapes nested loops, which `03-safety-overlay.md` bans outright |
| 4 — keep functions small | `readability-function-size` | **mechanical** |
| 3 — no dynamic allocation after init | `cppcoreguidelines-no-malloc`, `-owning-memory` | partial — flags allocation anywhere; the "after initialization" condition is not expressible |
| 7 — check every return value; check parameters | `bugprone-unused-return-value`, `cert-err33-c` | partial — a curated function list, not all non-void calls; the parameter-checking clause has no check |
| 8 — limit the preprocessor | `cppcoreguidelines-macro-usage`, `bugprone-macro-*` | partial |
| 10 — all warnings on, zero warnings | this config, once `WarningsAsErrors` is `'*'` | per-repo opt-in |
| **2** — statically provable loop bounds | — | **none — review only** |
| **5** — assert liberally | — | **none — review only** |
| **6** — smallest possible scope | — | **none — review only** |
| **9** — restrict pointer use, one dereference level | — | **none — review only** |

Two checks are easy to over-read. `cppcoreguidelines-pro-bounds-*` enforce
*bounds safety*, which is adjacent to rule 9 but is not dereference depth or a
function-pointer ban. `cppcoreguidelines-pro-type-cstyle-cast` fires on casts
between unrelated types and on downcasts — **not** on arithmetic casts like
`(int)d`; banning C-style casts outright needs `-Wold-style-cast`.

`cert-*` is enabled as a group for its CERT rule IDs, which appear alongside
the primary check name and give review a citable reference. Note it is not a
purely safety-motivated group — it also aliases style and performance checks.

## Adding a config

The set now covers every language in the standards (TS, Python, C#, Rust,
C/C++, Shell-via-`.editorconfig`, SQL, YAML, Markdown). When a tool needs a
canonical config, add the file here, document the rules it encodes in
[`docs/standards/02-languages.md`](../docs/standards/02-languages.md), and list
it in the table above.
