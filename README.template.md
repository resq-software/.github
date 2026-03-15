<!--
  ResQ README Template
  ═══════════════════════════════════════════════════════════════════
  Usage:
    1. Copy this file to your project root as README.md
    2. Replace every {{PLACEHOLDER}} with real values
    3. Delete sections that don't apply to your project
    4. Delete all HTML comments (<!-- ... -->) before publishing

  Auto-generated fields (see "Automation" section at the bottom):
    - CI badge          → set up GitHub Actions workflow named `ci.yml`
    - Version badge     → driven by npm/crates.io/PyPI/NuGet release
    - Coverage badge    → Codecov integration
    - Changelog content → conventional commits + release tooling

  ═══════════════════════════════════════════════════════════════════
-->

<!-- BANNER: 1200×300 recommended. Store in assets/banner.png or skip entirely -->
<p align="center">
  <img src="assets/banner.png" alt="{{PROJECT_NAME}} Banner" width="100%" />
</p>

<h1 align="center">{{PROJECT_NAME}}</h1>

<!-- TAGLINE: one sentence. What it is and who it's for. -->
<p align="center">
  {{ONE_LINE_DESCRIPTION}}
</p>

<!--
  BADGES — keep this row short (4–6 max).
  Delete the blocks that don't apply. The correct URL pattern per ecosystem:

  ── CI (all projects) ───────────────────────────────────────────────
  https://img.shields.io/github/actions/workflow/status/resq-software/{{REPO}}/ci.yml?branch=main

  ── Version ─────────────────────────────────────────────────────────
  npm:      https://img.shields.io/npm/v/{{NPM_PACKAGE}}
  crates:   https://img.shields.io/crates/v/{{CRATE_NAME}}
  PyPI:     https://img.shields.io/pypi/v/{{PYPI_PACKAGE}}
  NuGet:    https://img.shields.io/nuget/v/{{NUGET_PACKAGE}}

  ── Coverage ─────────────────────────────────────────────────────────
  https://codecov.io/gh/resq-software/{{REPO}}/graph/badge.svg

  ── License (Apache-2.0, all ResQ projects) ─────────────────────────
  https://img.shields.io/badge/license-Apache--2.0-blue.svg
-->
<p align="center">
  <!-- CI -->
  <a href="https://github.com/resq-software/{{REPO}}/actions/workflows/ci.yml">
    <img src="https://img.shields.io/github/actions/workflow/status/resq-software/{{REPO}}/ci.yml?branch=main&label=ci&style=flat-square" alt="CI" />
  </a>
  <!-- Version — pick ONE block below based on ecosystem, delete the rest -->
  <!-- npm -->
  <a href="https://www.npmjs.com/package/{{NPM_PACKAGE}}">
    <img src="https://img.shields.io/npm/v/{{NPM_PACKAGE}}?style=flat-square&label=npm" alt="npm version" />
  </a>
  <!-- crates.io -->
  <a href="https://crates.io/crates/{{CRATE_NAME}}">
    <img src="https://img.shields.io/crates/v/{{CRATE_NAME}}?style=flat-square" alt="crates.io" />
  </a>
  <!-- PyPI -->
  <a href="https://pypi.org/project/{{PYPI_PACKAGE}}/">
    <img src="https://img.shields.io/pypi/v/{{PYPI_PACKAGE}}?style=flat-square" alt="PyPI" />
  </a>
  <!-- NuGet -->
  <a href="https://www.nuget.org/packages/{{NUGET_PACKAGE}}">
    <img src="https://img.shields.io/nuget/v/{{NUGET_PACKAGE}}?style=flat-square" alt="NuGet" />
  </a>
  <!-- Coverage -->
  <a href="https://codecov.io/gh/resq-software/{{REPO}}">
    <img src="https://codecov.io/gh/resq-software/{{REPO}}/graph/badge.svg" alt="Coverage" />
  </a>
  <!-- License -->
  <a href="./LICENSE">
    <img src="https://img.shields.io/badge/license-Apache--2.0-blue.svg?style=flat-square" alt="License: Apache-2.0" />
  </a>
  <!-- Stars -->
  <a href="https://github.com/resq-software">
    <img src="https://img.shields.io/github/stars/resq-software.svg?color=gold&style=flat-square&label=Total%20Stars" alt="Total Stars" />
  </a>
