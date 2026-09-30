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

Most repos in the org have the `github-actions` ecosystem enabled, and they
are still waiting on a target that does not exist — but only for the pins that
point *here*.

Be exact about what those scans do, because an earlier revision of this section
was not. **They are not idle.** They resolve targets and open `github-actions`
Dependabot PRs routinely, and many have already merged. What resolves to
nothing is far narrower, and it is the actual failure — **a pin to a reusable
workflow in this repo**. Dependabot moves such a pin to the SHA of a newer tag;
this repo has none; so those pins alone are passed over while third-party
actions in the very same file keep getting bumped on schedule. Checked across
every enumerated repository and every PR state: **no Dependabot PR has ever
named one of those pins** — against a positive control confirming the same
query does return third-party bumps.

(This section has twice carried a wrong figure on `main`. It said "eight of
eleven", then "eight" — both public-only views of the org, the same blind spot
that made the drift check below report a clean bill of health it had not
earned. A third figure, "15 of 20", was written while preparing this change and
never reached `main`; it is recorded only because its shape is the trap. Its
denominator came from the org's *declared* counts, which read lower than a full
enumeration on the non-public side — see "When the counts disagree" below, and
note that the declared counter is not something to derive a denominator from.
No replacement figure is given here: none of the above depends on one, and the
sweep renders the current state on every run.)

## The contract

1. **Every user-visible workflow change MUST get a tag here.** Semver on the
   workflows as an interface: `MAJOR` for a breaking input change, `MINOR`
   for a new input or job, `PATCH` for a fix that changes no interface.

   This is an obligation, not a description. It is **not yet true**: this repo
   has no tags and no releases, and many commits have landed on `main` since
   the oldest pin still in use. `06-versioning.md` — "a repo that publishes
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

   Still true, and now checked across the whole org rather than its public
   half: every live pin is a full 40-character SHA and **not one of them
   carries a version comment**. `org-conformance-sweep.yml` counts them on
   every run — read `Check 6 — pin drift` in its latest job summary for the
   current figures rather than a number frozen into this document.

   Read that count as *pin occurrences across enumerated repositories*, not as
   a count of distinct consumers. A repository that carries a fork of another
   repository's workflows contributes its copy of a pin a second time, so
   file-derived figures run high while figures taken from the org's declared
   counters run low — see "When the counts disagree" below. Neither direction
   moves this clause, because no pin anywhere in the population carries a
   version comment.

   While clause 1 is unmet there is no version for a comment to name, so the
   stated rationale is inoperative.
   `org-conformance-sweep.yml` therefore reports the count with that caveat
   attached, rather than filing it against each consumer for a gap on the
   producer side.
3. **Consumers enable the `github-actions` ecosystem** in
   `.github/dependabot.yml`. Its scheduled run then opens a grouped update
   PR for whatever releases are available at that point — propagation is
   systematic, and still reviewed.

   **As written, this clause does not describe what currently happens here.**
   It is not weakened to make it true: it is the standard, and the standard is
   unmet. The ecosystem is enabled in most repositories in the org and it is
   working — for third-party actions. It is *this* repo that supplies no
   target: zero tags and zero releases, so every pin at
   `resq-software/.github` is skipped and no PR is ever opened for it, while
   third-party actions in the same file are bumped as normal. Dependabot
   raises no error for this. What was observed is that **no Dependabot PR, in
   any state, has ever named one of those pins**, while the same query returns
   third-party bumps — so the symptom is not a red run and not an absent PR,
   and enabling the ecosystem in the remaining repositories would not by
   itself surface it. Nothing on the consumer side closes it. Honouring
   clause 1 does, and only clause 1 does: the first tag and release cut here
   gives clauses 2 and 3 something to name and something to bump to on the
   same day.

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

The difference is live rather than theoretical: measured against this org,
`total_private_repos` reads lower than a full `type=all` enumeration of the
non-public side, and at least one enumerated non-public repository carries the
name shape of a GitHub security-advisory temporary fork. Such forks are
enumerable and are scanned by the sweep. The current figures are whatever the
latest run of `org-conformance-sweep.yml` renders; they are not copied into
this document, because they move and because the size of that candidate is
non-public in its own right.

The leading explanation is that `total_private_repos` does not count them. It
is *not* asserted here as an established mechanism: it rests on an agreement
in magnitude, in one organisation, at one moment, and counting cannot show
which repository a counter left out. GitHub documents
neither `total_private_repos` nor `owned_private_repos` beyond their type, so
there is no authoritative statement either way (checked 2026-09-30). What is
established is only the part the sweep depends on: **the declared count and
the enumeration can disagree in this direction, and a broader token does not
close the gap.** The evidence and its limits are set out under "When the
counts disagree" below; this is not a transient and not a partial grant.

