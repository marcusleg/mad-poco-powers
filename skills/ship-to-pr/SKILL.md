---
name: ship-to-pr
description: "After a design is approved: write the implementation plan, implement it with subagent-driven development, check it against the PRD, and open a pull request with a concise, complete description. Use when the user says to write the plan and implement it, to ship it, or to open a PR for an approved design or for finished work."
---

# Ship to PR

Run everything between an approved design and a pull request the user
reviews, stopping only for PRD questions and, when it can't be worked
out, which branch is the base. Invoking this skill is the user's
standing instruction: "write the plan and implement it using
subagent-driven-development, open a PR I can review, keep the description
concise yet complete."

**Announce at start:** "I'm using ship-to-pr: plan → subagent-driven
implementation → PRD check → pull request."

## What the invocation authorizes

The superpowers skills this one chains each have a stop that the user has
already answered by invoking ship-to-pr. When you reach one, don't ask;
use the answer below and continue.

- **Plan review is waived.** `superpowers:writing-plans` ends by asking
  "Please review the plan. Does it capture what you want?" Don't ask it:
  the ship-to-pr invocation is the answer. Link the plan in one line and
  continue. The plan is in the PR for review.
- **Worktree consent is given.** `superpowers:using-git-worktrees` asks
  before creating a worktree (directly, or inside subagent-driven
  development's setup). Don't ask: the answer is yes. Honor a declared
  worktree-directory preference. If a worktree isn't possible (the
  sandbox denies it, or the user has said they don't want worktrees),
  create a feature branch in place with `git switch -c <branch>` before
  any edit. If you're already in a linked worktree, using-git-worktrees
  creates no branch; if that worktree is on the base branch, run
  `git switch -c <branch>` there. Never implement on the base branch.
- **Execution method is chosen:** subagent-driven
  (`superpowers:subagent-driven-development`), unless no subagent tool
  is available; then `superpowers:executing-plans`, and say so.
- **Integration is chosen:** push the feature branch and open a PR
  (option 2 of `superpowers:finishing-a-development-branch`). Don't
  present its menu. Pushing the feature branch is authorized. Never
  merge, never push the base branch, never force-push.

Everything else still stops as the superpowers skills say: failing tests
before the PR, destructive or security-sensitive actions, and a plan so
broken that every path forward is a guess. PRD questions (steps 2 and 4)
are asked when they come up.

## Preconditions

**Determine the base branch first.** Take it from the plan, the
conversation or the branch's upstream; if none says, ask once, before any
work. Pass it on when finishing-a-development-branch asks.

Then one of:

- **Approved spec** (architectural path): a written spec the user
  approved.
- **Approved in-chat design** (bounded path): a short design the user
  approved in this conversation.
- **Finished feature branch**: the user has already implemented the work
  on a branch that isn't the base branch and just wants the PR. Commit
  any uncommitted work on it, then skip to step 4.
- **Finished work on the base branch**: the work is done but sits on the
  base branch (uncommitted, committed but not pushed, or both). Move it
  to a feature branch, in this order, then skip to step 4:
  1. `git fetch`, and note whether `<base>` is ahead of `origin/<base>`.
  2. `git switch -c <branch>`. The new branch starts at HEAD, so it has
     the local commits, and it carries the uncommitted changes with it.
  3. Commit the uncommitted changes on the new branch.
  4. Only if `<base>` was ahead: ask whether to move the local `<base>`
     back to `origin/<base>`, then, still on the feature branch,
     `git branch -f <base> origin/<base>`. This doesn't touch the working
     tree. If the user declines, leave `<base>` as it is.

  Never use `git reset --hard` here.

If none applies, say so and offer `brainstorming-with-docs`. The approval
gates before this point are not waived.

The repo needs a git remote that accepts PRs and a working forge CLI
(`gh` for GitHub, `glab` for GitLab). Check this first; if it's missing,
say what's missing before any work starts.

## Steps

Create a task for each step on your path.

**Architectural path (approved spec):**

1. **Worktree, then plan.** Invoke `superpowers:using-git-worktrees` to
   create a worktree on a new feature branch off the base branch, and
   work inside it from here on. Then invoke `superpowers:writing-plans`
   for the approved spec, telling it the execution method is
   subagent-driven.
2. **PRD check on the plan.** If `docs/prd/` has a PRD, invoke
   `prd-check` on the plan. Its questions are asked now, before
   execution starts. Then commit the plan and any PRD amendment on the
   feature branch, not on the base branch.
3. **Implement.** Invoke `superpowers:subagent-driven-development` with
   the plan. Its setup finds the worktree from step 1 and uses it. Let it
   run to the end without check-ins.
   - **Before it deletes its plan workspace** after the final review, copy
     out of its ledger every `Ruling:` line and every parked finding.
     They go in the PR description.
   - **When it reaches "Use superpowers:finishing-a-development-branch",
     don't invoke it yet.** Continue with step 4; finishing comes in
     step 5.

**Bounded path (approved in-chat design):**

1. **Branch.** Invoke `superpowers:using-git-worktrees` to create a
   worktree on a new feature branch off the base branch. Never implement
   on the base branch.
2. **PRD check on the design** already happened during brainstorming;
   nothing to do here.
3. **Implement** with TDD (`superpowers:test-driven-development`) in that
   worktree, committing as you go, then run the full test suite. Note any
   decision the approved design didn't cover: it goes in the PR
   description like a ledger ruling.

**All paths, including finished work:**

4. **PRD check on the branch.** If `docs/prd/` has a PRD, invoke
   `prd-check` on the branch diff against the base branch. Ask now the
   questions collected during implementation. PRD amendments are
   committed on this branch.
   - **"Change the work to fit the PRD"** rulings: implement them now on
     this branch (TDD; for a non-trivial change, one implementer subagent
     and one reviewer subagent), commit, and run the full test suite
     again.
5. **Open the PR.** Invoke `superpowers:finishing-a-development-branch`
   with option 2 already chosen: it verifies the full test suite, pushes
   the feature branch, and creates the PR. Write the description below.
   If the spec or plan still isn't on the branch (fallback for a missed
   step 2 commit), commit it there so the PR can link it.
