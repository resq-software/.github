# resq-software/.github

Organization-wide community health files, issue/PR templates, and the public profile for [resq-software](https://github.com/resq-software).

## What's in This Repo

| Path | Purpose |
| :--- | :--- |
| `profile/README.md` | The org profile shown at [github.com/resq-software](https://github.com/resq-software) |
| `assets/` | Shared assets (banner, logo) used across org READMEs |
| `README.template.md` | Standardized README template for new ResQ repositories |

### Community Health Files

Files placed here apply org-wide as defaults for all repositories that don't override them:

| File | Description |
| :--- | :--- |
| `.github/ISSUE_TEMPLATE/` | Bug report and feature request templates |
| `.github/PULL_REQUEST_TEMPLATE.md` | Standard PR checklist |
| `.github/CONTRIBUTING.md` | Contribution guidelines (Conventional Commits, branching, CI) |
| `.github/SECURITY.md` | Vulnerability disclosure policy |
| `.github/CODE_OF_CONDUCT.md` | Contributor Covenant |
| `.github/FUNDING.yml` | Sponsorship links |
| `.github/workflow-templates/` | Reusable CI workflow templates for org repos |

## Updating the Org Profile

Edit `profile/README.md` — GitHub renders it at [github.com/resq-software](https://github.com/resq-software) automatically on push.

Assets referenced from the profile must use absolute URLs pointing to the `main` branch:
```
https://raw.githubusercontent.com/resq-software/.github/main/assets/banner.png
```

## Using the README Template

When creating a new ResQ repository:

1. Copy `README.template.md` to the new repo's root as `README.md`
2. Replace every `{{PLACEHOLDER}}` with real values
3. Delete sections that don't apply
4. Remove all HTML comments before publishing

The template auto-populates with:
- CI badge (requires a `ci.yml` GitHub Actions workflow)
- Version badge (driven by npm/crates.io/PyPI/NuGet release)
- Coverage badge (Codecov integration)

## Ecosystem

| Repo | Role |
| :--- | :--- |
| [resQ](https://github.com/resq-software/resQ) | Core polyglot monorepo (private) |
| [dotnet-sdk](https://github.com/resq-software/dotnet-sdk) | .NET 9 client libraries |
| [mcp](https://github.com/resq-software/mcp) | FastMCP server for AI clients |
| [cli](https://github.com/resq-software/cli) | Rust CLI/TUI toolchain |
| [ui](https://github.com/resq-software/ui) | React component library |
| [programs](https://github.com/resq-software/programs) | Solana Anchor on-chain programs |
| [landing](https://github.com/resq-software/landing) | Marketing site |
| [docs](https://github.com/resq-software/docs) | Official documentation (Mintlify) |
| [resq-proto](https://github.com/resq-software/resq-proto) | Canonical Protobuf schemas |
| [dev](https://github.com/resq-software/dev) | One-command developer onboarding |

## License

Copyright 2026 ResQ. Licensed under the [Apache License, Version 2.0](./LICENSE).