So the direction of the comparison carries the entire safety property, and the
two directions are not symmetric:

* **Enumerated fewer than declared** — a genuine coverage gap: the token
  cannot see repositories the org says exist. Hard error, non-zero exit.
* **Enumerated as many as declared, or more** — no *shortfall* against the
  declared count. The run proceeds.

Read the second line for exactly what it says. Equal or greater counts show
the absence of a shortfall; they do **not** establish that no repository was
missed. A run that failed to see one declared repository while enumerating one
the counter leaves out lands precisely on `enumerated == declared` and looks
perfect. Count arithmetic cannot separate those two cases in either direction,
and neither the sweep nor this document claims otherwise.

What the run must not do is fold the second case into a claim that the two
numbers *agree*. An excess is reported as an excess, with the candidate
explanation attached, rather than rendered as "the org declares N public + M
non-public and both were fully enumerated" — a sentence which asserts an
equality that does not hold in the state measured above. The version first
merged for this sweep did render the excess case as agreement; the wording and
the both-directions comparison were corrected before this document landed, and
"When the counts disagree" below describes the behaviour as it now stands.

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

### When the counts disagree

Declared and enumerated are compared in **both** directions, because the two
disagreements mean opposite things.

| shape | meaning | outcome |
| --- | --- | --- |
| enumerated **<** declared | repositories the org declares were not seen | **hard error** — INCOMPLETE, exit non-zero, no conformance conclusion drawn |
| enumerated **>** declared | repositories were seen that the declared counter does not count | **reported** — the run is not failed, and the summary states the excess as an excess |

Over-enumeration keeps the **VERIFIED** mode, deliberately. `VERIFIED` claims
one thing — no repository the org *declares* escaped the enumeration — and an
enumeration larger than the declared count is a *stronger* position for that
claim than an exact match, not a weaker one. Calling it `UNVERIFIED` would
spell a stronger position with the weaker word, and it would throw away the
only mode in which a real shortfall is detectable at all: the sweep would go
blind to genuine under-enumeration for as long as the excess persisted. So the
mode stands and the *sentence* changes — the summary never says the two
figures agree when they do not.

The non-public counts can legitimately differ, and not just transiently. The
leading explanation is that `total_private_repos` **does not count GitHub
security-advisory temporary forks**, while `/orgs/{org}/repos` lists them —
which would make a non-public excess equal to the number of open advisory
drafts an expected steady state rather than something to chase, clearing when
the advisory is published or withdrawn.

**That is a hypothesis, not an established mechanism, and this document does
not have the evidence to call it more.** What has been measured against this
org is that the non-public excess and the advisory-fork name-shape count agree
in size, and that `owned_private_repos` in the same org object matches the
enumeration — so whatever is being left out is left out by
`total_private_repos` specifically, not by the org object as a whole. The
figures themselves are deliberately not written down here: they move, they go
stale, and the size of the candidate is non-public in its own right. The
current state is what a run of `org-conformance-sweep.yml` renders.

What that does *not* establish. It is one org at one moment, and agreement in
magnitude cannot distinguish "the counter omits this repository" from "the
counter omits some other repository while counting the advisory fork".
GitHub's REST reference for `GET /orgs/{org}` lists both
`total_private_repos` and `owned_private_repos` as bare integers with **no
description at all**, so there is no authoritative statement either way
(checked 2026-09-30); if one is later found, it belongs here. A public feature
request reports advisory forks being absent from repository *listings*, which
is the opposite of what is measured here — so the behaviour is not uniform
across organisations or over time, and a sweep that treated the exclusion as a
law would be wrong somewhere else.

The sweep therefore compares the excess against that candidate by
repository-name shape and reports whether the two **match in size**. A match
is an explanation offered, never a clearance granted; an excess that does
*not* match is worth investigating, because something else is then being left
out of the count that the completeness arithmetic rests on.

Only *whether* the two agree in size is ever printed — never the size itself,
which would disclose how many advisories the org has in draft. An advisory
fork's name embeds its GHSA id, and this repository is public, so the job
summary, the annotations and the step log would all carry whatever is printed.

The public side is handled the same way, and has no known exclusion:
`public_repos` counts archived public repositories and the enumeration is
taken over `type=all`, so the two are meant to agree. An excess there renders
as **unexplained**.

Counting has a limit in both directions, stated in the summary rather than
left implied: equal counts show the absence of a *shortfall*, not that the two
sets are identical. A run that missed one declared repository and saw one the
counter excludes lands on `enumerated == declared`.

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
