# Security Overlay

Applies to anything handling untrusted input, secrets, authentication, payments,
PII, or cryptography — i.e. most of the platform. Composes with all three tiers.
This is the day-to-day checklist; the disclosure process lives in
[`SECURITY.md`](../../SECURITY.md).

## Pre-merge checklist

- [ ] No hardcoded secrets (API keys, passwords, tokens).
- [ ] All user input validated at the boundary (schema-based where possible).
- [ ] SQL: parameterized queries only — no string concatenation.
- [ ] XSS: output encoded; no unsanitized HTML injection.
- [ ] CSRF protection on state-changing requests.
- [ ] AuthN/AuthZ verified on every protected path; fail closed.
- [ ] Rate limiting on public / abusable endpoints.
- [ ] Error messages don't leak secrets, stack traces, or PII.

## Secret management

- Never hardcode secrets. Use environment variables or a secret manager.
- Validate required secrets are present at startup; fail fast if missing.
- Rotate anything that may have been exposed; treat exposure as an incident.
- GitHub native secret scanning and push protection **must be enabled on every
  repository**. This is a requirement, not a description: repository-level push
  protection and user-facing alerts are both off by default, and on private
  repos both need GitHub Secret Protection, which is only available to
  organization-owned repos on a Team or Enterprise plan. (Partner-pattern
  alerts do run automatically on public repos, but they report to the provider
  rather than to us.) Where a repo does not have them, the compensating control
  is the opt-in Gitleaks scan in the required gate plus the shipped git hooks —
  and the gap is written down in that repo's `AGENTS.md`.
- CI runs OSV, zizmor and actionlint everywhere, with opt-in
  Gitleaks/Semgrep/Snyk/vet; Dependency Review
  needs Code Security on private repos (see
  [`security-scan.yml`](../../.github/workflows/security-scan.yml)).

## Reference standards

- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
  and the [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/) for
  application security requirements.
- **CERT** secure coding standards for C/C++/Java.
- **Semgrep** for custom org rules. **CodeQL** only where it is both available
  and licensed. Available: public repositories, or organization-owned
  repositories with GitHub Code Security — without it, code scanning returns
  403 and cannot run at all. Licensed: the CodeQL CLI's terms define an
  *Open Source Codebase* as one "released under an OSI-approved License",
  and grant CI/CD database generation only where that codebase is "hosted
  **and maintained** on GitHub.com" — or under a paid GitHub Advanced
  Security licence. That is the licence's own wording; GitHub has since
  split Advanced Security into Code Security and Secret Protection, and the
  CodeQL entitlement sits in Code Security. Both halves bite:
  a public repo that is unlicensed or source-available is not an Open
  Source Codebase, and an OSI-licensed upstream that is merely mirrored to
  GitHub.com is not maintained there.
  **A private repository without Code Security must not run CodeQL.**

## Web specifics

For browser-facing surfaces, in addition to the checklist:

- A production **Content-Security-Policy** (prefer per-request nonces over
  `'unsafe-inline'`).
- Security headers: `Strict-Transport-Security`, `X-Content-Type-Options: nosniff`,
  `X-Frame-Options: DENY`, `Referrer-Policy: strict-origin-when-cross-origin`,
  a restrictive `Permissions-Policy`.
- SRI for third-party CDN scripts; prefer self-hosting critical dependencies.

## Crypto & auth

- Use vetted libraries; never roll your own primitives.
- Constant-time comparisons for secrets/tokens.
- Short-lived, scoped tokens; verify signatures and audiences.
- OAuth2/OIDC for delegated auth; validate `iss`/`aud`/`exp`.

## Incident response

If you find a security issue mid-work: **stop**, fix the critical issue before
continuing, rotate any exposed secret, and sweep the codebase for the same
pattern. Report per [`SECURITY.md`](../../SECURITY.md) — never via a public issue.
