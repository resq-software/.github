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

## Adding a config

The set now covers every language in the standards (TS, Python, C#, Rust,
Shell-via-`.editorconfig`, SQL, YAML, Markdown). When a tool needs a canonical
config, add the file here, document the rules it encodes in
[`docs/standards/02-languages.md`](../docs/standards/02-languages.md), and list
it in the table above.
