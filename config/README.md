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
| [`labels.base.yml`](./labels.base.yml) | EndBug/label-sync | **reference** by raw URL (see below) |

## Issue labels (label-sync)

[`labels.base.yml`](./labels.base.yml) is the exception to the copy rule above:
`EndBug/label-sync` accepts multiple `config-file` entries, including URLs, so
repos adopt the base **by reference** and keep only their own additions locally.

```yaml
config-file: |
  https://raw.githubusercontent.com/resq-software/.github/<tag-or-sha>/config/labels.base.yml
  .github/labels.yml
```

Three constraints the action imposes, all load-bearing:

- **Configs are concatenated, not merged.** A name present in both files is
  emitted twice and applied by two concurrent, conflicting API calls — not
  "the last one wins". A repo's `labels.yml` must stay **disjoint** from the
  base; it holds additions only.
- **Pin the URL to a tag or commit SHA**, not a branch. A branch URL re-points
  every repo's labels the moment this file changes, with no review in between.
- **Never write a bare filename as a `config-file` entry.** The action decides
  local-vs-remote with a regex whose protocol group is optional
  (`^(https?:\/\/)?…`), so a bare `labels.base.yml` parses as the *domain*
  `labels.base` with TLD `yml` and is **fetched over the network** — which
  fails, or worse, silently resolves against something you do not control.

  | `config-file` entry | treated as |
  | --- | --- |
  | `labels.base.yml` | **remote URL** — wrong |
  | `labels-base.yml` | **remote URL** — wrong |
  | `.github/labels.yml` | local file — correct |
  | `./labels.base.yml` | local file — correct |
  | `config/labels.base.yml` | local file — correct |

  A `/` or a leading `.` anywhere before the first dot defeats the domain
  match, which is why the existing `.github/labels.yml` entries work. Keep the
  `.github/` prefix on every local entry and this never bites.

Keep `delete-other-labels: false`. Flipping it to `true` deletes every live
label a repo's config does not list, and a deleted label is removed from every
issue and PR that carried it.

### What the base contains, and why

46 labels in eight axes. A name is in the base when **automation depends on its
exact string in three or more repositories**, or when it is **already live in 12
or more** — a clear majority of the org — with a narrow allowance for a name
below 12 that completes an axis already carried. Namespaced names (`area:*`,
`pkg:*`, `crate:*`, `service:*`, `pipeline:*`, `lib:*`, `tool:*`) and aliases
for a concept the base already names once (`deps`, `chore`) stay in the repo's
own `labels.yml` whatever their count. The file's own header states the rule,
the per-axis evidence, and the four names that sit just under the three-repo
bar so the exclusion is arguable rather than silent.

#### Population — read this before quoting any number below

Every count here was measured on **2026-09-30, 14:30–14:39 UTC**, over every
repository this org owns, public and not, **minus the temporary forks the
security-advisory workflow creates**. Those forks hold no labels and no
`labels.yml`; a census that walks repository files over-counts them and one
that reads the org's declared private-repo counter under-counts them, which is
how the previous revision's figures drifted. Enumerate, exclude, then count —
the reproduction command is in the header of
[`labels.base.yml`](./labels.base.yml).

Compare hex colours **case-insensitively**. One label name is written `512BD4`
in one repo and `512bd4` in another; a case-sensitive compare reports that as
two colours and inflates the divergence count by one. Every colour figure here
is case-insensitive.

| measured over that population | 2026-09-30T14:30Z |
| --- | --- |
| label rows live across the org | 859 |
| distinct label names | 220 |
| names existing in exactly one repo | 132 |
| names live in 12+ repositories | 35 |
| names carrying more than one colour (hex compared case-insensitively) | 25 |

#### What adoption costs and changes

Adopting the base means **removing** the overlapping names from a repo's
`labels.yml`, or the concatenation bug above fires. **12** repos carry a
`labels.yml` today, and they declare **359** names that the base also declares.
Those 359 declarations have to go.

Effect on live labels if every repo adopts. Every one of the 46 names is
already live somewhere, so the base **introduces no new name** to the org — but
it is not a no-op: it creates **342 label rows** in repos that do not carry
those names yet.

| effect | count |
| --- | --- |
| existing row already identical — no change | 335 |
| existing row, description rewritten, colour kept | 123 |
| existing row **recoloured** | **120** |
| **rows created** (name already used elsewhere in the org) | **342** |
| **names new to the org** | **0** |

The 120 recolours are the intended effect. 15 of the 25 names that carry more
than one colour org-wide are in this file: all six of `size/*` (four colours
each), `A-DevOps` (five), and `javascript`, `github-actions`, `P1: high`,
`P3: low`, `refactor`, `security`, `ignore-for-release`, `skip-changelog`.
Same name, same meaning, different colour per repo is unmanaged drift, and
normalising it is name-safe — no automation reads a colour.

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
