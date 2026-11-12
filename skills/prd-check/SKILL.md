---
name: prd-check
description: "Check a design, spec, plan, or code change against the project's PRD in docs/prd/: point out contradictions, extensions beyond the PRD, and term drift, and ask whether the PRD or the work should change. Use whenever work in a repo with docs/prd/ is designed, planned, or about to be integrated, and whenever a change alters user-visible behavior the PRD covers."
---

# PRD Check

The PRD in `docs/prd/` is the project's agreed product intent: what is
built, for whom, and what is deliberately out. Work that silently drifts
from it makes the PRD a lie. This check catches the drift and puts every
mismatch to the user as a decision: change the PRD, or change the work.

No `docs/prd/` directory, or no PRD that covers the work: say so in one
line and stop.

## When it runs

| Moment | Who triggers it | Subject |
|---|---|---|
| During brainstorming | `brainstorming-with-docs`, live as answers land, then formally at spec self-review | the design / spec |
| After the plan is written | `ship-to-pr`, or you, after `superpowers:writing-plans` | the plan |
| Before integrating a branch | `ship-to-pr`, or you, before `superpowers:finishing-a-development-branch` | the branch diff |
| Ad-hoc changes | you, whenever a fix or quick change alters behavior the PRD covers (a debugging fix, a review fix, a bounded change) | the change |

## 1. Find the PRD

List `docs/prd/*.md`. Pick the PRD(s) whose vision, features or ids cover
the subject; when several do, check against each. Read the chosen PRD in
full, plus `GLOSSARY.md` and the ADRs it links. A PRD with
`status: draft` still counts; say that it's a draft.

## 2. Compare

Read the subject against the PRD and sort every mismatch into one kind.
For a big diff or a long spec, use a subagent per area, each given the
PRD and returning findings in this shape.

| Kind | What it is | Example |
|---|---|---|
| **Contradiction** | The work does what an FR, RULE, non-goal or MVP-scope line says it must not, or does an FR differently from how it's stated | FR-4 says guests can check out; the design requires an account |
| **Extension** | The work adds user-visible behavior, an actor, an integration or a scope item the PRD doesn't mention, or pulls in something the PRD defers to a later version | adds CSV export; the PRD's MVP scope defers export |
| **Gap** | The work claims to realize an FR (cites it, or the spec says so) but leaves part of it out | cites FR-2 but skips its offline condition |
| **Term drift** | The work names a domain concept differently from `GLOSSARY.md` / the PRD, or uses a synonym listed under `_Avoid_` | "Client" where the glossary says "Customer" |

Not findings: implementation detail the PRD rightly leaves to the level
below (data model, libraries, file layout), and internal refactors with no
user-visible effect. Don't pad the list: only mismatches a PRD reader
would care about.

Each finding cites the PRD id or section and the place in the subject
(spec section, plan task, `file:line`).

## 3. Decide, with the user

Put each contradiction, extension and gap to the user as a closed
question through **AskUserQuestion**, up to four per call, most
consequential first. The question text stands alone: quote the PRD line
and say what the work does instead. Options, your recommendation first
with ` (Recommended)`:

- **Amend the PRD** — `preview` shows the proposed PRD text (changed FR,
  new FR, moved scope line)
- **Change the work to fit the PRD** — say what changes
- **Record an open question** — an OQ-N in the PRD, with an owner, and
  the work proceeds as is
- **Not a mismatch** — when you're unsure it is one

Recommend from the evidence: if the user decided the new behavior in this
session, amending the PRD is usually right; if the work drifted by
accident, changing the work is.

Fix term drift in the work directly and list it in one line; if the
glossary is what's wrong, ask (domain-modeling).

**While a plan is executing** (`superpowers:subagent-driven-development`
or `executing-plans`), don't stop to ask: execution runs without pausing.
The spec was already checked against the PRD, and SDD treats the spec as
the binding authority. Where the spec and the PRD still differ, follow the
spec, record the difference as a ledger ruling, and collect it, along with
extensions and anything you're unsure of, for the check before
integration, where they're asked.

## 4. Apply

**Amending the PRD:** edit it in place, following the `prd` skill's
writing rules:

- Ids are never reused. A removed FR stays, marked `(withdrawn)` with a
  one-line reason; a new FR takes the next free number.
- Scope moves update both MVP scope and Non-goals where they apply.
- New terms go to `GLOSSARY.md` (domain-modeling).
- Set `updated` in the frontmatter to today's date (`date +%F`).
- Run `python3 ../prd/scripts/check_ids.py <prd>` (the path is relative
  to this skill's directory; needs Python 3.11+) and fix every hit.
- Commit the PRD change together with the work it belongs to, so a
  reviewer sees both in one PR.

A change to the PRD's vision, users or several features at once is not
an amendment: recommend running the `prd` skill's update instead.

**Changing the work:** update the spec or plan. For code, hand the change
back to the caller: during brainstorming the design changes; in
`ship-to-pr` it's implemented on the branch before the PR (its step 4);
for an ad-hoc change, make it now and rerun the tests.

## 5. Report

End with a short record the caller can reuse (`ship-to-pr` puts it in the
PR description):

```
PRD check against docs/prd/<file>.md:
- Amended: FR-7 (export now in MVP), new FR-12 (CSV export)
- Changed work to fit: FR-4 guest checkout restored
- Open questions added: OQ-5 (export format), owner: <user>
- Term fixes: Client → Customer (3 places)
```

Clean check: `PRD check: no mismatches with docs/prd/<file>.md.`
