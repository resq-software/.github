<div align="center">
  <h2>resq-software</h2>
  <p>Resilient infrastructure for autonomous systems when traditional networks fail.</p>
  <br/>

  [![Docs](https://img.shields.io/badge/docs-docs.resq.software-0ea5e9?style=flat-square)](https://docs.resq.software)
  [![Website](https://img.shields.io/badge/site-resq.software-0ea5e9?style=flat-square)](https://resq.software)
  [![License](https://img.shields.io/badge/license-Apache--2.0-blue?style=flat-square)](https://github.com/resq-software/.github/blob/main/LICENSE)
</div>

---

ResQ is a mission-critical autonomous platform for decentralized coordination in disaster response and emergency logistics — designed to operate when traditional networks fail.

## Ecosystem

| Repository | What it is | Language |
|-----------|-----------|----------|
| [npm](https://github.com/resq-software/npm) | React component library (shadcn/ui + Radix + Tailwind v4) | TypeScript |
| [crates](https://github.com/resq-software/crates) | Rust CLI/TUI toolset for drone ops (ships the `resq` binary) | Rust |
| [pypi](https://github.com/resq-software/pypi) | FastMCP server + DSA utilities | Python |
| [dotnet-sdk](https://github.com/resq-software/dotnet-sdk) | .NET typed clients + blockchain anchoring | C# |
| [programs](https://github.com/resq-software/programs) | Solana Anchor on-chain programs | Rust |
| [vcpkg](https://github.com/resq-software/vcpkg) | C++ vcpkg libraries | C++ |
| [viz](https://github.com/resq-software/viz) | .NET visualization library | C# |
| [docs](https://github.com/resq-software/docs) | Documentation (Mintlify) | MDX |
| [dev](https://github.com/resq-software/dev) | One-command developer setup + canonical git hooks | Shell |

## Get started

Install the `resq` CLI and onboard a repo in one line:

```bash
curl -fsSL https://get.resq.software | sh
```

This installs a SHA256-verified `resq` binary (with a `cargo install --git` fallback), optionally clones an org repo, and drops the canonical git hooks — copyright, secrets, polyglot format, audit — into it.

Prefer to read before piping to a shell:

```bash
curl -fsSL https://get.resq.software -o install-resq.sh
less install-resq.sh
sh install-resq.sh
```

## Links

[📖 Docs](https://docs.resq.software) · [🌐 Website](https://resq.software) · [🤝 Contributing](https://github.com/resq-software/.github/blob/main/CONTRIBUTING.md)
