# Versioning & Release

The org's single source of truth for how versions are decided, tagged and
published. Pairs with [`05-workflow-releases.md`](./05-workflow-releases.md),
which covers how changes to the shared workflows themselves propagate.

## Why this is a contract and not a tool

Five languages, several monorepos, four release tools already in production.
No single tool covers Rust, TypeScript, Python, C# and C++ without someone
being made worse off — and a tool a repo resents is a tool a repo quietly
stops using. So the central artifact is this document plus the checks that
enforce it. Per-ecosystem tooling stays per-ecosystem.

What was measured across the 11 public repos before writing this:

| Dimension | Distinct variants found |
| --------- | ----------------------- |
| Tag grammar | **5**, across 367 tags |
| Version source of truth | **7** incompatible mechanisms |
| Release tooling | **4** live, plus 1 orphaned config nothing runs |
| Release trigger | **5** models |
| Changelog strategy | **3** |

The org already pays for that spread: `docs/scripts/build_changelog.py`
carries a per-repo dispatch table of four regexes purely to parse its own
org's tags.

## Tag grammar

| Repo shape | Format | Example |
| ---------- | ------ | ------- |
| Single artifact | `v<semver>` | `v1.4.0` |
| Multiple artifacts | `<component>-v<semver>` | `resq-cli-v0.4.2` |
| npm workspaces | `@scope/<pkg>@<semver>` | `@resq-systems/ui@0.42.0` |

The multi-artifact form is the one already proven at scale here — 185 tags
across 18 components with no ambiguity. The npm form is a **documented
exception**: changesets mandates it, and changesets is kept for a reason
given below. One scope only — `@resq-systems`. A second, abandoned scope is
still present in 25 published tags and must not be extended.

A repo that publishes nothing still tags, using the single-artifact form, so
that "what is deployed" has an answer.

## Version source of truth

**The tag is the release. The in-tree version must equal the latest tag for
that component.** Where the number lives is the ecosystem's business —
`Cargo.toml`, `package.json`, `pyproject.toml`, `Directory.Build.props`,
`vcpkg.json`, a `VERSION` file — but it must agree with the tag.

This is the rule with teeth, because the failure it prevents is already
present: one repo's in-tree version reads three releases behind its highest
tag, and `GET /releases/latest` on two repos returns a version *older* than
what shipped, so both landing pages advertise a regression.

A repo that publishes to a registry has a release workflow. A release config
that no workflow invokes is dead config and is a finding, not decoration.

## Conventional Commits

Every candidate tool except changesets derives severity from commit
messages, which makes commit hygiene the load-bearing input rather than a
style preference. Conventional Commits are required on the default branch —
see [`CONTRIBUTING.md`](../../CONTRIBUTING.md) for the format.

Measured conformance ranges from 99% to 66% across the org. The two lowest
repos are also repos with no release automation; that is not a coincidence,
and automating them without fixing commit hygiene first will produce wrong
version bumps rather than no bumps.

## Semantics

Same definitions as `05-workflow-releases.md`, applied to code rather than
workflow inputs:

- **MAJOR** — a consumer must change something to upgrade.
- **MINOR** — new capability, existing usage unaffected.
- **PATCH** — a fix that changes no interface.

Pre-1.0 (`0.y.z`) the same rules apply one position right: `0.y` behaves as
major. Say so in the README rather than leaving a reader to guess whether
`0.4.2 → 0.5.0` will break them.

## Tooling by ecosystem

Kept deliberately, each for a stated reason. None of these is a default.

| Ecosystem | Tool | Why this one |
| --------- | ---- | ------------ |
| Rust | release-plz + git-cliff | Only option offering `cargo-semver-checks` API-break detection |
| TypeScript | changesets | Records author *intent* at PR time instead of inferring severity from commits — worth more than uniformity where commit conformance is imperfect |
| Python | python-semantic-release | The right Python tool, already deployed |
| C# | MinVer / Nerdbank.GitVersioning | Derives version from the tag, which makes in-tree drift structurally impossible |
| C++, Shell | release-please (`simple`) or hand-rolled | No native convention exists; pick one and declare it |

`release-it` and `cargo-release` are **not** used anywhere in the org. Earlier
revisions of the standards named them; that was never true.

## What is checked centrally

[`org-conformance-sweep.yml`](../../.github/workflows/org-conformance-sweep.yml)
reads every repo through the API on a schedule — no file is copied into any
repo, so there is nothing to drift. It reports:

- tag grammar matching the repo's declared shape
- in-tree version equal to the latest tag
- a publishable repo having a release workflow
- release config that no workflow invokes

Warn-first, consistent with the org's audit-then-enforce pattern.

## What is not checked

Whether a MAJOR bump was deserved. Whether a changelog entry is meaningful.
Whether the version a human chose matches the change they made. Those are
review, and naming them here is the point — the checks above are a floor,
not a definition of correct versioning.
