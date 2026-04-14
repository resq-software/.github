<!--
Thanks for contributing! Fill out every section. Empty sections block review.
See CONTRIBUTING.md in this org for the full contributor flow:
https://github.com/resq-software/.github/blob/main/CONTRIBUTING.md
-->

## Summary

<!-- 1-3 sentences: what this PR changes and why. Keep it tight. -->

## Type of change

<!-- Pick one (or more). Matches the Conventional Commits type of your commits. -->

- [ ] `feat` — new user-visible capability
- [ ] `fix` — bug fix
- [ ] `docs` — documentation only
- [ ] `refactor` — code change that neither fixes a bug nor adds a feature
- [ ] `perf` — performance improvement
- [ ] `test` — adds/updates tests
- [ ] `build` — build system or external dependency
- [ ] `ci` — CI configuration
- [ ] `chore` — other housekeeping
- [ ] Breaking change (`!` in the commit type)

## Test plan

<!-- Concrete commands you ran locally, and what passed. Include test counts
     where relevant. Example:
       - [x] cargo test -p resq-cli → 94 / 94 pass
       - [x] resq format --check → clean
       - [ ] Manual smoke on macOS arm64 (pending hardware)                -->

- [ ]
- [ ]

## Breaking changes

<!-- If none, write "None". Otherwise: what breaks, who's affected, and the
     migration path. Major breakers should also update the repo's CHANGELOG
     on the release-plz PR once cut. -->

None.

## Checklist

- [ ] Commits follow Conventional Commits (`commit-msg` hook will reject otherwise)
- [ ] Branch name matches the allowed pattern (`pre-push` hook will reject otherwise)
- [ ] `resq pre-commit` / `resq format --check` clean locally
- [ ] New code has tests (or a deliberate note on why not)
- [ ] Docs updated (AGENTS.md / README / inline) for user-visible changes
- [ ] Security-sensitive changes acknowledged (secrets, auth, input validation)
