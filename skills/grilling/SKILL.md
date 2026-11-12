---
name: grilling
description: Grill the user relentlessly about a plan, decision, or idea, in rounds of questions asked through the AskUserQuestion tool. Use when the user wants to stress-test their thinking or uses any 'grill' trigger phrase. brainstorming-with-docs and prd run their interviews with it.
---

# Grilling

Interview the user relentlessly until you reach a shared understanding.
Map this as a **design tree**: every decision branches into the decisions
that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose
prerequisites are already settled: the questions you can ask _now_
without guessing at answers you haven't heard yet. Ask the whole frontier
in one round, each question with your recommended answer. Then wait for
the answers before the next round.

Each round reshapes the tree: settled decisions push the frontier outward
and unblock questions that depended on them. Recompute the frontier and
ask the next round. A question whose answer depends on another question
still open in this round belongs to a _later_ round, not this one.

## Asking a round

Sort the frontier into two kinds of question.

**Closed questions** — the answer is a choice between alternatives you
can name (approach A vs B, include this section or not, which term is
canonical). Ask them with the **AskUserQuestion** tool:

- Up to four questions per call. If the round has more, make successive
  calls, the most foundational first. Questions in one round are
  independent by definition, so the order is only for readability.
- Your recommended option goes first, with ` (Recommended)` at the end of
  its label. 2-4 options, each a real alternative; its description says
  the trade-off. Never pad with filler options to reach two.
- Each question stands alone: the user may see only the dialog, not what
  you wrote before it. Put the context it depends on (what you found,
  what was settled) into the question text itself.
- `header` is a 12-character chip naming the branch of the tree
  ("Auth", "Scope", "Storage").
- Use `multiSelect` when the choices are not mutually exclusive.
- Use `preview` when the user must compare concrete artifacts: interface
  sketches, code shapes, layouts, diagrams.
- The user can always choose "Other" and answer in their own words. That
  answer is the decision, as given; if it opens new branches, add them to
  the tree.

**Open questions** — purpose, motivation, what a word means to them, a
story only they can tell (a real session of a user, the incident that
prompted this). Listing options would put words in their mouth, so never
force these into the picker. After the round's AskUserQuestion calls
return, ask the open questions in chat, then end your turn and wait:

```
❓ **Q1** - **<question title>**: <question body>

➡️ <your recommended answer, or what you'd guess and why, if you have one>
```

Word each chat question so "yes" accepts your recommended answer.

Don't let the tool shape the interview. A question is closed because the
alternatives really are known, never because a picker is convenient. If
you catch yourself inventing options for something the user should
describe, it's an open question.

If AskUserQuestion is unavailable or the user declines it, ask the whole
round in chat in the format above.

## Facts vs decisions

Finding _facts_ is your job, never the user's. When a frontier question
needs a fact from the environment (code, files, docs, tools, the web),
dispatch a subagent with a fresh context window to find it; don't ask the
user for anything you could look up yourself. Don't block on it: a
running exploration is an unsettled prerequisite, so only the questions
downstream of it wait for the subagent to report; ask the rest of the
frontier now. The _decisions_ are the user's: put each to them and wait.

## Done

The session is done when the frontier is empty: every branch of the
design tree visited, nothing left silently assumed. Confirm with the user
(AskUserQuestion works well here) that you have reached a shared
understanding, and do not act on it until they do.
