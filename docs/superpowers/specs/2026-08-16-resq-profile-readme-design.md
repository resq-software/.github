# RESQ Organization Profile README Design

## Goal

Replace the flat organization profile with a current, developer-first launchpad. A reader should understand what RESQ builds, enter the reproducible development environment, inspect the live system, and find the relevant repository without working through a product pitch.

## Audience And Primary Action

The primary audience is developers and contributors. The primary call to action is the full onboarding installer at `get.resq.software`; live visualization, documentation, and research are adjacent secondary actions.

## Information Hierarchy

The README will use this order:

1. RESQ identity and a concise, supportable platform position.
2. Developer actions: Get Started, Live Viz, Docs, and Research.
3. The current one-command onboarding flow and its delivery guarantees.
4. The four live surfaces: Research, Viz, Docs, and Design.
5. A compact platform map connecting packages, SDKs, coordination infrastructure, simulation, and the operator view.
6. The public repository catalog, grouped by role.
7. Contribution guidance, organization standards, contact, and license.

## Visual Treatment

- Use GitHub-native Markdown with restrained HTML where it improves alignment.
- Center the existing RESQ mark and title without using the outdated 21 MB banner.
- Use flat-square badges for primary destinations and the license.
- Use one compact Mermaid diagram as the visual manual.
- Present Research, Viz, Docs, and Design in a four-column surface strip.
- Group repositories under Experience, SDKs, Runtime, and Foundation rather than presenting one flat language list.
- Keep headings short and avoid emoji-heavy presentation, nested cards, generated artwork, and pitch-deck sections.

The result must remain legible in GitHub light and dark themes and must not depend on custom CSS or JavaScript.

## Content Contract

Repository names and descriptions will be based on the current public GitHub organization state. Include the ten public, non-archived, non-fork product and tooling repositories. Exclude the `.github` meta-repository and the `ardupilot` fork.

The installer section will describe the current `dev` behavior: GitHub authentication, Nix-based reproducible tooling, repository selection, CLI and hook setup, immutable version routes, and pinned, hash-verified delivery. It must distinguish integrity checks from independent proof of origin.

Product copy must remain concise and supportable from public repositories and live RESQ surfaces. Investor claims and unsupported performance, compliance, or security claims do not belong in the organization profile.

## Repository Groups

- **Experience:** `viz`, `npm`, `docs`
- **SDKs:** `dotnet-sdk`, `pypi`
- **Runtime:** `crates`, `programs`
- **Foundation:** `dotnet`, `vcpkg`, `dev`

## Verification

- Confirm every linked RESQ URL responds successfully.
- Re-query public GitHub repositories and verify each eligible repository appears exactly once.
- Validate Markdown structure and embedded HTML balance.
- Validate the Mermaid diagram syntax where local tooling permits.
- Review rendered content for stale names, installer behavior, and unsupported claims.
- Inspect the final diff to ensure unrelated workspace changes are untouched.
