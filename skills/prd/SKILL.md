---
name: prd
description: "Grill the user into a Product Requirements Document (PRD): what to build, for whom, and how success is measured. Use when the user asks for a PRD, product requirements, a product spec, or wants a product idea written up before any technical design."
---

# PRD

Produce a PRD for a reader who will use it without this conversation: a
team, human or AI, that builds from it. The user holds the knowledge; you
grill it out of them, pressure-test it and structure it. Nothing goes in
as theirs that they did not say or accept.

The document is the BMad Method's PRD (its `prd` and `prd-light`
templates, ids, assumption tags, review rubric and finalize steps). The
interview is Matt Pocock-style grilling. There is no `_bmad/` setup, no
persona, no ticket tree.

A PRD says **what** and **why**. The **how** is the technical design,
which `brainstorming-with-docs` produces from the PRD afterwards.

## Files

- `templates/prd.md` — full PRD, for anything with real stakes or more
  than one builder
- `templates/prd-light.md` — short PRD with technical direction, for a
  very simple or hobby project
- `rubric.md` — the review rubric
- `scripts/check_ids.py` — finds broken id references and a stale table
  of contents (`python3 <this skill's dir>/scripts/check_ids.py <prd.md>`)

Paths are relative to this skill's directory.

## The template is the brief

Comments above the first heading (`purpose`, `stance`, `inputs`,
`writing`, `done when`, `next`) and under each heading steer you and
never go into the document. Neither do the frontmatter keys
`description`, `not_for`, `facilitation_order` and `word_ceiling`.

