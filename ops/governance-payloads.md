# Governance payloads

API payloads for custom repository properties and org rulesets. Apply via
`gh api` after review. Order: **properties → values → rulesets** (rulesets
reference property values, so properties must be defined first).

> **Warning.** Ruleset A enforces `require_code_owner_review`. Any repo
> without `CODEOWNERS` will block every PR until one lands. `resq-proto`
> currently lacks `CODEOWNERS` — either ship one or exclude `resq-proto`
> from Ruleset A's `conditions.repository_name` during a bake period.

---

## 1. Custom property schema

Endpoint: `PATCH /orgs/resq-software/properties/schema` with body:

```json
{
  "properties": [
    {
      "property_name": "domain",
      "value_type": "single_select",
      "required": true,
      "default_value": "tooling",
      "description": "What the repo serves.",
      "allowed_values": ["drone", "backend", "frontend", "sdk", "ops", "infra", "tooling"]
    },
    {
      "property_name": "tier",
      "value_type": "single_select",
      "required": true,
      "default_value": "supporting",
      "description": "Governance strictness target.",
      "allowed_values": ["critical", "supporting", "experimental"]
    },
    {
      "property_name": "lang",
      "value_type": "single_select",
      "required": true,
      "default_value": "polyglot",
      "description": "Which reusable CI to wire.",
      "allowed_values": ["rust", "ts", "py", "cpp", "cs", "proto", "polyglot"]
    }
  ]
}
```

Apply:

```sh
gh api --method PATCH \
  -H "Accept: application/vnd.github+json" \
  /orgs/resq-software/properties/schema \
  --input ops/properties-schema.json
```

Extract the JSON block above into `ops/properties-schema.json` before running — `gh api --input` reads a file.

---

## 2. Property value assignments

Endpoint: `PATCH /orgs/resq-software/properties/values`. One entry per repo.

```json
{
  "properties": [
    { "repository_name": "crates",       "properties": [ {"property_name": "domain", "value": "sdk"},      {"property_name": "tier", "value": "critical"},    {"property_name": "lang", "value": "rust"}      ] },
    { "repository_name": "dev",          "properties": [ {"property_name": "domain", "value": "tooling"},  {"property_name": "tier", "value": "supporting"},  {"property_name": "lang", "value": "ts"}        ] },
    { "repository_name": "dotnet-sdk",   "properties": [ {"property_name": "domain", "value": "sdk"},      {"property_name": "tier", "value": "supporting"},  {"property_name": "lang", "value": "cs"}        ] },
    { "repository_name": "landing",      "properties": [ {"property_name": "domain", "value": "frontend"}, {"property_name": "tier", "value": "supporting"},  {"property_name": "lang", "value": "ts"}        ] },
    { "repository_name": "programs",     "properties": [ {"property_name": "domain", "value": "drone"},    {"property_name": "tier", "value": "supporting"},  {"property_name": "lang", "value": "rust"}      ] },
    { "repository_name": "npm",          "properties": [ {"property_name": "domain", "value": "sdk"},      {"property_name": "tier", "value": "critical"},    {"property_name": "lang", "value": "ts"}        ] },
    { "repository_name": "pypi",         "properties": [ {"property_name": "domain", "value": "sdk"},      {"property_name": "tier", "value": "critical"},    {"property_name": "lang", "value": "py"}        ] },
    { "repository_name": "resQ",         "properties": [ {"property_name": "domain", "value": "backend"},  {"property_name": "tier", "value": "critical"},    {"property_name": "lang", "value": "polyglot"}  ] },
    { "repository_name": "vcpkg",        "properties": [ {"property_name": "domain", "value": "infra"},    {"property_name": "tier", "value": "supporting"},  {"property_name": "lang", "value": "cpp"}       ] },
    { "repository_name": ".github",      "properties": [ {"property_name": "domain", "value": "ops"},      {"property_name": "tier", "value": "supporting"},  {"property_name": "lang", "value": "polyglot"}  ] },
    { "repository_name": "docs",         "properties": [ {"property_name": "domain", "value": "ops"},      {"property_name": "tier", "value": "supporting"},  {"property_name": "lang", "value": "ts"}        ] },
    { "repository_name": "viz",          "properties": [ {"property_name": "domain", "value": "frontend"}, {"property_name": "tier", "value": "supporting"},  {"property_name": "lang", "value": "ts"}        ] },
    { "repository_name": "resq-proto",   "properties": [ {"property_name": "domain", "value": "backend"},  {"property_name": "tier", "value": "critical"},    {"property_name": "lang", "value": "proto"}     ] },
    { "repository_name": "ardupilot",    "properties": [ {"property_name": "domain", "value": "drone"},    {"property_name": "tier", "value": "supporting"},  {"property_name": "lang", "value": "cpp"}       ] }
  ]
}
```

