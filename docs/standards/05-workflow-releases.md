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

The repos that already had the `github-actions` ecosystem enabled were
waiting on a target that did not exist, and still are. Measured 2026-09-30
against a fully enumerated org: **15 of the 20 non-archived repositories**
have the ecosystem enabled, and all 15 find nothing to bump.

(This section has carried two undercounts. It first said "eight of eleven";
eleven was a public-only view of an org that has twenty non-archived
repositories — the same blind spot that made the drift check below report a
clean bill of health it had not earned. It then said "eight", still a
public-only figure. Both are superseded by the line above, which is the first
count taken with non-public coverage VERIFIED.)

## The contract

1. **Every user-visible workflow change MUST get a tag here.** Semver on the
   workflows as an interface: `MAJOR` for a breaking input change, `MINOR`
   for a new input or job, `PATCH` for a fix that changes no interface.

   This is an obligation, not a description. It is **not yet true**: this repo
   has no tags and no releases, and 48 commits have landed on `main` since the
   oldest pin still in use (that pin dates to 2026-04-17; the count is as of
   2026-09-30 and only grows). `06-versioning.md` — "a repo that publishes
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

   Still true, and now measured across the whole org rather than its public
   half: the 2026-09-30 VERIFIED run found **22 pins, 22 of them without a
   version comment**. While clause 1 is unmet there is no version for one to
   name, so the stated rationale is inoperative.
   `org-conformance-sweep.yml` therefore reports the count with that caveat
   attached, rather than filing it against each consumer for a gap on the
   producer side.
3. **Consumers enable the `github-actions` ecosystem** in
   `.github/dependabot.yml`. Its scheduled run then opens a grouped update
   PR for whatever releases are available at that point — propagation is
   systematic, and still reviewed.

   **As written, this clause describes nothing that currently happens.** It
   is not weakened here to make it true: it is the standard, and the standard
   is unmet. 15 of 20 repositories have the ecosystem enabled and this repo
   has zero tags and zero releases, so all 15 scans resolve to no target and
   open no PR. Dependabot raises no error for this — the symptom is silence,
   which is how the gap survived five months. Nothing on the consumer side
   closes it. Honouring clause 1 does, and only clause 1 does: the first tag
   and release cut here gives clauses 2 and 3 something to name and something
   to bump to on the same day.

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
read, plus organization Administration: Read for the strict non-public count
(see the permission table below).

Such a token is now configured. The sentence that stood here — "until one is
available the weekly run is red on purpose" — described 2026-09-28 and is no
longer the state: the 2026-09-30 run completed green with non-public coverage
**VERIFIED**. A clean report from it now means clean, not silent.

That guarantee is bounded by which mode the run was in, and the bound matters
because the report is easy to over-read. Both modes stay documented: the
weaker one is reachable again the moment the token cannot read the declared
count. A **VERIFIED** report is the strong claim: both sides were checked
against the org's own declared counts, so a clean result means every
repository in the org was checked. An **UNVERIFIED**
report is not that claim and must not be quoted as one. It establishes that
the public side is complete and that the token can see *some* non-public
repositories; it **cannot prove that it saw every non-public repository**,
because the declared count it would have to check against is unreadable, and a
partial non-public grant looks identical to a full one from inside the run. So
a clean UNVERIFIED report is evidence that nothing was found among the
repositories that *were* read — not proof that every repository was read. The
summary names the mode on every run for exactly this reason.

Completeness is asserted in one of two modes, and the summary says which one
ran. The org's `public_repos` field is public, so the public side is always
checked strictly against it. The non-public side depends on
`total_private_repos`, which sits in the owner-only block of the org object
alongside `plan` and `disk_usage`.

**The fine-grained route is now measured.** Two earlier revisions of this
document got this wrong in opposite directions — the first asserted the field
is an *organization-administration* field and *not* a membership field, which
nobody had checked; the second recorded that the fine-grained advice was
untested, which was true when written and is no longer. A fine-grained token
has since been built and run. Every row below is a reading of
`/orgs/{org}` `.total_private_repos` against this org:

