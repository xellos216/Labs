# Optional ChatGPT Setup for Labs

## Role

ChatGPT may act as an interactive questioner, evidence reviewer, mechanism
explainer, and approved archive drafter. It is not the source of repository
state, proof that an experiment occurred, a substitute for learner work, or a
requirement for using Labs.

## Source Precedence

Provide or load [Labs Source and Git Policy](LABS_SOURCE_SYNC.md). Apply this
order:

```text
current committed repository file
>
current observed system output
>
focused branch or diff evidence
>
explicit handoff
>
accumulated chat context
```

Project Sources and model memory are cached context, not canonical state.
Uncommitted local work is invisible unless its file content or focused diff is
provided.

## Recommended Context

Keep the smallest stable context needed for the current work:

- `LABS_SOURCE_SYNC.md`
- `DESIGN_PRINCIPLES.md`
- `LABS_SESSION_RULES.md`
- `MARKDOWN_GENERATION_POLICY.md`
- `ROADMAP_INDEX.md`
- `SESSION_ARCHIVE_FORMAT.md`

Load the relevant roadmap README from its current committed lifecycle path.
Use [Roadmap Format](ROADMAP_FORMAT.md) only for curriculum changes and [Lab
Experiment Format](LAB_EXPERIMENT_TEMPLATE.md) only for a detailed experiment
record.

Do not treat ordinary session archives, handoffs, temporary notes, or progress
summaries as persistent authority.

## Conversation Boundary

Organize a conversation around one meaningful roadmap section or one coherent
workstream. A conversation may contain multiple closely related tasks.

Do not require:

- one new conversation for every task
- one conversation for an entire long milestone
- a coordinator conversation
- an end-of-session handoff

Create a separate conversation or branch when:

- troubleshooting materially diverges from the learning objective
- a driver or tool installation issue becomes its own problem
- repository corruption or maintenance is involved
- the issue requires an independent experiment
- the work no longer belongs to the current roadmap section

## Starting a Learning Conversation

Use a prompt like this, replacing the path with the actual active roadmap:

```text
Read ops/docs/LABS_SOURCE_SYNC.md, ops/docs/LABS_SESSION_RULES.md, and
ops/docs/ROADMAP_INDEX.md from the current committed repository. Then read
active/security_core/README.md. Determine progress only from committed session
files with status: completed. Confirm the next documented session and begin
with minimal setup and one question. Ask for my prediction when useful, then
let me perform the primary work. Review only actual output. Do not change the
roadmap sequence or generate an archive unless I explicitly approve it.
```

## Session Behavior

The assistant should:

- state the authorized scope and central question
- request a prediction when it improves learning
- let the learner perform primary terminal or browser work
- review actual commands and observed output
- give the smallest correction needed to continue
- separate evidence, interpretation, alternatives, and uncertainty
- keep the session to one coherent workstream and no more than five core tasks
- apply the roadmap's explicit completion criterion
- require explicit approval for a phase transition

It should not immediately provide a full solution unless the learner asks or
is blocked, fabricate output, infer completion from chat, add core tasks
silently, or alter roadmap sequence without an approved repository change.

## Archive Workflow

After actual completion, ask whether the learner wants the final archive. Only
after approval, use [Session Archive Format](SESSION_ARCHIVE_FORMAT.md) and the
[Korean template](../templates/session_archive_ko.md).

The archive must be Korean, use the real completion date, contain only observed
commands and results, separate evidence from interpretation, preserve useful
failed hypotheses, state limitations, and redact sensitive identifiers. It is
one file; do not create separate notes or QA files.

## Handoffs

Create a handoff only when cross-session transfer is genuinely useful. Include
only:

- what was verified
- what remains unresolved
- exact paths, commands, or evidence
- current branch and commit
- next bounded action

Verify the handoff against repository evidence at the start of the next
conversation.

## Private and Public Context

Do not upload private bug bounty data, undisclosed findings, credentials,
tokens, personal account data, unredacted captures, confidential source, or
scope-restricted target information as reusable project context. Keep real
private-program evidence in a separate private workspace and provide only
sanitized, necessary context.

## Maintenance Principle

Chat interfaces and context features may change. Preserve the workflow rather
than depending on a specific menu, connector, or persistent-memory behavior.
The current committed repository remains authoritative.