`facilitation_order` lists the `##` sections by id (heading lowercased,
punctuation dropped, spaces as hyphens). An id ending in `?` is not
required; its comment says whether it is `optional` (the user's call) or
`include when` (its condition's). Where a template comment conflicts with
this skill, the template wins.

## 1. Gather before asking

Invoke the `grilling` and `domain-modeling` skills. Then, before the
first round, read everything that bears on the PRD: what the user pointed
at (long sources through subagents that extract only what matters),
`GLOSSARY.md` / `GLOSSARY-MAP.md`, `docs/adr/`, any parent PRD in
`docs/prd/`, and the code if the product already exists.

Look for an unfinished PRD (`status: draft`) for this topic in
`docs/prd/`. If there is one, the first round asks whether to resume it;
on resume, the settled sections are settled branches of the tree.

When the PRD rests on facts about the outside world (market,
competitors, current platforms, regulations), start web-research
subagents now, each returning a digest. Your training may be out of date.
Per grilling, only the questions downstream of that research wait for it.

## 2. Grill

Run the interview by the grilling skill: a design tree worked in rounds,
each round the whole frontier, closed questions through AskUserQuestion
with your recommendation first, open questions in chat. The template
shapes the tree:

**Round one** usually holds, minus whatever the inputs already answer:

- **Template** (closed): `prd` or `prd-light`, recommended from their
  `description` and `not_for`. If the user wants to prove out an idea or
  try a running thing before committing, recommend a spike with
  `brainstorming-with-docs` instead of a PRD.
- **Stakes** (closed): hobby or personal · internal · pitch, publication
  or public launch · regulated or contracted. Stakes set how hard you
  push and how long the document gets.
- **Why, who, success** (open, in chat): why this PRD exists now, who it
  is for, what success looks like. These are the template's `inputs`:
  only the user knows them, so never default them.

Once the template is chosen, read it in full and create
`docs/prd/<slug>.md` (user preference for location wins): frontmatter
`title`, `template`, `version`, `status: draft`, `created`; the `#`
title, `## Table of Contents`, and every `##` heading. Say the path in
your next round.

**Then the sections are the branches.** Each `##` section in
`facilitation_order` is a branch; its comment is your brief for what the
branch must settle. A section's questions reach the frontier once what
they depend on is settled: users before journeys, journeys before
features, features before MVP scope and success metrics. The full PRD's
`stance` asks which entry fits (capability-first or journey-led); that
is an early closed question, and the answer changes which branches open
first.

How the template's moves become questions:

- **Journeys** are captured, not authored. Ask the user, in chat, to
  narrate a real session with a named protagonist. You structure it into
  UJ-N form and put it back as a closed question, with the drafted
  journey in a `preview`, for them to accept or correct.
- **Requirements** you draft from what's settled (journeys, the user's
  own words) and put to the user to accept: the drafted FRs go in a
  `preview`; options are accept, or what to change. Never put an FR in
  the document that the user didn't accept.
- **Optional sections** (`optional`) are closed questions: include or
  settle out, with your recommendation.
- **Include-when sections** are closed questions too: say what you found
  against the condition in the question text, and recommend.
- **MVP scope**: once the requirements are out, the template asks you to
  coach leanness. Make it a closed question: your recommended cut
  (named, with why) vs keeping the scope as is. The call is the user's.
- **Domain terms**: as domain-modeling says, challenge and sharpen them;
  a resolved term goes into `GLOSSARY.md` the same turn. The PRD's
  Glossary section only links and lists.
- **"I don't know"** is a fine answer. If the section can default
  safely at these stakes, write your best guess tagged
  `[ASSUMPTION: ...]`; otherwise it becomes an open question (OQ-N) with
  an owner.
- A concern no section covers gets its own section, via its own question.

**Write as you go.** When a round settles a section, write it into the
document before the next round, so the user sees the PRD grow. Strip the
section's template comment as you fill it.

Open each round with one line of progress: the document, how many
sections are settled of the facilitation order's total, and the next
open: `PRD · 6 of 24 settled · next: Non-goals, MVP scope`.

**Done** when the frontier is empty: every section filled, settled out
(with the reason), or holding a named open question; the template's
`done when` holds; and the user confirms the shared understanding.

## Writing rules

- **Nothing goes in as the user's that they did not say or accept.** Keep
  the user's own words for their claims and stories. A recommended answer
  the user accepted is theirs.
- **Assumptions.** Mark each unconfirmed inference where it lands as
  `[ASSUMPTION: ...]`, the only tag — your own knowledge and anything a
  review fix added included. A tag stays until the user accepts that
  item.
- **Stakes.** At hobby stakes, one line fills a section. Below regulated
  or contracted stakes, you may default and tag. At regulated or
  contracted stakes, ask instead, and invent no regulation, citation or
  threshold; an obligation the user did not name is an open question.
- **Ids.** Give stable ids to anything others will cite (UJ-N, FR-N,
  RULE-N, SM-N, OQ-N). Ids are global and never reused. Make each
  requirement testable and each scope edge explicit.
- **Names, owners and dates** come from the user, never the environment.
- **Diagrams.** Draw what a section's comment names from what is known;
  use Mermaid wherever a diagram beats prose.
- **Table of Contents** sits right after the title: a linked entry per
  `##` section, each with a short phrase of the key terms a reader would
  search for and the ids it covers. A reader or agent that reads only the
  top can judge the document.
- **Length** fits the stakes and the reader, within `word_ceiling`.
  Detail the level below should decide goes there (the technical design,
  or a narrower PRD), not here.

## 3. Review

Pick reviewers by stakes:

- **Hobby:** the rubric walker only, or none.
- **Otherwise:** the rubric walker and a fresh reader, plus any lens the
  document warrants (security, compliance). Put the reviewer list to the
  user as a closed question with your pick recommended.

Run them as parallel subagents on the document, each told the stakes and
altitude:

- **Rubric walker:** given `rubric.md` and the template's `done when`.
- **Fresh reader:** given only the document, asked what it would still
  need answered to build from it, ranked, nitpicks skipped; "nothing
  important left" is a fine answer. One round.

Lead with a one-sentence verdict and the three fixes that matter most.
Then grill the findings: critical and high ones as closed questions
(fix as proposed, discuss, defer to Open questions, ignore), your
recommendation first; summarize the rest. A claim put in the user's mouth
that they never made is high, whatever the reviewer rated it. Where
reviewers disagree, show both. Keep the fixes that make the document
right, not longer. A finding that would grow the agreed scope, or add
detail the level below should decide, is no fix here: recommend deferring
it to Open questions unless the user asks for it by name.

## 4. Finalize

Tell the user the sequence in one sentence, then run every step in order.

1. **Id check:** run `check_ids.py` on the document; fix every
   `undefined`, `duplicate` and `withdrawn` hit.
2. **Coverage:** for each input the user supplied, one subagent returns
   what the document dropped, above all tone and feel; surface the gaps.
   Skip at hobby stakes unless the user wants it.
3. **Review** (section 3).
4. **Triage** each `[ASSUMPTION]` tag and open question in one last
   grilling round: blockers (the reader cannot use the document with it
   open) get resolved; the rest are deferred, each with an owner and a
   revisit condition. Never re-raise one the user already deferred. Many
   blockers is itself a finding: say so.
5. **Polish:** structure before prose. Move any `[ASSUMPTION]` tags still
   standing out of the body into a closing `## Assumptions` section, one
   line each naming the id or section it affects, so the body reads
   clean. Polish adds no claim the user did not make. Count the body with
   `wc -w` (excluding frontmatter, table of contents and assumptions) and
   cut to the `word_ceiling`; if it still runs over, say by how much and
   what fitting would cost, and let the user decide.
6. **ToC:** rebuild it from the final headings, rerun `check_ids.py`, fix
   every `toc` hit. Make sure no template comment or template-only
   frontmatter key is left.
7. **Your read:** tell the user which sections were settled out and why
   and what polish changed, name the three decisions you are least
   confident about, then wait for their read. Apply their changes,
   repeating step 6 if headings moved.

## Close

Set `updated`, and `status: final` unless a blocking open question
remains; then it stays `draft` and you say what blocks it. Commit the PRD
together with any `GLOSSARY.md` and ADR changes from the session. Give
the path and name the next steps from the template's `next` comment,
usually `brainstorming-with-docs` to design the build, citing the PRD's
ids.

## Updating an existing PRD

Read it, its template and the inputs it names. Grill what changed and
why, scaled to the change (a patch without context becomes drift), and
put any conflict with a prior decision to the user before applying. Then
run Finalize. For a fundamental change, recommend a new PRD instead.
