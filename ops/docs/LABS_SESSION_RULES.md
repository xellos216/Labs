# Labs Session Rules

## Purpose

These rules define how interactive Curriculum V2 learning sessions are run.
Roadmap README files define what each session covers; this document defines how
the learner and assistant conduct it.

## Learner-Facing Flow

```text
Minimal Setup
↓
Question or Task
↓
Learner Prediction
↓
Learner Performs Primary Work
↓
Observed Output
↓
Review and Minimal Correction
↓
Mechanism Explanation
↓
Completion Decision
↓
Korean Session Archive
```

The archive step occurs only after the session is complete and the learner
explicitly requests or approves final generation.

## Minimal Setup

Establish only what is needed to begin:

- roadmap, phase, and session identifier
- central question and completion criterion
- authorized scope and stop conditions
- required local fixture, accounts, or capture point
- relevant environment differences

Do not front-load a full solution. Setup should make the learner's first task
safe and observable.

## Question and Prediction

Present one central question or bounded task. Request a prediction before
observation when the prediction will expose assumptions or make later evidence
more meaningful.

A useful prediction identifies an expected state, path, response, or trust
decision and gives a reason. Do not treat a wrong prediction as failure; use it
as a comparison point for the observed mechanism.

## Learner Performs the Primary Work

The learner performs the primary terminal, browser, packet-capture, request,
or analysis work. The assistant should not immediately provide the complete
solution unless the learner asks for it or is blocked after a genuine attempt.

The assistant may provide a minimal fixture or bounded command when setup
syntax is not the learning objective. It must still leave the core observation
and reasoning work to the learner.

## Observed Output

Review actual attempted commands, requests, responses, logs, captures, browser
state, or other observed output. Ask for the smallest additional evidence
needed to resolve an ambiguity.

Keep these categories distinct:

- observed evidence
- explanation supported by that evidence
- inference that still needs testing
- limitation or uncertainty

Never invent output, successful execution, environment state, or completion.

## Review and Minimal Correction

Review the learner's actual attempt. Identify the specific mismatch between
the task and the evidence, then provide the smallest correction that lets the
learner continue. Explain syntax only to the depth needed for the current
question.

Do not silently add a new concept, core task, or unrelated troubleshooting
branch. When a dependency problem becomes its own workstream, split it from the
learning session and preserve the current continuation point.

## Mechanism Explanation

After observation, connect the evidence to the mechanism:

```text
observed state or event
↓
responsible component and decision
↓
state, data, or trust-boundary transition
↓
reusable mental model
```

State which alternatives were rejected and which remain possible. Commands
are instruments; the explanation must remain useful with equivalent tools.

## Scope and Task Budget

A normal session has one central question, no more than five core tasks, one
explicit completion criterion, and tasks from one coherent workstream. Setup,
review, correction, completion review, and approved archive generation do not
increase the task count.

If work overruns, reduce or defer later tasks. Do not append work because a
phase lacks a convenient stopping point. Keep the roadmap-defined session
purpose and sequence stable unless the learner explicitly approves a
curriculum change.

## Completion Decision

Completion requires evidence that satisfies the session's explicit criterion.
The learner should be able to:

- identify the relevant observed evidence
- explain the mechanism in their own words
- distinguish evidence from inference
- reject or bound plausible alternative explanations
- state limitations and uncertainty
- apply the mental model to a nearby case

Do not infer completion from chat length, task count, earlier conversations,
or an assistant-generated summary. If evidence is incomplete, mark the session
as not complete or inconclusive and identify the next bounded action.

Phase transitions require explicit learner approval after the phase capstone
and completion evidence are reviewed. Do not silently advance a phase or
change roadmap order.

## Capstones

A phase capstone uses the same learner-facing flow but integrates several phase
concepts. It must require the learner to collect evidence, reconstruct a state,
path, or trust boundary, test alternative explanations, explain the mechanism,
and state limitations or uncertainty.

Use the same archive format as an ordinary session. Do not create a separate
review or answer file.

## Korean Session Archive

Generate one final Korean archive only when all of these are true:

1. The learner performed the primary work.
2. The relevant commands or tests and outputs were actually observed.
3. The completion criterion was reviewed explicitly.
4. The learner requested or approved archive generation.

Follow [Session Archive Format](SESSION_ARCHIVE_FORMAT.md) and
[`session_archive_ko.md`](../templates/session_archive_ko.md). Do not create a
draft or incomplete session file. Do not copy a raw transcript. Include only
the minimum raw evidence needed and redact sensitive identifiers.

## Conversation Boundary

Use one conversation for one meaningful roadmap section or one coherent
workstream. Closely related tasks may remain together. A conversation need not
map one-to-one to a session, and a long milestone need not remain in one
conversation.

Start a separate conversation or branch when:

- troubleshooting materially diverges from the learning objective
- driver or tool installation becomes its own problem
- repository corruption or maintenance is involved
- an independent experiment is required
- the work no longer belongs to the current roadmap section

A coordinator conversation and end-of-session handoff are optional, not
required.

## Handoffs

Create a handoff only when cross-session transfer is genuinely useful. Include
only:

- what was verified
- what remains unresolved
- exact paths, commands, or evidence
- current branch and commit
- next bounded action

Repository evidence takes precedence over any handoff or accumulated chat
context.

## Safety and Public Records

Keep practical work inside the roadmap's authorized lab boundary. Stop when
scope, ownership, regulatory permission, or publication safety is uncertain.
Private research evidence stays outside the public repository.

Before generating public Markdown, apply
[Markdown Generation Policy](MARKDOWN_GENERATION_POLICY.md), including
redaction and media review.
