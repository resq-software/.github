<div align="center">
  <img src="https://raw.githubusercontent.com/resq-software/.github/main/assets/resq-icon-color.svg" alt="ResQ Systems mark" width="88">
  <h1>ResQ Systems</h1>
  <p><strong>Coordination software for disaster response.</strong></p>
  <p>Resilient coordination, simulation, and operator tooling for systems working through degraded networks and infrastructure.</p>

  [![Get started](https://img.shields.io/badge/get_started-get.resq.software-238636?style=flat-square&logo=gnubash&logoColor=white)](https://get.resq.software)
  [![Live visualization](https://img.shields.io/badge/live_viz-viz.resq.software-0e7490?style=flat-square)](https://viz.resq.software)
  [![Documentation](https://img.shields.io/badge/docs-docs.resq.software-2563eb?style=flat-square)](https://docs.resq.software)
  [![Research](https://img.shields.io/badge/research-research.resq.software-7c3aed?style=flat-square)](https://research.resq.software)
  [![License](https://img.shields.io/badge/license-Apache--2.0-475569?style=flat-square)](https://github.com/resq-software/.github/blob/main/LICENSE)
</div>

## Start building

Bootstrap a reproducible RESQ development environment:

```bash
curl -fsSL https://get.resq.software | sh
```

The onboarding flow authenticates with GitHub, installs a pinned Nix toolchain, lets you choose a repository, enters its development environment, and configures the `resq` CLI and canonical git hooks.

`get.resq.software` serves pinned, SHA-256-verified artifacts and exposes immutable version routes for provisioning. Those checks protect the GitHub-to-Worker delivery path and detect corrupted downloads; they are not an independent signature or proof of origin. See [`resq-software/dev`](https://github.com/resq-software/dev) for version pinning, unattended setup, digest verification, and the complete trust model.

## Explore the system

<table>
  <tr>
    <td width="25%" valign="top">
      <a href="https://research.resq.software"><strong>Research</strong></a><br>
      Working papers, technical notes, and field reports on emergency-response software.
    </td>
    <td width="25%" valign="top">
      <a href="https://viz.resq.software"><strong>Viz</strong></a><br>
      Live 3D common operating picture for simulated multi-agency response.
    </td>
    <td width="25%" valign="top">
      <a href="https://docs.resq.software"><strong>Docs</strong></a><br>
      Operations manual, platform concepts, integration guides, and API references.
    </td>
    <td width="25%" valign="top">
      <a href="https://design.resq.software"><strong>Design</strong></a><br>
      Component library and engineering design system used across RESQ interfaces.
    </td>
  </tr>
</table>

## Platform map

```mermaid
flowchart LR
    Foundation["Packages & building blocks<br/>npm · dotnet · vcpkg"]
    Tools["SDKs & developer tools<br/>dotnet-sdk · pypi · crates"]
    Operations["Simulation & operations<br/>viz"]

    Foundation --> Tools
    Tools --> Operations
```

## Public repositories

### Experience

| Repository | What it provides | Stack |
| :--- | :--- | :--- |
| [`viz`](https://github.com/resq-software/viz) | Web application | TypeScript, Three.js, .NET |
| [`npm`](https://github.com/resq-software/npm) | Shared TypeScript packages | TypeScript, React |
| [`docs`](https://github.com/resq-software/docs) | Documentation site | MDX, Mintlify |

### SDKs

| Repository | What it provides | Stack |
| :--- | :--- | :--- |
| [`dotnet-sdk`](https://github.com/resq-software/dotnet-sdk) | .NET SDK | C#, Protobuf |
| [`pypi`](https://github.com/resq-software/pypi) | Python package (MCP server) | Python, FastMCP |

### Runtime

| Repository | What it provides | Stack |
| :--- | :--- | :--- |
| [`crates`](https://github.com/resq-software/crates) | The `resq` CLI and TUI workspace | Rust |

### Foundation

| Repository | What it provides | Stack |
| :--- | :--- | :--- |
| [`dotnet`](https://github.com/resq-software/dotnet) | .NET libraries | C# |
| [`vcpkg`](https://github.com/resq-software/vcpkg) | C++ libraries (vcpkg registry) | C++17, CMake |
| [`dev`](https://github.com/resq-software/dev) | Developer onboarding scripts and git hooks | Shell, PowerShell, Nix |

## Contribute

Start with the repository's `AGENTS.md` for its architecture and local commands, then read the organization-wide [contribution guide](https://github.com/resq-software/.github/blob/main/CONTRIBUTING.md) and [engineering standards](https://github.com/resq-software/.github/tree/main/docs/standards). Security reports follow the [coordinated disclosure policy](https://github.com/resq-software/.github/blob/main/SECURITY.md).

Questions about the platform or an integration? Contact [contact@resq.software](mailto:contact@resq.software).

Copyright 2026 ResQ Systems, Inc. Public RESQ projects are individually licensed; organization defaults are available under [Apache License 2.0](https://github.com/resq-software/.github/blob/main/LICENSE).