6. **Report** the PR URL and the two or three things the user should look
   at first. Stop. The worktree stays for review feedback.

## The PR description

The reader is the user, reviewing later, without this conversation.
Everything they need to decide "merge or push back" goes in; everything
they can click through to stays a link.

If the repo has a PR template (`.github/pull_request_template.md`,
`.gitlab/merge_request_templates/`, `docs/pull_request_template.md`),
fill that template, carrying the content below into its sections.
Otherwise use this shape, and **drop any section that would be empty**:

```markdown
<1-3 sentences: what this changes for the user of the product, and why.
Cite PRD ids (FR-N) where it realizes them.>

## Changes
- <behavior-level bullet, not a file list>
- <...>

## Decisions made during implementation
- <ruling: what was decided — why — cost if wrong>

## Deviations from the spec
- <what differs from the approved spec or design, and why>

## PRD, glossary and ADRs
- <the prd-check record: amendments, open questions added>
- <GLOSSARY.md terms added or changed; ADRs added>

## Review focus
- <where a mistake would hurt most; the riskiest file:line or decision>

## Testing
- <what was run and the result; what is not covered by tests and why>

## Follow-ups
- <parked findings and open questions, each one line>

Spec: <path> · Plan: <path>
```

On the bounded path there is no spec or plan file: replace the last line
with a `## Design` section holding the approved in-chat design, a few
lines at most. For a finished feature branch, drop the line.

Rules:

- **Concise:** one screen when possible (roughly 300 words). Each bullet
  is one line. If it's longer, the cause is usually a file list or
  restated spec; cut those, link instead.
- **Complete:** every ledger ruling, every deviation from the spec, every
  PRD amendment and every test gap is in, however small. A reviewer who
  finds a decision in the code that isn't in the description stops
  trusting the description.
- **Honest:** tests that failed, were skipped or don't exist are stated,
  not implied away.
- **No process narration** ("I used subagents", "the reviewer found…").
  The reader wants the result.
- **Title:** imperative, under 70 characters, saying the change, not the
  ticket.
- End with the attribution lines configured for this session, if any.
