# Governance payloads

API payloads for custom repository properties and org rulesets. The
four JSON files in this directory are the direct `gh api --input`
inputs; this document explains *why* each one is shaped the way it
is. Apply order: **properties → values → rulesets**.

Files in this directory:

```text
ops/
  properties-schema.json      # schema for domain/tier/lang
  properties-values.json      # per-repo assignments (wrapper shape; see §2)
  ruleset-a-baseline.json     # default-branch-baseline ruleset
  ruleset-b-critical.json     # critical-tier-extras ruleset
  governance-payloads.md      # this document
```

---

## Pre-flight checklist — do these BEFORE flipping rulesets to `active`

Ruleset A enforces `require_code_owner_review`. A repo without
`CODEOWNERS` will block every PR on ruleset activation, because there
is no code owner to approve. The recommended rollout:

1. **Apply rulesets in `evaluate` mode first** (the JSON files ship
   this way). Evaluate mode logs violations without blocking any
   merge. Watch `github.com/organizations/resq-software/settings/rules/<id>`
   for a week of real PR traffic.
2. **Verify every repo has a `.github/CODEOWNERS` or `CODEOWNERS`**
   before flipping to `active`. As of commit time the following repos
   are missing one:
   - `resq-software/.github` — fixed in this PR (see `.github/CODEOWNERS`).
   - `resq-software/ardupilot` — open; add a small CODEOWNERS before
     activation, or exclude via `conditions.repository_name.exclude`
     in Ruleset A during the bake window.
   - `resq-software/resq-proto` — fix is pending in
     `resq-software/resq-proto#2`; merge that first.
3. **Confirm at least one consumer repo has merged a PR emitting the
   `required` status check** (created by the reusable `required.yml`
   workflow in this repo). If none do, the `required_status_checks`
   rule in Ruleset A will fail every future PR once active.
4. **Flip to active:**
   ```sh
   # Fetch the current body, toggle enforcement, PUT the full object back.
   # PUT with a partial body would strip the ruleset of its rules.
   gh api /orgs/resq-software/rulesets/<id> \
     | jq '.enforcement = "active"' \
     | gh api --method PUT /orgs/resq-software/rulesets/<id> --input -
   ```

---

## 1. Custom property schema (`ops/properties-schema.json`)

Endpoint: `PATCH /orgs/resq-software/properties/schema`.

Defines three custom properties: `domain` (what the repo serves),
`tier` (governance strictness), `lang` (which reusable CI to wire).
`tier=critical` is the selector Ruleset B uses.

Apply:

```sh
gh api --method PATCH \
  -H "Accept: application/vnd.github+json" \
  /orgs/resq-software/properties/schema \
  --input ops/properties-schema.json
```

---

## 2. Property value assignments (`ops/properties-values.json`)

**Important:** the org-bulk endpoint `PATCH /orgs/<org>/properties/values`
requires one repo-list + one uniform property-set per call, which
doesn't match a per-repo assignment scheme. The file in this
directory is a **wrapper** around the per-repo payloads; apply it
with a loop that hits the per-repo endpoint
`PATCH /repos/<owner>/<repo>/properties/values`:

```sh
jq -r '.repos[] | @json' ops/properties-values.json | while read -r row; do
  repo=$(jq -r '.repository_name' <<<"$row")
  props=$(jq '{properties: .properties}' <<<"$row")
  gh api --method PATCH \
    -H "Accept: application/vnd.github+json" \
    "/repos/resq-software/$repo/properties/values" \
    --input - <<<"$props"
done
```

---

## 3. Ruleset A — default-branch baseline (`ops/ruleset-a-baseline.json`)

Applies to every repo's default branch. Rules: deletion block,
no force-push, linear history, PR with code-owner review, required
status check `required` (emitted by the reusable `required.yml`
workflow).

**Intent note on `required_approving_review_count: 0` +
`require_code_owner_review: true`**: for the solo-dev stage of
resq-software, this means a CODEOWNER must be requested as a
reviewer but their review does not block merge — effectively a
visibility guarantee rather than an approval gate. When the team
grows past one person, bump `required_approving_review_count` to
`1` and tighten CODEOWNERS coverage.

`bypass_actors.actor_id: 5` is the built-in `RepositoryRole: admin`
with `bypass_mode: pull_request` — org admins can merge via a PR
even if CODEOWNERS disapproves, but cannot push directly.

Apply:

```sh
gh api --method POST \
  -H "Accept: application/vnd.github+json" \
  /orgs/resq-software/rulesets \
  --input ops/ruleset-a-baseline.json
```

---

## 4. Ruleset B — critical-tier extras (`ops/ruleset-b-critical.json`)

Targets repos where custom-property `tier=critical` (currently:
`crates`, `npm`, `pypi`, `resQ`, `resq-proto`). Composes with
Ruleset A.

Rules:
- `pull_request` with `require_last_push_approval: true` — any new
  push to a PR after approval re-requires approval.
- `required_signatures` — commits on the default branch must be
  GPG/SSH-signed.

**Deviation from an earlier draft:** the original plan used
`required_deployments: ["production"]`. That was dropped because
no critical-tier repo currently has a `production` environment
configured — applying that rule would have blocked every merge.
Add environments later and swap the rule in if desired.

**`bypass_actors: []` is intentional.** Admin bypass is allowed on
Ruleset A (baseline rules) but NOT on Ruleset B (critical-tier
extras), so admins cannot emergency-merge an unsigned commit or
unresolved-push-approval PR on critical repos. If that's too
strict, add an admin bypass entry mirroring Ruleset A.

Apply:

```sh
gh api --method POST \
  -H "Accept: application/vnd.github+json" \
  /orgs/resq-software/rulesets \
  --input ops/ruleset-b-critical.json
```

---

## 5. Verification (read-only)

```sh
gh api /orgs/resq-software/properties/schema
gh api /orgs/resq-software/properties/values
gh api /orgs/resq-software/rulesets
gh api /repos/resq-software/pypi/rules/branches/main
```

---

## 6. Rollback

```sh
# List with IDs
gh api /orgs/resq-software/rulesets --jq '.[] | {id, name, enforcement}'

# Soft-disable: PUT replaces the entire ruleset resource, so fetch
# the existing body, toggle `enforcement`, and re-submit the full
# object. PUT-ing only `enforcement` would strip the ruleset.
gh api /orgs/resq-software/rulesets/<id> \
  | jq '.enforcement = "disabled"' \
  | gh api --method PUT /orgs/resq-software/rulesets/<id> --input -

# Hard delete
gh api --method DELETE /orgs/resq-software/rulesets/<id>

# Remove a property from the schema
gh api --method DELETE /orgs/resq-software/properties/schema/<property_name>
```
