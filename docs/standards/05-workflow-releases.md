# Reusable Workflow Releases

How a change to a workflow in this repo reaches the repositories that consume
it. Written because it did not, for five months.

## The failure this prevents

Consumer repos pin org workflows by commit SHA, which is correct — an
unpinned `@main` means any push here changes every repo's CI with no review.
But **Dependabot bumps a SHA pin to the SHA of the latest tag**. This repo had
no tags, so there was nothing to bump to, and Dependabot did nothing. It did
not warn; there was no error to see. Consumers sat on April and June pins
while fixes landed here on `main`.

Eight of the repos that already had the `github-actions` ecosystem enabled
were waiting on a target that did not exist. (This section used to say "eight
of eleven". Eleven was a public-only view of the org, which has twenty
non-archived repositories — the same blind spot that made the drift check
below report a clean bill of health it had not earned.)

## The contract

1. **Every user-visible workflow change MUST get a tag here.** Semver on the
   workflows as an interface: `MAJOR` for a breaking input change, `MINOR`
   for a new input or job, `PATCH` for a fix that changes no interface.

   This is an obligation, not a description. It is **not yet true**: this repo
   has no tags and no releases, and 47 commits have landed on `main` since the
   oldest pin still in use. `06-versioning.md` — "a repo that publishes
   nothing still tags" — makes this a rule this repo is itself breaking, and
   it is why propagation is still manual. Clauses 2 and 3 cannot function
   until it is honoured.
2. **Consumers pin by SHA with a trailing version comment**, the same
   convention this repo already applies to third-party actions:

   ```yaml
   uses: resq-software/.github/.github/workflows/required.yml@<sha>  # v1.2.0
   ```

   The SHA is what runs; the comment is what lets Dependabot find the next
   version.

   Currently **no pin in the org carries that comment**, and while clause 1 is
   unmet there is no version for one to name, so the stated rationale is
   inoperative. `org-conformance-sweep.yml` therefore reports the count with
   that caveat attached, rather than filing it against each consumer for a
   gap on the producer side.
3. **Consumers enable the `github-actions` ecosystem** in
   `.github/dependabot.yml`. Its scheduled run then opens a grouped update
   PR for whatever releases are available at that point — propagation is
   systematic, and still reviewed.

   Note what that does *not* promise. The scan is weekly and this repo's
   own config groups all Actions updates, so several releases inside one
   interval collapse into a single PR bumping straight to the newest. You
   get every change, not every release as its own PR. A repo that wants
   per-release granularity has to shorten the interval or drop the group.

## What is breaking

Changing an existing input's default, removing an input, renaming a job that
callers reference, or making a previously-warning check fail. Adding an input
that defaults to off is not breaking, which is why every opt-in job added
here ships `default: false`.

## Checking for drift

`org-conformance-sweep.yml` reads every
`uses: resq-software/.github/.github/workflows/<wf>@<sha>` in every repo it
can enumerate and reports, per pin, how old the pinned commit is and which
workflows have actually changed since — **including the siblings
`required.yml` pulls in by `./` path, which GitHub resolves at the caller's
pinned ref**. A repo on an old `required.yml` is running that whole reusable
suite at that vintage, whatever it pins for the siblings directly, so a check
that reads only the direct pin under-reports badly.

It detects drift. It does not fix it: there is no remediation step and no
automatic PR. And because clause 1 is unmet, "behind the current release" is
not yet a measurable statement — the sweep measures the **age of the pinned
commit** instead (flagging at 30 days and at 90), and separately reports that
a pin carries no `# vX.Y.Z` comment.

Those two thresholds are reported, not enforced: most pins in the org are
already past 90 days, and a check that fails on arrival gets muted rather than
fixed. They become blocking once the current backlog is cleared.

The sweep does hard-fail on one thing: if it cannot enumerate the whole org it
reports INCOMPLETE and exits non-zero, instead of rendering the repos it could
see as a clean result. It needs a token that can read every repo to do that —
the existing org secret `SYNC_TOKEN` granted to this repository, or an
org-wide `ORG_READ_TOKEN`, fine-grained with Metadata, Contents and Actions
read. Until one is available the weekly run is red on purpose. A clean report
from it now means clean, not silent.

Completeness is asserted in one of two modes, and the summary says which one
ran. The org's `public_repos` field is public, so the public side is always
checked strictly against it. The non-public side depends on
`total_private_repos`, which is an **organization-administration** field — it
sits in the owner-only block of the org object alongside `plan` and
`disk_usage`, and it is *not* a membership field. The read-only token above
therefore does not see it, and that alone is not a failure: the sweep reports
the non-public side as **UNVERIFIED**, corroborates the enumeration against an
independent GraphQL listing, and refuses to continue if it enumerated no
non-public repositories at all — which is exactly the 2026-09-28
public-only-token configuration. Adding **Organization administration: Read**
to the token upgrades that side to **VERIFIED** and restores the strict
shortfall arithmetic. A partial non-public *grant* is only detectable in
VERIFIED mode; that limitation is printed in the summary rather than papered
over.

Run health is scored against each workflow's own `on:` block, not a fixed
wall-clock window: the window is two missed fires of the workflow's shortest
cron interval, floored at 14 days, so a weekly cron lands on exactly 14 days
and a monthly cron gets 60. A workflow with no schedule is **not** judged on
elapsed days at all — there is no cadence to be late against — and a workflow
that has never run is only a finding when it is scheduled and already older
than its own window.

What it still does not check: whether a pinned workflow is *behind a release*
(there are none), and whether the drift it finds ever gets fixed.