Apply:

```sh
gh api --method PATCH \
  -H "Accept: application/vnd.github+json" \
  /orgs/resq-software/properties/values \
  --input ops/properties-values.json
```

---

## 3. Ruleset A — default-branch baseline (all repos)

```json
{
  "name": "default-branch-baseline",
  "target": "branch",
  "enforcement": "active",
  "conditions": {
    "ref_name": { "include": ["~DEFAULT_BRANCH"], "exclude": [] },
    "repository_name": { "include": ["*"], "exclude": [], "protected": true }
  },
  "bypass_actors": [
    { "actor_id": 5, "actor_type": "RepositoryRole", "bypass_mode": "pull_request" }
  ],
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" },
    { "type": "required_linear_history" },
    {
      "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 0,
        "dismiss_stale_reviews_on_push": true,
        "require_code_owner_review": true,
        "require_last_push_approval": false,
        "required_review_thread_resolution": true
      }
    },
    {
      "type": "required_status_checks",
      "parameters": {
        "strict_required_status_checks_policy": true,
        "required_status_checks": [
          { "context": "required", "integration_id": null }
        ]
      }
    }
  ]
}
```

`bypass_actors.actor_id: 5` = built-in `RepositoryRole: admin`. Adjust or drop if you want zero bypass.

Apply:

```sh
gh api --method POST \
  -H "Accept: application/vnd.github+json" \
  /orgs/resq-software/rulesets \
  --input ops/ruleset-a-baseline.json
```

---

## 4. Ruleset B — critical-tier extras

Adds stricter rules to repos where `tier=critical`. Composes with Ruleset A.

```json
{
  "name": "critical-tier-extras",
  "target": "branch",
  "enforcement": "active",
  "conditions": {
    "ref_name": { "include": ["~DEFAULT_BRANCH"], "exclude": [] },
    "repository_property": {
      "include": [
        { "name": "tier", "property_values": ["critical"], "source": "custom" }
      ],
      "exclude": []
    }
  },
  "bypass_actors": [],
  "rules": [
    {
      "type": "required_deployments",
      "parameters": {
        "required_deployment_environments": ["production"]
      }
    }
  ]
}
```

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

# Soft-disable: PUT replaces the entire ruleset resource, so fetch the
# existing body, toggle `enforcement`, and re-submit the full object.
gh api /orgs/resq-software/rulesets/<id> \
  | jq '.enforcement = "disabled"' \
  | gh api --method PUT /orgs/resq-software/rulesets/<id> --input -

# Hard delete
gh api --method DELETE /orgs/resq-software/rulesets/<id>

# Remove a property from the schema
gh api --method DELETE /orgs/resq-software/properties/schema/<property_name>
```

---

## 7. Extract to standalone files before applying

Before `gh api --input`, split the embedded JSON into its own files:

```
ops/
  properties-schema.json      # JSON from §1
  properties-values.json      # JSON from §2
  ruleset-a-baseline.json     # JSON from §3
  ruleset-b-critical.json     # JSON from §4
```

Each standalone file contains ONLY the JSON body (no Markdown, no code
fences). This Markdown document is the source-of-truth for intent; the
split files are what `gh api --input` consumes.
