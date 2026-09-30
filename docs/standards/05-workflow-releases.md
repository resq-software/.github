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
waiting on a target that did not exist, and still are — but only for the pins
that point *here*. Measured 2026-09-30 against a fully enumerated org:
**16 of the 21 non-archived repositories** have the `github-actions`
ecosystem enabled — 9 of the 11 public and 7 of the 10 non-public. The other
five carry no Dependabot configuration at all.

Be exact about what those 16 scans do, because an earlier revision of this
section was not. **They are not idle.** They resolve targets and open PRs
routinely: counting the public half alone, four `github-actions` Dependabot
PRs stood open on 2026-09-30 and many more have already merged. What resolves
to nothing is far narrower, and it is the actual failure — **a pin to a
reusable workflow in this repo**. Dependabot moves such a pin to the SHA of a
newer tag; this repo has none; so those pins alone are passed over while every
third-party action in the very same file keeps getting bumped on schedule.
Checked the same day across all 21 repositories and every PR state: **not one
Dependabot PR has ever been opened against one of those pins** — against a
positive control confirming the same query returns third-party bumps.

(This section has carried three wrong figures. It said "eight of eleven", then
"eight" — both public-only views of an org that has twenty-one non-archived
repositories, the same blind spot that made the drift check below report a
clean bill of health it had not earned. It then said "15 of 20", whose
denominator came from the org's *declared* counts; those undercount the
non-public side by one, for a structural reason set out under "Checking for
drift" below. Enumerating every repository and reading each one's Dependabot
config gives 16 of 21. The two differ by exactly the repository the declared
counter omits — a security-advisory temporary fork, which inherits its
parent's config — so "15 of 20" landed close for a reason unrelated to how it
was derived. The figure above is the first one counted rather than inferred.)

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
   half. Re-counted 2026-09-30 by running the sweep's own `pins.awk` over
   every workflow file in all 21 repositories: **23 live pins — 17 public and
   6 non-public — every one of them a full 40-character SHA, and not one
   carrying a version comment.** (The sweep's own run that day reported 22.
   The one-pin difference is not explained here; it does not move the clause,
   since the "without a version comment" figure is the whole population on
   either count. Two further `@main` matches were excluded as documentation
   examples in `.github/workflows/README.md` rather than live pins.) While
   clause 1 is unmet there is no version for a comment to name, so the stated
   rationale is inoperative.
   `org-conformance-sweep.yml` therefore reports the count with that caveat
   attached, rather than filing it against each consumer for a gap on the
   producer side.
3. **Consumers enable the `github-actions` ecosystem** in
   `.github/dependabot.yml`. Its scheduled run then opens a grouped update
   PR for whatever releases are available at that point — propagation is
   systematic, and still reviewed.

   **As written, this clause does not describe what currently happens here.**
   It is not weakened to make it true: it is the standard, and the standard is
   unmet. The ecosystem is enabled in 16 of the 21 repositories and it is
   working — for third-party actions. It is *this* repo that supplies no
   target: zero tags and zero releases, so every pin at
   `resq-software/.github` is skipped and no PR is ever opened for it, while
   the surrounding actions in the same file are bumped as normal. Dependabot
   raises no error for this. The symptom is not a red run and not an absent
   PR — it is a grouped PR that arrives on time and quietly covers everything
   except us, which is why the gap survived five months and why enabling the
   ecosystem in the remaining five repositories would not have surfaced it.
   Nothing on the consumer side closes it. Honouring clause 1 does, and only
   clause 1 does: the first tag and release cut here gives clauses 2 and 3
   something to name and something to bump to on the same day.

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
available the weekly run is red on purpose" — is no longer the state: the
2026-09-30 run completed green with non-public coverage **VERIFIED**. That
sentence was not stale from some earlier era. It was introduced on 2026-09-30
by the commit that merged #65 (`5513c2a`) and was superseded the same day; the
"2026-09-28" this paragraph previously attached to it dated the token
configuration it described, not the sentence, and is dropped rather than left
pointing at the wrong commit. A clean report from the sweep now means clean,
not silent.

That guarantee is bounded, and the bound matters because the report is easy to
over-read. Both modes stay documented: the weaker one is reachable again the
moment the token cannot read the declared count.

A **VERIFIED** report is the strong claim, and it is worth stating exactly
what it claims. It establishes that **the enumeration is not short of what the
org declares** — `public_repos` on the public side, `total_private_repos` on
the non-public side. That is a floor, not an identity.

The difference is live rather than theoretical, because
**`total_private_repos` is not a count of every non-public repository.**
Measured 2026-09-30: this org declared `public_repos=11` and
`total_private_repos=9`, while a full `type=all` enumeration returned 21
repositories — 11 public and 10 non-public. The extra one is a GitHub
security-advisory temporary fork. Such forks are enumerable, and the sweep
does scan them, but they are excluded from `total_private_repos`. The two
figures therefore disagree *by construction* for as long as an advisory draft
exists. That is not a transient, not a partial grant, and not something a
broader token would fix.

So the direction of the comparison carries the entire safety property, and the
two directions are not symmetric:

* **Enumerated fewer than declared** — a genuine coverage gap: the token
  cannot see repositories the org says exist. Hard error, non-zero exit.
* **Enumerated as many as declared, or more** — no coverage gap. The run
  proceeds.

What the run must not do is fold the second case into a claim that the two
numbers *agree*. An excess is reported as an excess, with the advisory-fork
explanation attached, rather than rendered as "the org declares N public + M
non-public and both were fully enumerated" — a sentence which asserts an
equality that does not hold in the state measured above. **This document
describes the sweep's behaviour after that correction.** The version merged
in PR #65 did render the excess case as agreement; that wording is being
fixed alongside this document, and the description here assumes the fix.

Two further limits on VERIFIED, so it is not read as more than it is. It does
not establish that the token could read the *contents* of every repository it
enumerated — unreadable repositories are counted and reported separately. And
it says nothing about repositories the org does not declare and does not list.

An **UNVERIFIED**
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

One caveat on how to read that table at all. **Only a token's creator can see
its permission set.** No reader of this repository can check the middle column
or reproduce any row; each is an assertion by whoever built the token, and the
first four rows describe tokens that no longer need to exist. What *is*
externally observable is narrower and worth keeping separate: the sweep prints
its own mode, so the line
`non-public coverage=VERIFIED second-listing=corroborated` in a public run log
is public evidence that *some* token available to this repository could read
`total_private_repos` on that date. Which token that was, and which
permissions it carried, is not. Treat the table as a lab notebook that records
what was tried, not as a reproducible result.

The gate therefore has two modes, which is all the sweep depends on. When the
field is readable, the non-public side is checked against it as a floor —
short of it is an error, level with or above it is not (**VERIFIED**). When it
is not readable, the sweep reports that side as **UNVERIFIED**,
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
