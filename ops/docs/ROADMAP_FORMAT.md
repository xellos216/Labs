# Roadmap Format Specification

## Purpose

A roadmap README is an English curriculum specification. It defines stable
scope, sequence, evidence expectations, activation relationships, and
completion criteria. It is not a session archive, progress checklist, tool
manual, or collection of prospective notes.

## Required Top-Level Sections

Every roadmap README must contain these sections in a clear order:

1. Title
2. Status
3. Objective
4. Scope
5. Non-Goals
6. Learning Method
7. Lab Environment
8. Prerequisites
9. Phase Structure
10. Phases and Sessions
11. Completion Criteria
12. Relationship to Other Roadmaps
13. Final Outcome
14. Core Mental Model

The title is normally the document's single level-one heading. Use level-two
headings for the remaining required sections.

When AI materially affects how a roadmap is practiced, add the optional
`AI Assistance Boundary` section after `Learning Method`.

## Status

Use a lifecycle value that matches the roadmap directory:

```text
Active
Backlog
Archived
```

Status describes lifecycle, not document quality or session completion.

## Objective

State the capability the roadmap develops and the security-research role it
serves. Use measurable verbs such as explain, identify, distinguish, inspect,
observe, reconstruct, compare, validate, reject, and report.

Do not describe the repository as a broad collection of topics or repeat every
session title.

## Scope and Non-Goals

Scope defines the canonical concepts, systems, and trust boundaries owned by
the roadmap. Non-Goals prevent adjacent topics, tools, frameworks, or practice
platforms from silently becoming curriculum.

Apply one concept, one canonical home. Reference prerequisites from other
roadmaps instead of reteaching them. Tools are instruments, not roadmaps.

## Learning Method

Use the common evidence cycle:

```text
Scope
↓
Question
↓
Prediction or Hypothesis
↓
Observation or Controlled Test
↓
Evidence
↓
Explanation
↓
Record
```

State any roadmap-specific adaptation without contradicting
[Labs Session Rules](LABS_SESSION_RULES.md).

## AI Assistance Boundary

Include this optional section when AI materially affects roadmap practice.
Link to [AI-Assisted Learning and
Research](AI_ASSISTED_LEARNING_AND_RESEARCH.md) and define concisely:

- permitted assistance levels;
- work the learner must perform directly;
- evidence and human-verification requirements;
- roadmap-specific data-handling constraints; and
- whether the roadmap contains AI-specific sessions.

Do not repeat the canonical policy or make AI mandatory before its readiness
gate.

## Lab Environment

Define the minimum safe, authorized, and observable environment. Distinguish
required capabilities from optional tools. Allow equivalent instruments when
they preserve the session's question and evidence.

Do not embed private targets, real credentials, or environment-specific
identifiers. Practical work must be local, owned, isolated, explicitly
authorized, or performed on an authorized training platform.

## Prerequisites

Name required roadmap outcomes or equivalent evidence. Do not count tool
familiarity or removed historical sessions as curriculum credit. State
activation gates when hardware, accounts, scope, or lab isolation must be
verified.

## Phase Structure

Describe the stable phase pattern. A normal phase should move from bounded
questions through observation and controlled tests to an integration
capstone. Sessions normally contain one central question, no more than five
core tasks, and one explicit completion criterion.

Fixtures, services, tools, and exercises may adapt to the learner's actual
environment. Phase and session purposes remain stable.

## Phases and Sessions

Use stable identifiers and titles:

```text
Phase 01 — Phase Title
P01-S01 — Session Title
```

Within each phase, include:

- a goal
- the ordered session list
- a capstone as the final session
- an observable phase completion criterion

Descriptions are optional unless needed to disambiguate scope. Do not repeat
the same verbose explanation under the phase, session, and completion sections.

## Capstones

Every phase capstone must verify integration through:

- evidence collection
- reconstruction of a state, path, or trust boundary
- alternative-explanation checks
- mechanism-based explanation
- limitations and uncertainty

A capstone is a normal numbered session and uses the same completed-session
archive format.

## Completion Criteria

Define observable criteria for each phase and for the full roadmap. Completion
must depend on evidence-backed explanation, not attendance, task count,
checkboxes, platform ranking, bounty amount, or assistant assertion.

Progress comes from committed completed-session files. Do not create a separate
progress database or embed progress checkboxes in the roadmap.

## Relationship to Other Roadmaps

Explain:

- which canonical foundations this roadmap consumes
- which later roadmaps use its outcomes
- which adjacent concepts remain elsewhere
- whether it can act as a bounded secondary support roadmap

Do not duplicate another roadmap's sessions to make the relationship appear
self-contained.

## Final Outcome and Core Mental Model

The final outcome states what the learner can reconstruct, validate, reject,
or report after completing the roadmap. End with a concise text diagram that
shows the roadmap's central state, path, or trust-boundary model.

## File and Lifecycle Rules

Use these canonical paths:

```text
active/<roadmap>/README.md
backlog/<roadmap>/README.md
archive/<roadmap>/README.md
```

Use lowercase English snake_case for roadmap directories. Move the complete
roadmap directory when its lifecycle changes, then update
[Roadmap Index](ROADMAP_INDEX.md). Do not create duplicate roadmap documents,
redirects, or compatibility trees.

Do not create `phaseXX/` directories or session files while drafting a roadmap.
Create a phase directory only when its first session is actually completed and
the learner approves the Korean archive.

## Review Checklist

Before accepting a roadmap change, verify that:

- all required sections exist
- status and path agree
- canonical topic boundaries do not overlap another roadmap
- every phase ends in a capstone
- completion criteria are observable
- sessions are stable and coherently bounded
- authorization and public-documentation boundaries are explicit
- an AI Assistance Boundary exists when AI materially affects practice
- relationships reference prerequisites instead of duplicating them
- no future progress or observation is fabricated
