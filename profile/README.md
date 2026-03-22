<div align="center">

<img src="https://raw.githubusercontent.com/resq-software/.github/main/assets/banner.png" alt="resQ Banner" width="100%">

# resQ

**Secure. Autonomous. Decentralized.**

resQ builds the operating protocols for autonomous drone swarms — ensuring coordination and safety when traditional infrastructure fails. Our mission is resilient, decentralized software for critical disaster response and humanitarian logistics.

[![License: Apache 2.0](https://img.shields.io/badge/license-Apache--2.0-blue.svg?style=flat-square)](./LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/resq-software.svg?color=gold&style=flat-square&label=Stars)](https://github.com/resq-software)

---

## Platform

*Built on a simulation-first methodology — verified in PX4 SITL and Gazebo before any hardware deployment.*

<table align="center">
  <tr>
    <td align="center" width="33%">
      <br><strong>Swarm Orchestration</strong><br>
      Real-time fleet management and decentralized mission coordination via the Hybrid Coordination Engine.
    </td>
    <td align="center" width="33%">
      <br><strong>Edge Autonomy</strong><br>
      On-drone C++/ROS 2 logic for obstacle avoidance, sensor fusion, and GPS-denied navigation.
    </td>
    <td align="center" width="33%">
      <br><strong>Predictive Intelligence</strong><br>
      ML-powered disaster forecasting and RL-optimized deployment strategies.
    </td>
  </tr>
  <tr>
    <td align="center" width="33%">
      <br><strong>Verifiable Trust</strong><br>
      Immutable audit trails anchored on Neo N3 and Solana. Every mission, delivery, and crossing is on-chain.
    </td>
    <td align="center" width="33%">
      <br><strong>Digital Twin</strong><br>
      High-fidelity physics simulation for stress-testing swarm strategies before deployment.
    </td>
    <td align="center" width="33%">
      <br><strong>AI-Native Tooling</strong><br>
      MCP server exposing platform capabilities directly to Claude, Cursor, and other AI clients.
    </td>
  </tr>
</table>

---

## Ecosystem

| Category | Repository | Description |
| :--- | :--- | :--- |
| **Core Platform** | [resQ](https://github.com/resq-software/resQ) | Polyglot monorepo: HCE, PDIE, DTSOP, Infrastructure API, Edge AEAI, Web Dashboard |
| | [programs](https://github.com/resq-software/programs) | Solana Anchor programs: airspace access control and proof-of-delivery |
| **SDKs & Tooling** | [dotnet-sdk](https://github.com/resq-software/dotnet-sdk) | .NET 9 clients for ResQ APIs, Neo N3 anchoring, and simulation harnesses |
| | [mcp](https://github.com/resq-software/mcp) | FastMCP server: drone fleet, simulations, and incident response for AI agents |
| | [cli](https://github.com/resq-software/cli) | Rust CLI/TUI: audit, health diagnostics, log aggregation, deployment orchestration |
| | [ui](https://github.com/resq-software/ui) | React component library — 55+ typed, tree-shakeable components on Radix UI + Tailwind v4 |
| **Infrastructure** | [resq-proto](https://github.com/resq-software/resq-proto) | Canonical Protobuf schemas published to the Buf Schema Registry |
| | [dev](https://github.com/resq-software/dev) | One-command developer onboarding across the full ResQ stack |
| **Content** | [landing](https://github.com/resq-software/landing) | Marketing site — Next.js 15, Tailwind CSS, deployed on Cloudflare Pages |
| | [docs](https://github.com/resq-software/docs) | Official documentation — Mintlify with API references and guides |

---

## Get Started

```bash
# Set up your local environment in one command
curl -fsSL https://raw.githubusercontent.com/resq-software/dev/main/install.sh | sh
```

Installs git, gh CLI, Nix, clones your chosen repo, and configures pre-commit hooks automatically.

---

## Contact

| [Engineering](mailto:engineer@resq.software) | [Partnerships](mailto:contact@resq.software) | [X / Twitter](https://x.com/ResQSoftware) | [LinkedIn](https://www.linkedin.com/company/resq-software/) |
| :---: | :---: | :---: | :---: |

---

Copyright 2026 ResQ — [Apache License 2.0](../LICENSE)

</div>