</p>

---

<!-- TABLE OF CONTENTS: include when the README is longer than ~150 lines -->
## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Install](#install)
- [Quick Start](#quick-start)
- [Usage](#usage)
- [Configuration](#configuration)
- [Contributing](#contributing)
- [Changelog](#changelog)
- [License](#license)

---

## Overview

<!-- 2–4 sentences.
     Answer: what problem does this solve? Who uses it? How does it fit
     into the wider ResQ platform? Link to resq.software or related repos. -->

{{PROJECT_NAME}} is a {{WHAT_IT_IS}} for the [ResQ platform](https://resq.software).
It {{CORE_VALUE_PROPOSITION}}.

<!-- If this package is part of a larger ecosystem, list the related repos: -->

**Related projects:**

| Repo | Description |
|------|-------------|
| [resq-software/resQ](https://github.com/resq-software/resQ) | Core platform monorepo |
| [resq-software/cli](https://github.com/resq-software/cli) | CLI tooling |
| [resq-software/ui](https://github.com/resq-software/ui) | Shared component library |
| [resq-software/mcp](https://github.com/resq-software/mcp) | MCP server |
| [resq-software/dotnet-sdk](https://github.com/resq-software/dotnet-sdk) | .NET SDK |

---

## Features

<!-- Bullet-point highlights. Keep to ≤ 8 items; link to docs for depth. -->

- **{{FEATURE_1}}** — {{FEATURE_1_DESCRIPTION}}
- **{{FEATURE_2}}** — {{FEATURE_2_DESCRIPTION}}
- **{{FEATURE_3}}** — {{FEATURE_3_DESCRIPTION}}

---

## Install

<!-- Show the minimal install command for each applicable package manager.
     Delete the blocks that don't apply. -->

### Node / Bun

```sh
bun add {{NPM_PACKAGE}}
# or: npm install {{NPM_PACKAGE}}
```

**Peer dependencies:**

```sh
bun add react@^19 react-dom@^19 tailwindcss@^4
```

### Rust

```sh
cargo add {{CRATE_NAME}}
```

### Python

```sh
uv add {{PYPI_PACKAGE}}
# or: pip install {{PYPI_PACKAGE}}
```

### .NET

```sh
dotnet add package {{NUGET_PACKAGE}}
```

### From source

```sh
git clone https://github.com/resq-software/{{REPO}}.git
cd {{REPO}}
{{BUILD_COMMAND}}
```

---

## Quick Start

<!-- The absolute minimum to get something working in < 5 minutes.
     One short, copy-pasteable code block. -->

```{{LANGUAGE}}
{{MINIMAL_WORKING_EXAMPLE}}
```

---

## Usage

<!-- Expand on Quick Start. Show the most common real-world patterns.
     Prefer short, self-contained examples over walls of prose. -->

### {{USE_CASE_1}}

```{{LANGUAGE}}
{{EXAMPLE_1}}
```

### {{USE_CASE_2}}

```{{LANGUAGE}}
{{EXAMPLE_2}}
```

<!-- For large APIs, link out rather than listing everything inline: -->

Full API reference: [`docs/`](./docs/) or [resq.software/docs/{{REPO}}](https://resq.software/docs)

---

## Configuration

<!-- Environment variables, config files, flags. Use a table when there are > 3 options. -->

| Variable / Flag | Default | Description |
|-----------------|---------|-------------|
| `{{ENV_VAR_1}}` | `{{DEFAULT_1}}` | {{DESCRIPTION_1}} |
| `{{ENV_VAR_2}}` | `{{DEFAULT_2}}` | {{DESCRIPTION_2}} |

<!-- If config is file-based, show a minimal example: -->

```{{CONFIG_FORMAT}}
{{CONFIG_EXAMPLE}}
```

---

## Contributing

We welcome contributions. Please read [`CONTRIBUTING.md`](./CONTRIBUTING.md) before opening a PR.

**Local setup:**

```sh
git clone https://github.com/resq-software/{{REPO}}.git
cd {{REPO}}
{{DEV_SETUP_COMMAND}}
```

**Run tests:**

```sh
{{TEST_COMMAND}}
```

**Commit convention:** This project uses [Conventional Commits](https://www.conventionalcommits.org/).
All PRs must follow the `type(scope): subject` format — see the table below.

| Prefix | Effect on version |
|--------|------------------|
| `feat:` | Minor bump (`0.x.0`) |
| `fix:` / `perf:` | Patch bump (`0.0.x`) |
| `BREAKING CHANGE` footer or `!` suffix | Major bump (`x.0.0`) |
| `docs:` `style:` `refactor:` `test:` `chore:` | No version bump |

---

## Changelog

See [CHANGELOG.md](./CHANGELOG.md) for the full release history.

<!-- The CHANGELOG is auto-generated from conventional commits on every release.
     See the "Automation" section of README.template.md for tooling details. -->

---

## License

Copyright 2026 ResQ

Licensed under the [Apache License, Version 2.0](./LICENSE).

---

<!--
  ═══════════════════════════════════════════════════════════════════
  AUTOMATION REFERENCE — delete this section before publishing
  ═══════════════════════════════════════════════════════════════════

  This section documents the recommended release automation tooling
  for each ecosystem used across the ResQ org.


  ── CONVENTIONAL COMMITS ENFORCEMENT (all projects) ───────────────

  Install commitlint + husky to block non-conforming commits locally:

    bun add -D @commitlint/cli @commitlint/config-conventional husky

    # .commitlintrc.json
    { "extends": ["@commitlint/config-conventional"] }

    # in package.json scripts:
    "prepare": "husky"

    # .husky/commit-msg
    bunx --no -- commitlint --edit "$1"

  For Rust projects, use cocogitto (cog):
    cargo install cocogitto
    cog init   # writes cog.toml


  ── JAVASCRIPT / TYPESCRIPT (ui, landing, programs) ────────────────

  Uses: release-it + @release-it/conventional-changelog
  (already configured in ui)

  .release-it.json:
  {
    "git": { "commitMessage": "chore(release): ${version}" },
    "github": { "release": true },
    "npm": { "publish": true },
    "plugins": {
      "@release-it/conventional-changelog": {
        "preset": "angular",
        "infile": "CHANGELOG.md"
      }
    }
  }

  Run:   bunx release-it
  CI:    bunx release-it --ci  (in GitHub Actions on push to main)

  GitHub Actions workflow snippet:
  ┌─────────────────────────────────────────────────────────────┐
  │  release:                                                   │
  │    runs-on: ubuntu-latest                                   │
  │    steps:                                                   │
  │      - uses: actions/checkout@v4                            │
  │        with: { fetch-depth: 0 }                             │
  │      - uses: oven-sh/setup-bun@v2                           │
  │      - run: bun install                                     │
  │      - run: bunx release-it --ci                            │
  │        env:                                                 │
  │          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}          │
  │          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}                │
  └─────────────────────────────────────────────────────────────┘

  For multi-package repos (if ui ever becomes a monorepo):
  → switch to changesets:
    bun add -D @changesets/cli
    bunx changeset init
  Then contributors run `bunx changeset` before each PR.
  On merge to main, the Changesets GitHub Action opens a
  "Version Packages" PR. Merging that PR publishes all packages.


  ── PYTHON (mcp) ───────────────────────────────────────────────────

  Uses: python-semantic-release (already configured in mcp)

  pyproject.toml:
  [tool.semantic_release]
  version_toml = ["pyproject.toml:project.version"]
  branch = "main"
  changelog_file = "CHANGELOG.md"
  build_command = "uv build"
  commit_message = "chore(release): {version}"

  Run:   semantic-release publish
  CI:    GitHub Actions with PSR action

  GitHub Actions workflow snippet:
  ┌─────────────────────────────────────────────────────────────┐
  │  release:                                                   │
  │    runs-on: ubuntu-latest                                   │
  │    steps:                                                   │
  │      - uses: actions/checkout@v4                            │
  │        with: { fetch-depth: 0 }                             │
  │      - uses: astral-sh/setup-uv@v5                          │
  │      - run: uv sync                                         │
  │      - uses: python-semantic-release/python-semantic-release│
  │        with:                                                │
  │          github_token: ${{ secrets.GITHUB_TOKEN }}          │
  └─────────────────────────────────────────────────────────────┘


  ── RUST (cli) ─────────────────────────────────────────────────────

  Two options:

  Option A — cargo-release + git-cliff (recommended):
    cargo install cargo-release git-cliff

    cliff.toml:
    [changelog]
    header = "# Changelog\n\n"
    body = """
    {% for group, commits in commits | group_by(attribute="group") %}
    ### {{ group | upper_first }}\n
    {% for commit in commits %}
    - {{ commit.message }}{% endfor %}
    {% endfor %}
    """
    [git]
    conventional_commits = true
    filter_unconventional = true
    commit_parsers = [
      { message = "^feat", group = "Features" },
      { message = "^fix", group = "Bug Fixes" },
      { message = "^perf", group = "Performance" },
    ]

    Run:   cargo release minor  (or patch / major)

  Option B — release-plz (GitHub App):
    Monitors your repo, opens release PRs automatically.
    Zero config needed beyond adding the GitHub App.
    https://release-plz.dev

  GitHub Actions workflow snippet (cargo-release):
  ┌─────────────────────────────────────────────────────────────┐
  │  release:                                                   │
  │    runs-on: ubuntu-latest                                   │
  │    steps:                                                   │
  │      - uses: actions/checkout@v4                            │
  │        with: { fetch-depth: 0 }                             │
  │      - uses: dtolnay/rust-toolchain@stable                  │
  │      - run: cargo install git-cliff cargo-release           │
  │      - run: cargo release --execute --no-confirm            │
  │        env:                                                 │
  │          CARGO_REGISTRY_TOKEN: ${{ secrets.CARGO_TOKEN }}   │
  └─────────────────────────────────────────────────────────────┘


  ── .NET (dotnet-sdk) ──────────────────────────────────────────────

  Uses: MinVer (recommended) or Nerdbank.GitVersioning

  MinVer derives versions directly from git tags — no config file needed:
    dotnet add package MinVer  (add to every csproj that ships)

  Tag a release:
    git tag v1.2.3 && git push --tags

  For changelog generation, pair with git-cliff or
  conventional-changelog-dotnet.

  GitHub Actions workflow snippet:
  ┌─────────────────────────────────────────────────────────────┐
  │  release:                                                   │
  │    runs-on: ubuntu-latest                                   │
  │    steps:                                                   │
  │      - uses: actions/checkout@v4                            │
  │        with: { fetch-depth: 0 }                             │
  │      - uses: actions/setup-dotnet@v4                        │
  │        with: { dotnet-version: '9.x' }                      │
  │      - run: dotnet build -c Release                         │
  │      - run: dotnet pack -c Release --no-build               │
  │      - run: dotnet nuget push **/*.nupkg                    │
  │        env:                                                 │
  │          NUGET_API_KEY: ${{ secrets.NUGET_TOKEN }}          │
  └─────────────────────────────────────────────────────────────┘


  ── BADGE AUTO-UPDATE ──────────────────────────────────────────────

  Most shields.io badges are live — they pull data directly from the
  registry (npm, crates.io, PyPI, NuGet, GitHub Actions). No extra
  automation needed; just keep the badge URLs correct.

  For coverage badges, set up Codecov:
    1. Sign in to codecov.io with your GitHub org
    2. Add CODECOV_TOKEN to repo secrets
    3. Add to CI:
         - uses: codecov/codecov-action@v5
           with: { token: ${{ secrets.CODECOV_TOKEN }} }

  ── SUMMARY: which tool per project ───────────────────────────────

  Project    | Tool
  ──────────────────────────────────────────────
  ui         | release-it + @release-it/conventional-changelog ✓ (done)
  mcp        | python-semantic-release ✓ (done)
  cli        | cargo-release + git-cliff  OR  release-plz
  dotnet-sdk | MinVer + git-cliff + manual `dotnet nuget push`
  programs   | (Solana — tag-based, no registry; cargo-release for crates)
  resQ mono  | changesets (if needed) or turborepo + individual tools
  landing    | No versioning needed (private, deploy-only)

  ═══════════════════════════════════════════════════════════════════
-->
