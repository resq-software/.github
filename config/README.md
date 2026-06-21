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

## Not yet templated

C# (`.editorconfig` analyzer rules), SQL (`.sqlfluff`), and Markdown
(`.markdownlint.jsonc`) are documented in the standards but not yet shipped as
canonical files here — add them when the first repo of that kind needs one.
