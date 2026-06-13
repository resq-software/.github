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
- GitHub native secret scanning + push protection are on; CI runs OSV/Dependency
  Review, with opt-in Gitleaks/Semgrep/Snyk (see
  [`security-scan.yml`](../../.github/workflows/README.md)).

## Reference standards

- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
  and the [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/) for
  application security requirements.
- **CERT** secure coding standards for C/C++/Java.
- **Semgrep** for custom org rules; **CodeQL** for SAST (GitHub default setup).

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
