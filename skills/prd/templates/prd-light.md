---
title: ""
template: prd-light
version: 1.1.0-mpp
description: "A short PRD with enough technical detail for a very simple project."
not_for: "Work with real stakes or several people building from it (`prd`); learning fast before committing (a spike in `brainstorming-with-docs`); a technical design for one feature (`brainstorming-with-docs`)."
facilitation_order: [vision, users-and-journeys, features-and-requirements, non-goals, technical-approach, success?, open-questions]
word_ceiling: "about 1,250 words; a project that needs more is a `prd`"
status: draft
created: ""
updated: ""
---

<!-- purpose: Requirements and enough technical direction to start building a very simple project from one document. -->

<!-- stance: Keep it light: drop a section that does not earn its place and never add one just because a full PRD has it. Technical choices are the user's, so ask only what changes the build. With no preference and nothing already built, suggest a starter project to build from, checked on the web for what is current. -->

<!-- inputs: What it is, who it is for and what is out; where it will be built and the platform if the sources do not say. -->

<!-- writing: FR ids are global. Domain nouns are used exactly as GLOSSARY.md defines them, and a term that resolves while writing goes into GLOSSARY.md (domain-modeling). Unlike a full PRD, technical decisions expensive to reverse belong here. -->

<!-- done when: Every FR can be checked, the scope edges are explicit, and a builder could start from this file alone. -->

<!-- next: Offer `brainstorming-with-docs` to design the build from this file; it cites the FR ids and then offers ship-to-pr or writing-plans. -->

# {title}

## Table of Contents

## Vision
<!-- A short paragraph: what this is, who it is for and why it is worth building. -->

## Users and journeys
<!-- Who uses it, and a sentence for each journey that matters. -->

## Features and requirements
<!-- By feature, nested FR-N: "[Actor] can [capability] [under conditions]", each with one checkable result. -->

## Non-goals
<!-- What this will not do, with a one-line reason where it matters. Once the features are out, if the list is more than a first version needs, say so, suggest what could wait for a later version and why, and let the user decide. -->

## Technical approach
<!-- Where it is built, platform, stack and any starter project, then data and storage, integrations, hosting and any other decision expensive to reverse. Flag the rest for the builder instead of choosing. Enough to start, not an architecture. -->

## Success
<!-- optional. One sentence on how the user will know it worked. -->

## Open questions
<!-- Each with who answers it. -->
