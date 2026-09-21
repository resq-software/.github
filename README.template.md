<!--
  ResQ README starter. Copy to your repo root as README.md, then:
    1. Replace every {{PLACEHOLDER}}
    2. Delete every section that does not apply — a short accurate README
       beats a long one with empty headings
    3. Delete this comment block

  Do NOT leave a marker comment behind. repo-standards.yml warns on any
  leftover {{PLACEHOLDER}} token, and a README is checked for a title, a
  description line, a License section, unique H2s, and working relative
  links. See docs/standards/README.md.

  House style, derived from what the org's repos actually do rather than
  from taste: badges are `style=flat-square`; Mermaid is used for branching
  flows, not for decoration; there are no emoji in headings.
-->

<!--
Copyright 2026 ResQ Systems, Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

<div align="center">

<img src="https://raw.githubusercontent.com/resq-software/.github/main/assets/resq-icon-color.svg" alt="" width="72" />

<h1>{{PROJECT_NAME}}</h1>

<p><em>{{ONE_LINE_DESCRIPTION}}</em></p>

<!-- Keep the badges that are true. Delete the rest — a permanently grey
     badge is worse than no badge. {{DEFAULT_BRANCH}} is `main` for most
     repos; check yours, two default to `master`. -->

[![CI](https://img.shields.io/github/actions/workflow/status/resq-software/{{REPO}}/ci.yml?branch={{DEFAULT_BRANCH}}&style=flat-square&logo=githubactions&logoColor=white&label=ci)](https://github.com/resq-software/{{REPO}}/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/license-Apache--2.0-blue?style=flat-square)](./LICENSE)

<!-- Then ONE release badge for what you actually publish, and any
     stack-version badges worth pinning (runtime, framework, standard): -->
<!-- [![npm](https://img.shields.io/npm/v/{{PACKAGE}}?style=flat-square&logo=npm)](https://www.npmjs.com/package/{{PACKAGE}}) -->
<!-- [![crates.io](https://img.shields.io/crates/v/{{PACKAGE}}?style=flat-square&logo=rust)](https://crates.io/crates/{{PACKAGE}}) -->
<!-- [![PyPI](https://img.shields.io/pypi/v/{{PACKAGE}}?style=flat-square&logo=pypi)](https://pypi.org/project/{{PACKAGE}}/) -->
<!-- [![NuGet](https://img.shields.io/nuget/v/{{PACKAGE}}?style=flat-square&logo=nuget)](https://www.nuget.org/packages/{{PACKAGE}}) -->

</div>

<!-- OPTIONAL, and the most valuable thing you can write if it applies.
     State what this is NOT and which outputs are advisory, before you
     state what it is. Safety- and device-adjacent repos should always
     have one. Example from the org:
       > **Operating boundary:** simulation-only. Track fusion output is
       > advisory and must not be used for flight-critical decisions. -->

> **Operating boundary:** {{SCOPE_AND_NON_GOALS}}

## Overview

{{PROJECT_NAME}} {{WHAT_IT_DOES}}. It is used by {{WHO_USES_IT}}.

<!-- Two or three paragraphs of mechanism, not marketing. Say how it works
     and where it sits, so a reader can decide whether to keep reading.

     If you quote numbers, say which kind each one is — this is the single
     habit that keeps a README honest as the code moves:
       * a LIMIT the build enforces (a CI gate, a compile-time bound), or
       * a MEASUREMENT from one run, pinned to a date and a commit.
     Never present the second as the first. -->

## Packages

<!-- Monorepos only — delete this section for a single-artifact repo.
     Index every published package so the root README is a real entry
     point rather than a signpost. -->

| Package | Description | Version |
| ------- | ----------- | ------- |
| [`{{PACKAGE}}`](./{{PATH}}) | {{WHAT_IT_DOES}} | {{VERSION_BADGE_OR_NUMBER}} |

## Quick start

<!-- The shortest path from clone to a result the reader can see. Prefer
     commands that ASSERT something, so the README fails loudly when it
     goes stale rather than misleading someone quietly. -->

```{{LANGUAGE}}
{{MINIMAL_WORKING_EXAMPLE}}
```

<!-- Prerequisites belong here, at the point of use, not in a table far
     above. Pin to the versions CI actually tests. -->

## Architecture

<!-- The most common section in the org after Overview, and the one the
     old template never asked for. Name real types and link real files, so
     the section is checkable against the code.

     Use Mermaid where a flow BRANCHES (failure and recovery paths,
     lifecycle transitions). A diagram of a straight line is decoration. -->

```mermaid
flowchart LR
  A[{{INPUT}}] --> B[{{COMPONENT}}]
  B -->|ok| C[{{RESULT}}]
  B -->|{{FAILURE_MODE}}| D[{{RECOVERY}}]
```

## Development

```bash
git clone https://github.com/resq-software/{{REPO}}.git
cd {{REPO}}
{{SETUP_COMMAND}}
{{TEST_COMMAND}}
```

<!-- List the gates in the order CI runs them, so a contributor can
     reproduce a red build locally in the same sequence. -->

## Contributing

Read [CONTRIBUTING.md](https://github.com/resq-software/.github/blob/main/CONTRIBUTING.md)
for commit format, branch naming and the PR flow, and the
[engineering standards](https://github.com/resq-software/.github/tree/main/docs/standards)
for the tier that applies to this repo.

<!-- Link ./CONTRIBUTING.md, ./SECURITY.md or ./CHANGELOG.md ONLY if that
     file exists in THIS repo. Most repos inherit them org-wide from
     resq-software/.github, and a relative link to a file you do not have
     is a broken link that repo-standards.yml will flag. -->

## Security

Report vulnerabilities privately per
[SECURITY.md](https://github.com/resq-software/.github/blob/main/SECURITY.md).
Never open a public issue for a suspected vulnerability.

## License

Apache-2.0 — see [LICENSE](./LICENSE). Copyright 2026 ResQ Systems, Inc.