| token | permissions | `total_private_repos` | measured |
| --- | --- | --- | --- |
| a user who is not a member of the org | classic `repo`, `read:org` | absent | 2026-09-29 |
| a user who is not a member of the org | classic `repo`, `read:org` | absent | 2026-09-29 |
| a user who is an org **owner** | classic `repo`, `read:org` | present | 2026-09-29 |
| a user who is an org **owner** | classic `admin:org`, `repo`, … | present | 2026-09-29 |
| fine-grained PAT, **all** repositories | repository Actions + Contents + Metadata: Read, **plus organization Administration: Read** | **present** | 2026-09-30 |

Two things follow. It is **not** the `admin:org` scope that exposes the field
— a `read:org` token belonging to an org owner sees it. And the fine-grained
route works: with that last permission set the sweep ran in **VERIFIED** mode
on 2026-09-30, reporting
`non-public coverage=VERIFIED second-listing=corroborated`.

State the limit of that result precisely, because it is narrower than it
looks. What is established is that **this permission set exposes the field**.
What is **not** established:

* **That the set is minimal.** Every permission in that row was granted at
  once. None was dropped and the token re-tested.
* **That `Administration: Read` is the permission doing the work.** No token
  was tried with `Administration: Read` absent and the rest present. The
  field's appearance is therefore attributed to the set as a whole, not to any
  one member of it.
* **Whether plain org membership would also suffice.** The classic-token rows
  above leave that open, and still do: no non-owner member token has ever been
  available to separate membership from owner-level privilege.

So grant the whole set. Do not trim it on the assumption that some subset is
enough — that is precisely the experiment nobody has run.

The gate therefore has two modes, which is all the sweep depends on. When the
field is readable, the non-public side is checked strictly against it
(**VERIFIED**). When it is not, the sweep reports that side as **UNVERIFIED**,
corroborates the enumeration against an independent GraphQL listing, and
refuses to continue if it enumerated no non-public repositories at all — which
is exactly the 2026-09-28 public-only-token configuration. A partial
non-public *grant* is only detectable in VERIFIED mode; that limitation is
printed in the summary rather than papered over.

That corroboration has three outcomes, not two, because the GraphQL call can
also simply fail — a rate limit, a transient 5xx, a token GraphQL rejects. The
sweep therefore reports it as `corroborated`, `DISAGREED` or `unavailable`
rather than as a boolean. A disagreement establishes that the two listings do
not agree, which makes the enumeration untrustworthy, and it fails the run. It
does not establish *why* they disagree: truncation of one of them is one cause,
but a repository created or deleted between the two calls produces the same
mismatch, and the sweep takes the REST and GraphQL counts in separate requests.
So the run fails on the disagreement itself, and truncation is claimed only
where the sweep actually detects it. `unavailable` does not fail the run — the gate
stays the enumeration-completeness assertion, and a job that goes red on a
transient error is a job that gets muted — but it does change what the report
says: in UNVERIFIED mode the GraphQL listing is the *only* independent check on
the non-public enumeration, so when it does not answer the summary states that
the non-public count is corroborated by nothing on that run, instead of
claiming a check that never ran.

Run health asks two independent questions, and only one of them involves
cadence:

* **Did its last run fail?** Asked of every workflow, scheduled or not.
  Nothing about a workflow being event-driven makes a red run acceptable, so
  this is a finding regardless of cadence (`FAILING`, or `NEVER-GREEN` when it
  has never once succeeded).
* **Is it overdue against its own schedule?** Scored against the workflow's
  own `on:` block, not a fixed wall-clock window: the window is two missed
  fires of its shortest cron interval, floored at 14 days, so a weekly cron
  lands on exactly 14 days and a monthly cron gets 60 (`STALE`). A workflow
  with no schedule is **not** judged on elapsed days at all — there is no
  cadence to be late against.

Cadence therefore controls only whether *elapsed time* counts against a
workflow. An on-demand workflow with a failing last run is reported; an
on-demand workflow that has simply not been invoked recently is not. A
workflow that has never run is only a finding when it is scheduled and already
older than its own window.

What it still does not check: whether a pinned workflow is *behind a release*
(there are none), and whether the drift it finds ever gets fixed.
